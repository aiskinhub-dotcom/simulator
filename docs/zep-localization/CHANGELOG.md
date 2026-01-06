# Zep 本地化 MVP 改动清单

## 背景

MiroFish 原本依赖 Zep Cloud 提供知识图谱和记忆服务。本次本地化的目标是：

1. **消除外部依赖**：不再需要 Zep Cloud API Key，完全本地运行
2. **降低成本**：无 API 调用费用，适合开发和演示
3. **数据自主**：所有数据存储在本地 Neo4j，便于调试和控制

## 技术方案

使用 `graphiti-core` + `Neo4j` 替代 Zep Cloud，实现相同的接口（`ZepClientAdapter`）。

核心挑战：
- graphiti-core 是异步库，Flask 是同步框架
- camel-ai 和 graphiti-core 对 neo4j driver 版本有冲突
- DashScope Embedding API 有批次大小限制

---

## 已实施改动

### 1. Graphiti 本地客户端

**文件**：`backend/app/services/zep_graphiti_impl.py`

**为什么做**：
- Zep Cloud 需要 API Key 和网络连接
- 需要一个本地替代方案，实现相同接口

**做了什么**：
- 实现 `GraphitiClient` 类，继承 `ZepClientAdapter` 接口
- 单后台线程 + 专用事件循环，解决 Flask 同步 + Graphiti 异步冲突
- `DashScopeEmbedderWrapper` 包装器，自动分块处理 Embedding 请求（批次 ≤10）

**有什么用**：
- 本地运行知识图谱服务，无需 Zep Cloud
- Flask 请求线程安全调用异步 Graphiti API
- 兼容 DashScope Embedding API 的批次限制

---

### 2. 双虚拟环境隔离

**文件**：
- `backend/.venv/` - 主环境（Flask + graphiti-core）
- `backend/.venv-simulation/` - 模拟环境（camel-ai/oasis）

**为什么做**：
- camel-ai 需要 `neo4j==5.23.0`
- graphiti-core 需要 `neo4j>=5.26.0`
- 同一环境无法同时满足

**做了什么**：
- 创建独立的 `.venv-simulation` 环境，使用 Python 3.11
- 模拟脚本通过 subprocess 运行，天然进程隔离

**有什么用**：
- 两个库可以各自使用兼容的 neo4j 版本
- 无需 fork 或修改任何依赖库

---

### 3. 模拟环境自动检测

**文件**：`backend/app/services/simulation_runner.py`

**为什么做**：
- 模拟脚本需要使用独立环境的 Python 解释器
- 需要自动检测，避免手动配置

**做了什么**：
- 添加 `_get_simulation_python()` 函数
- 优先级：环境变量 `SIMULATION_PYTHON` > `.venv-simulation/bin/python` > 当前 Python

**有什么用**：
- 自动使用正确的 Python 环境运行模拟
- 支持通过环境变量覆盖，便于部署

---

### 4. 前端状态修复

**文件**：`frontend/src/components/Step3Simulation.vue`

**为什么做**：
- 模拟失败时，前端状态显示不正确（一直显示运行中）

**做了什么**：
- 在 `fetchRunStatus()` 中添加 `failed` 状态检测
- 失败时停止轮询，显示正确状态

**有什么用**：
- 用户能看到模拟是否失败
- 不再无限轮询已结束的模拟

---

## 新增文件

| 文件 | 用途 |
|------|------|
| `LOCAL-STARTUP.md` | 本地版启动指南 |
| `docs/zep-localization/troubleshooting.md` | 问题排查指南 |
| `docs/zep-localization/TODO.md` | 待完善清单 |
| `docs/zep-localization/CHANGELOG.md` | 本文档 |
| `backend/.venv-simulation/` | 模拟独立环境 |

---

## 修改文件汇总

| 文件 | 改动类型 | 说明 |
|------|---------|------|
| `backend/app/services/zep_graphiti_impl.py` | 重构 | 单后台线程 + DashScope Wrapper |
| `backend/app/services/simulation_runner.py` | 新增函数 | `_get_simulation_python()` |
| `frontend/src/components/Step3Simulation.vue` | 修复 | failed 状态检测 |

---

## 验证方法

```bash
# 1. 检查环境隔离
cd backend
echo "主环境: $(.venv/bin/python -c 'import neo4j; print(neo4j.__version__)')"
echo "模拟环境: $(.venv-simulation/bin/python -c 'import neo4j; print(neo4j.__version__)')"
# 预期：主环境 6.x，模拟环境 5.23.0

# 2. 完整流程测试
# 前端：上传 PDF → 构建图谱 → 运行模拟 → 生成报告
```

---

## 已知限制

1. **串行执行**：所有 Graphiti 操作在单后台线程串行执行，高并发场景可能成为瓶颈
2. **Flask + 异步**：架构上的妥协，长期建议图谱服务独立化
3. **硬编码配置**：部分配置（超时、批次大小）硬编码在代码中

详见 [TODO.md](TODO.md) 了解后续优化计划。
