# Zep 本地化（Graphiti）实施评估与改进点

本文档用于评估当前“Zep Cloud → Graphiti + Neo4j 本地后端”的实现质量、可用性风险，并给出按优先级排序的改进建议（MVP 先跑通，再逐步对齐 full parity）。

## 1. 当前实现概览（已完成）

- ✅ 适配器与工厂
  - `backend/app/services/zep_adapter.py`：统一数据结构与接口
  - `backend/app/services/zep_cloud_impl.py`：保持 `zep-cloud` 行为的包装实现
  - `backend/app/services/zep_graphiti_impl.py`：Graphiti + Neo4j 本地实现（MVP）
  - `backend/app/services/zep_factory.py`：基于 `ZEP_BACKEND` 选择后端
- ✅ 调用方迁移
  - `graph_builder.py`、`zep_tools.py`、`zep_entity_reader.py`、`zep_graph_memory_updater.py`、`oasis_profile_generator.py` 已切到适配器
- ✅ 本地依赖
  - `docker-compose.local.yml`：提供 Neo4j 本地部署
  - `backend/pyproject.toml` 增加 `graphiti-core`、`neo4j`

## 2. 总体评价

- 架构选择（适配器模式 + 双后端可切换）是对的，面试加分点成立。
- 目前实现更接近“**代码结构到位**”，但“**本地后端端到端跑通**”仍有明显不确定性，主要集中在：
  - Graphiti 的 LLM/embedding 配置对齐
  - Graphiti 在 Neo4j 的真实落库 schema 与当前 Cypher 查询假设是否一致
  - `asyncio` 事件循环包装是否可靠
  - 搜索接口依赖 Graphiti 私有 API（`_search`），兼容性与稳定性风险偏高

## 3. 关键风险点（按严重程度）

### P0（可能直接导致无法运行/不可用）

1) **`OPENAI_*` 映射承诺与实现不一致**
- `.env.example` 写了“`OPENAI_API_KEY/OPENAI_BASE_URL` 会自动从 `LLM_*` 映射”，但后端（Flask）启动路径里没有做这个映射。
- 影响：Graphiti 默认读取 `OPENAI_*` 时，可能拿不到 key/base_url，导致 ingestion/search 直接失败。

2) **`_run_async()` 在已有事件循环场景不安全**
- `zep_graphiti_impl.py` 中在检测到 running loop 后仍调用 `asyncio.run(...)`。
- 影响：如果未来引入 async runtime（或某些环境已有 loop），会抛 `RuntimeError: asyncio.run() cannot be called from a running event loop`。

3) **Neo4j schema 假设可能与 Graphiti 实际落库不一致**
- 当前 `get_all_nodes/get_all_edges/get_node/get_node_edges` 使用硬编码 Cypher 查询 `(:Entity {group_id})`、关系属性 `uuid/fact/valid_at...`。
- 影响：如果 Graphiti 存储的 label/属性名不同，这些 API 会返回空结果或报错，导致前端图谱展示、实体读取、报告检索链路断裂。

4) **`search()` 依赖 Graphiti 私有 API（`self._graphiti._search`）**
- 私有 API 可能随版本变动；且 hybrid 配方需要 embedder/索引就绪。
- 影响：升级 `graphiti-core` 或配置不全时，搜索不可用。

### P1（可运行但质量/一致性差）

1) **Graphiti 后端的 `set_ontology()` 目前仅缓存**
- `graph_builder.set_ontology()` 在 graphiti 模式传入的是 list（原始 ontology），GraphitiClient 也仅缓存，不参与抽取或约束。
- 影响：实体类型/关系类型对齐会明显弱于 Zep Cloud；`zep_entity_reader` 的“按 label 过滤实体”可能失效（Graphiti 可能不产出与 ontology 一致的 label）。

2) **边的方向性/覆盖范围**
- `get_all_edges()` 查询写成了 `MATCH (n:Entity {group_id})-[r]->(m:Entity)`，只拿单向边，且限定两端均为 `:Entity`。
- 影响：如果 Graphiti 使用无向关系、不同 label、或边两端不是 `:Entity`，会漏边。

3) **依赖变成“强依赖”**
- `graphiti-core`、`neo4j` 被直接加入 `dependencies`，即使用户只用 cloud 也会安装。
- 影响：安装更慢、依赖冲突面更大；如果面试官在意“可选后端”，这点会被追问。

## 4. 建议改进（按优先级）

### P0 修复（建议尽快做，保证 MVP 可跑）

1) **在后端启动时做 `LLM_* → OPENAI_*` 映射（或显式传 client）**
- 选择 A（最简单）：在 `backend/app/config.py` 或 app factory 初始化阶段做：
  - 如果 `OPENAI_API_KEY` 未设置且 `LLM_API_KEY` 有值，则赋值
  - 同理 `OPENAI_BASE_URL`
- 选择 B（更干净）：在 `GraphitiClient` 初始化时显式注入 OpenAI-compatible client + embedder（避免环境变量散落）。

2) **重写 `_run_async()` 的事件循环处理**
- 目标：在任何情况下都不调用“嵌套的 `asyncio.run()`”。
- 建议实现：检测 running loop 时用 `asyncio.run_coroutine_threadsafe`（需要 loop 与线程策略），或统一用单独线程执行事件循环（维护一个后台 loop）。

3) **用真实 Graphiti schema 校验并调整 Cypher**
- 最小闭环：跑一轮 `add_episode` 后，在 Neo4j 里确认节点/边 label 与属性名，再把查询改成稳定版本。
- 建议：把“节点/边查询”封装成单独私有方法，并且加 fallback（查不到时打日志提示 schema mismatch）。

4) **为 `search()` 增加降级路径**
- 如果 Graphiti hybrid search 失败：至少提供一个 fallback（例如仅做 Neo4j 关键词查询或简单 BM25）。
- 目的：MVP 先保证 ReportAgent 不会因为 search 崩掉。

### P1 优化（提升一致性与工程质量）

1) **把 Graphiti 依赖改成可选（extras 或条件安装）**
- Python 依赖层面可用 `optional-dependencies` / extras；文档里明确 `ZEP_BACKEND=graphiti` 才需要安装。

2) **明确 Graphiti 的实体/边如何与 MiroFish ontology 对齐**
- MVP：把 ontology 文本注入到 episode 的 source_description/prompt（至少引导抽取）。
- Full parity：再考虑类型映射、约束、或在 Neo4j 上做标签/属性规范化。

3) **连接生命周期管理**
- `zep_factory.get_zep_client()` 是全局单例，但关闭策略依赖 `__del__`（不可靠）。
- 建议：在 Flask app teardown / atexit 钩子里显式调用 `close()`。

## 5. 建议的验证清单（面试前）

> 目标：用 `ZEP_BACKEND=graphiti` 跑通 1 次端到端，并记录截图/录屏。

- 启动 Neo4j：`docker-compose -f docker-compose.local.yml up -d`
- 启动后端（确保 LLM/OPENAI env 可用）
- Step1：上传文档 → 生成 ontology → build graph（GraphPanel 能看到 nodes/edges）
- Step2：entities/profiles/config 能生成（允许数量/类型与 cloud 不同，但不能报错）
- Step4：生成报告能走完（search 至少能返回一些内容）

## 6. Full parity 方向（后续里程碑）

- Ontology 映射：实体/关系类型与标签对齐
- Temporal 字段：`valid_at/invalid_at/expired_at` 等语义对齐
- Search 行为：scope/limit/reranker 对齐，结果结构更接近 `zep-cloud`
- Graph memory updater：模拟事件写回图谱并可检索

