# Zep 本地化实施

将 MiroFish 的知识图谱后端从 Zep Cloud 迁移到本地 Graphiti + Neo4j 方案。

## 背景

MiroFish 原依赖 Zep Cloud 作为知识图谱服务，为支持本地部署需求，实现了双后端架构：

- **Zep Cloud**：原有云服务，适合快速开发
- **Graphiti + Neo4j**：本地部署方案，完全开源

## 架构概览

```
┌─────────────────────────────────────┐
│        MiroFish 业务代码            │
│   (graph_builder, zep_tools, ...)   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│      ZepClientAdapter (适配层)       │
│         统一 API 接口               │
└──────────────┬──────────────────────┘
               │
      ┌────────┴────────┐
      ▼                 ▼
┌───────────┐    ┌─────────────────┐
│ Zep Cloud │    │ Graphiti Local  │
│ (云服务)   │    │ Neo4j + LLM     │
└───────────┘    └─────────────────┘
```

## 快速开始

### 使用 Graphiti 本地后端

```bash
# 1. 启动 Neo4j
docker-compose -f docker-compose.local.yml up -d

# 2. 等待服务就绪
docker-compose -f docker-compose.local.yml ps

# 3. 设置环境变量
export ZEP_BACKEND=graphiti
export NEO4J_URI=bolt://localhost:7687
export NEO4J_USER=neo4j
export NEO4J_PASSWORD=password

# 4. 启动后端
cd backend && uv run python -m flask run
```

### 使用 Zep Cloud 后端

```bash
# 设置环境变量
export ZEP_BACKEND=cloud  # 或不设置，默认 cloud
export ZEP_API_KEY=your_api_key

# 启动后端
cd backend && uv run python -m flask run
```

## 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `ZEP_BACKEND` | 后端选择：`cloud` 或 `graphiti` | `cloud` |
| `ZEP_API_KEY` | Zep Cloud API 密钥（cloud 模式必填） | - |
| `NEO4J_URI` | Neo4j 连接地址 | `bolt://localhost:7687` |
| `NEO4J_USER` | Neo4j 用户名 | `neo4j` |
| `NEO4J_PASSWORD` | Neo4j 密码 | `password` |

## 文档目录

- [架构设计](./architecture.md) - 适配器模式设计、文件清单、API 映射
- [迁移指南](./migration-guide.md) - 从 Zep Cloud 迁移到 Graphiti 的步骤

## 技术亮点

1. **适配器模式**：业务代码无感知切换后端
2. **配置驱动**：通过环境变量选择后端，无需改代码
3. **Docker 一键部署**：Neo4j 容器化，开箱即用
4. **向后兼容**：保留 Zep Cloud 支持，可随时切回
