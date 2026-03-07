# CoPaw 项目文档

> 欢迎来到 CoPaw！本文档将帮助您快速了解项目、安装部署并开始参与开发。

## 📚 文档目录

| 文档 | 说明 |
|------|------|
| [01-安装指南](./01-installation.md) | 环境准备、安装方式详解 |
| [02-快速开始](./02-quick-start.md) | 三步启动您的第一个 AI 助手 |
| [03-项目架构](./03-architecture.md) | 技术栈、模块划分、核心流程 |
| [04-配置指南](./04-configuration.md) | 模型配置、渠道配置、技能配置 |
| [05-开发指南](./05-development.md) | 本地开发、调试、测试、贡献代码 |
| [06-常见问题](./06-faq.md) | 安装、运行、开发常见问题解答 |

---

## 🎯 项目简介

**CoPaw** 是一个开源的个人 AI 助手，具有以下特点：

- **多渠道接入** - 支持 DingTalk、Feishu、QQ、Discord、iMessage、Telegram 等多种聊天平台
- **本地部署** - 数据和任务都在您自己的机器上运行，隐私可控
- **技能扩展** - 通过 Skills（技能）扩展功能，支持自定义开发
- **本地模型** - 支持 llama.cpp、MLX、Ollama 等本地大模型，无需 API Key
- **定时任务** - 内置 Cron 支持，可设置定时提醒和自动化任务

### 项目定位

```
CoPaw = Co Personal Agent Workstation（个人智能体工作站）
```

CoPaw 不仅是工具，更是您的数字生活伙伴 —— 一只随时待命的"小爪子"🐾

---

## 🛠️ 技术栈概览

```
┌─────────────────────────────────────────────────────────────┐
│                      CoPaw 技术架构                          │
├─────────────────────────────────────────────────────────────┤
│  前端 (Console)     │  React 18 + TypeScript + Vite + Ant Design  │
│  后端 (Backend)     │  Python 3.10+ + FastAPI + AgentScope        │
│  网站 (Website)     │  React + Vite (静态文档站)                  │
│  模型支持           │  OpenAI / DashScope / Ollama / llama.cpp    │
│  存储               │  JSON 文件存储 (无需数据库)                  │
└─────────────────────────────────────────────────────────────┘
```

### 核心依赖

**Python 后端：**
- `fastapi` - Web 框架
- `agentscope` - Agent 框架核心
- `agentscope-runtime` - Agent 运行时
- `uvicorn` - ASGI 服务器
- `apscheduler` - 定时任务调度

**前端 Console：**
- `react` + `react-dom` - UI 框架
- `antd` - 组件库
- `react-router-dom` - 路由
- `i18next` - 国际化

---

## 📁 项目结构

```
CoPaw/
├── src/copaw/              # Python 后端源码
│   ├── agents/             # Agent 逻辑、工具、技能
│   │   ├── skills/         # 内置技能 (SKILL.md 模式)
│   │   └── tools/          # Agent 工具
│   ├── app/
│   │   ├── routers/        # FastAPI 路由处理
│   │   ├── runner/         # Session/Agent 运行时
│   │   ├── channels/       # 渠道实现 (DingTalk, QQ, etc.)
│   │   └── mcp/            # MCP 客户端管理
│   ├── cli/                # CLI 命令 (copaw init, app, etc.)
│   ├── config/             # 配置处理
│   ├── local_models/       # 本地模型后端 (llamacpp, mlx)
│   ├── providers/          # 模型提供商注册和管理
│   └── utils/              # 共享工具
│
├── console/                # React 前端
│   ├── src/
│   │   ├── api/            # API 客户端和类型定义
│   │   ├── components/     # 可复用组件
│   │   ├── layouts/        # 页面布局
│   │   └── pages/          # 路由页面
│   └── package.json
│
├── website/                # 静态文档站
├── tests/                  # Python 测试
└── mydoc/                  # 本文档目录 (中文指南)
```

---

## 🚀 快速开始

### 方式一：pip 安装（推荐）

```bash
pip install copaw
copaw init --defaults
copaw app
```

然后打开浏览器访问 **http://127.0.0.1:8088/**

### 方式二：从源码安装

```bash
git clone https://github.com/agentscope-ai/CoPaw.git
cd CoPaw
pip install -e ".[dev]"

# 构建前端
cd console && npm ci && npm run build && cd ..

# 初始化并启动
copaw init --defaults
copaw app
```

---

## 📖 下一步

1. **完成安装** - 参考 [安装指南](./01-installation.md) 了解详细安装方式
2. **配置模型** - 参考 [配置指南](./04-configuration.md) 配置 API Key 或本地模型
3. **了解架构** - 参考 [项目架构](./03-architecture.md) 理解系统设计
4. **参与开发** - 参考 [开发指南](./05-development.md) 开始贡献代码

---

## 🔗 相关链接

- **GitHub 仓库**: https://github.com/agentscope-ai/CoPaw
- **官方文档**: https://copaw.agentscope.io/
- **问题反馈**: https://github.com/agentscope-ai/CoPaw/issues
- **社区讨论**: https://github.com/agentscope-ai/CoPaw/discussions

---

## 📄 许可证

CoPaw 采用 [Apache License 2.0](../LICENSE) 开源协议。