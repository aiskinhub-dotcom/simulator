<div align="center">

<img src="./static/image/MiroFish_logo_compressed.jpeg" alt="MiroFish Logo" width="75%"/>

简洁通用的群体智能引擎，预测万物
</br>
<em>A Simple and Universal Swarm Intelligence Engine, Predicting Anything</em>

<a href="https://www.shanda.com/" target="_blank"><img src="./static/image/shanda_logo.png" alt="tt-a1i/MiroFish-local | Shanda" height="40"/></a>

[![GitHub Stars](https://img.shields.io/github/stars/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/stargazers)
[![GitHub Watchers](https://img.shields.io/github/watchers/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/watchers)
[![GitHub Forks](https://img.shields.io/github/forks/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/network)
[![GitHub Issues](https://img.shields.io/github/issues/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/pulls)

[![GitHub License](https://img.shields.io/github/license/tt-a1i/MiroFish-local?style=flat-square)](https://github.com/tt-a1i/MiroFish-local/blob/main/LICENSE)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/tt-a1i/MiroFish-local)
[![Version](https://img.shields.io/badge/version-v0.1.0-green.svg?style=flat-square)](https://github.com/tt-a1i/MiroFish-local)
[![Python](https://img.shields.io/badge/Python-3.11+-blue?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/Docker-支持-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)

[English](./README-EN.md) | [中文文档](./README.md)

</div>

## ⚡ 项目概述

**MiroFish** 是一款基于多智能体技术的新一代 AI 预测引擎。通过提取现实世界的种子信息（如突发新闻、政策草案、金融信号），自动构建出高保真的平行数字世界。在此空间内，成千上万个具备独立人格、长期记忆与行为逻辑的智能体进行自由交互与社会演化。你可透过「上帝视角」动态注入变量，精准推演未来走向——**让未来在数字沙盘中预演，助决策在百战模拟后胜出**。

> 你只需：上传种子材料（数据分析报告或者有趣的小说故事），并用自然语言描述预测需求</br>
> MiroFish 将返回：一份详尽的预测报告，以及一个可深度交互的高保真数字世界

### 我们的愿景

MiroFish 致力于打造映射现实的群体智能镜像，通过捕捉个体互动引发的群体涌现，突破传统预测的局限：

- **于宏观**：我们是决策者的预演实验室，让政策与公关在零风险中试错
- **于微观**：我们是个人用户的创意沙盘，无论是推演小说结局还是探索脑洞，皆可有趣、好玩、触手可及

从严肃预测到趣味仿真，我们让每一个如果都能看见结果，让预测万物成为可能。

### 核心亮点

- 🧠 **多智能体群体智能仿真** — 成千上万个独立人格智能体自由交互，涌现真实社会动态
- 🌐 **高保真平行数字世界构建** — 基于 GraphRAG 自动提取实体关系，一键构建仿真环境
- 📊 **自动预测报告生成** — ReportAgent 深度分析模拟结果，输出结构化预测报告
- 💬 **与仿真世界深度交互** — 与模拟世界中的任意角色对话，探索平行可能性
- 🔧 **Cloud / 本地双模式部署** — 支持 Zep Cloud 快速上手，也支持 Graphiti + Neo4j 完全本地化

## 🎬 演示视频

<div align="center">
<a href="https://www.bilibili.com/video/BV1VYBsBHEMY/" target="_blank"><img src="./static/image/武大模拟演示封面.png" alt="MiroFish Demo Video" width="75%"/></a>

点击图片查看使用微舆BettaFish生成的《武大舆情报告》进行预测的完整演示视频
</div>

> 红楼梦结局模拟推演演示视频、金融方向预测示例演示视频等陆续更新中...

## 🖼️ UI 截图

<div align="center">
<!-- UI截图即将更新，敬请期待 -->
<p><em>🖼️ UI 截图即将更新...</em></p>
</div>

## 🏗️ 系统架构

```mermaid
flowchart LR
    A["🌱 种子输入"] --> B["🕸️ 图谱构建\n(GraphRAG)"]
    B --> C["🏠 环境搭建\n(人设生成)"]
    C --> D["⚙️ 并行模拟\n(OASIS 引擎)"]
    D --> E["📊 报告生成\n(ReportAgent)"]
    E --> F["💬 深度交互"]
```

| 模块 | 说明 |
|------|------|
| **种子输入** | 接收用户上传的种子材料（新闻、报告、小说等），解析预测需求 |
| **图谱构建** | 基于 GraphRAG 提取实体关系，注入个体与群体记忆，构建知识图谱 |
| **环境搭建** | 自动生成智能体人设，由环境配置 Agent 注入仿真参数 |
| **并行模拟** | OASIS 引擎驱动大规模智能体并行交互，动态更新时序记忆 |
| **报告生成** | ReportAgent 使用丰富工具集与模拟后环境深度交互，生成预测报告 |
| **深度交互** | 用户可与模拟世界中的任意角色对话，或与 ReportAgent 进一步探讨 |

## 🔄 工作流程

1. **图谱构建** — 现实种子提取 & 个体与群体记忆注入 & GraphRAG 构建。系统从用户上传的种子材料中抽取关键实体与关系，构建结构化知识图谱，为仿真世界奠定信息基础。

2. **环境搭建** — 实体关系抽取 & 人设生成 & 环境配置 Agent 注入仿真参数。基于图谱自动生成具有独立人格和背景故事的智能体，配置社交网络拓扑与初始行为参数。

3. **开始模拟** — 双平台并行模拟 & 自动解析预测需求 & 动态更新时序记忆。OASIS 引擎驱动智能体在仿真环境中自由交互，实时记录行为轨迹与态度变化。

4. **报告生成** — ReportAgent 拥有丰富的工具集与模拟后环境进行深度交互。汇聚仿真数据，从多维度分析群体行为模式，输出结构化预测报告。

5. **深度互动** — 与模拟世界中的任意角色进行对话 & 与 ReportAgent 进行对话。用户可随时介入仿真世界，探索不同决策路径下的演化结果。

## 🎯 应用场景

| 场景 | 描述 |
|------|------|
| 🗞️ **舆情预测与危机公关预演** | 模拟突发事件在社交网络中的传播路径，预判舆论走向，提前制定应对方案 |
| 💹 **金融市场情绪推演** | 构建投资者群体行为模型，模拟市场对政策、事件的反应，辅助投资决策 |
| 🏛️ **政策影响评估** | 在虚拟社会中预演政策实施效果，观察不同群体的行为反馈与社会影响 |
| ✍️ **创意实验** | 小说结局推演、历史事件重演、脑洞验证——让想象力在数字世界中自由奔跑 |
| 🔬 **社会科学研究模拟** | 为社会学、传播学、行为经济学等学科提供大规模可控实验平台 |

## 🚀 快速开始

### 前置要求

> 注：MiroFish 在 Mac 环境下完成开发与测试，Windows 兼容性未知，测试中

| 工具 | 版本要求 | 说明 | 安装检查 |
|------|---------|------|---------|
| **Python** | 3.11+ | 后端运行环境 | `python --version` |
| **Node.js** | 18+ | 前端运行环境，包含 npm | `node -v` |
| **uv** | 最新版 | Python 包管理器 | `uv --version` |
| **Docker** *(可选)* | 最新版 | 本地模式启动 Neo4j 等依赖服务 | `docker --version` |

### 1. 配置环境变量

```bash
# 复制示例配置文件
cp .env.example .env

# 编辑 .env 文件，填入必要的 API 密钥
```

环境变量分为以下几组：

#### LLM API 配置（必需）

支持 OpenAI SDK 格式的任意 LLM。推荐使用阿里百炼平台 qwen-plus 模型。

> 注意：模拟消耗较大，建议先进行小于 40 轮的模拟尝试。

```env
LLM_API_KEY=your_api_key
LLM_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
LLM_MODEL_NAME=qwen-plus
```

#### Zep 后端选择

通过 `ZEP_BACKEND` 切换记忆后端模式：

| 值 | 模式 | 说明 |
|---|------|------|
| `cloud` | Zep Cloud（默认） | 零配置，每月免费额度即可上手 |
| `graphiti` | 本地 Graphiti + Neo4j | 完全本地化，数据不出域 |

```env
ZEP_BACKEND=cloud
```

#### Zep Cloud 配置（`ZEP_BACKEND=cloud` 时必需）

免费注册：https://app.getzep.com/

```env
ZEP_API_KEY=your_zep_api_key
```

#### Graphiti / Neo4j 本地配置（`ZEP_BACKEND=graphiti` 时必需）

```env
NEO4J_URI=bolt://localhost:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=password

# Graphiti 使用的 LLM 模型（推荐显式配置）
GRAPHITI_LLM_MODEL=qwen3-max
GRAPHITI_EMBEDDING_MODEL=text-embedding-v4
```

> `OPENAI_API_KEY` / `OPENAI_BASE_URL` 会自动从 `LLM_API_KEY` / `LLM_BASE_URL` 映射，无需重复配置。如需单独指定 Graphiti 使用的 LLM，可显式设置 `OPENAI_API_KEY` 和 `OPENAI_BASE_URL`。

#### 加速 LLM 配置（可选）

可配置独立的加速 LLM 用于提升特定环节的处理速度：

```env
LLM_BOOST_API_KEY=your_boost_api_key
LLM_BOOST_BASE_URL=https://another-api-provider.com/v1
LLM_BOOST_MODEL_NAME=gpt-4o-mini
```

### 2. 启动依赖服务（可选，仅本地模式）

如果选择 `ZEP_BACKEND=graphiti`，需要先启动 Neo4j 数据库：

```bash
# 使用 Docker Compose 启动依赖服务（Neo4j）
docker-compose -f docker-compose.local.yml up -d
```

### 3. 安装依赖

```bash
# 一键安装所有依赖（根目录 + 前端 + 后端）
npm run setup:all
```

或者分步安装：

```bash
# 安装 Node 依赖（根目录 + 前端）
npm run setup

# 安装 Python 依赖（自动创建虚拟环境）
npm run setup:backend
```

### 4. 启动服务

```bash
# 同时启动前后端（在项目根目录执行）
npm run dev
```

**服务地址：**
- 前端：`http://localhost:3000`
- 后端 API：`http://localhost:5001`

**单独启动：**

```bash
npm run backend   # 仅启动后端
npm run frontend  # 仅启动前端
```

## 💻 硬件需求

MiroFish 本身是 LLM 调用型应用，计算主要依赖远端 LLM API，本地资源需求较低。

| 配置 | CPU | 内存 | 磁盘 | GPU |
|------|-----|------|------|-----|
| **最低配置** | 4 核 | 8 GB | 10 GB | 不需要 |
| **推荐配置** | 8 核 | 16 GB | 20 GB | 不需要 |

> 说明：GPU 仅在本地部署 LLM（如使用 Ollama 等工具运行本地模型）时需要。使用云端 LLM API 无需 GPU。

## 🤝 贡献指南

欢迎提交 Pull Request 和 Issue！无论是 Bug 反馈、功能建议还是文档改进，我们都非常感谢社区的每一份贡献。

> 贡献指南正在完善中，敬请期待。

## 📄 致谢

**MiroFish 得到了盛大集团的战略支持和孵化！**

MiroFish 的核心仿真引擎由 **[OASIS](https://github.com/camel-ai/oasis)** 驱动。OASIS 是由 [CAMEL-AI](https://github.com/camel-ai) 团队开发的高性能社交媒体模拟框架，支持百万级智能体交互仿真，为 MiroFish 的群体智能涌现提供了坚实的技术基础。我们衷心感谢 CAMEL-AI 团队的开源贡献！

## 📈 项目统计

<a href="https://www.star-history.com/#tt-a1i/MiroFish-local&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=tt-a1i/MiroFish-local&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=tt-a1i/MiroFish-local&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=tt-a1i/MiroFish-local&type=date&legend=top-left" />
 </picture>
</a>
