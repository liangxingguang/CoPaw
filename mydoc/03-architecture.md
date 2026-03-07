# 项目架构

本文档详细介绍 CoPaw 的技术架构、模块划分和核心流程，帮助您理解系统设计。

---

## 🏗️ 整体架构

```
┌─────────────────────────────────────────────────────────────────────┐
│                          用户界面层                                   │
├─────────────────────────────────────────────────────────────────────┤
│  Web Console (React)  │  DingTalk │  Feishu │  QQ │  Discord │ ...  │
├─────────────────────────────────────────────────────────────────────┤
│                          API 层 (FastAPI)                            │
├─────────────────────────────────────────────────────────────────────┤
│                        Agent 运行时 (AgentRunner)                     │
├─────────────────────────────────────────────────────────────────────┤
│  Skills  │  Tools  │  Memory  │  MCP Clients  │  Channels            │
├─────────────────────────────────────────────────────────────────────┤
│                        模型层 (LLM Providers)                         │
├─────────────────────────────────────────────────────────────────────┤
│  DashScope │  OpenAI │  Ollama │  llama.cpp │  MLX │  Custom        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔧 技术栈详解

### 后端 (Python)

| 组件 | 技术 | 说明 |
|------|------|------|
| **Web 框架** | FastAPI | 高性能异步 Web 框架 |
| **Agent 框架** | AgentScope | 阿里开源的 Agent 开发框架 |
| **运行时** | agentscope-runtime | Agent 执行环境 |
| **ASGI 服务器** | Uvicorn | 异步 HTTP 服务器 |
| **定时任务** | APScheduler | 任务调度框架 |
| **记忆系统** | ReMe | 长期记忆管理 |

### 前端 (Console)

| 组件 | 技术 | 说明 |
|------|------|------|
| **框架** | React 18 | UI 框架 |
| **语言** | TypeScript | 类型安全 |
| **构建工具** | Vite | 快速构建 |
| **组件库** | Ant Design | UI 组件 |
| **样式** | Less + CSS Modules | 样式方案 |
| **国际化** | i18next | 多语言支持 |
| **路由** | React Router | SPA 路由 |

---

## 📁 目录结构详解

```
src/copaw/
├── agents/                    # Agent 核心模块
│   ├── skills/               # 内置技能
│   │   ├── cron/            # 定时任务技能
│   │   ├── file_reader/     # 文件读取技能
│   │   ├── pdf/             # PDF 处理技能
│   │   ├── docx/            # Word 处理技能
│   │   ├── xlsx/            # Excel 处理技能
│   │   ├── pptx/            # PPT 处理技能
│   │   ├── news/            # 新闻摘要技能
│   │   └── browser_visible/ # 浏览器自动化技能
│   ├── tools/               # Agent 工具
│   │   ├── shell.py        # Shell 命令执行
│   │   ├── file_io.py      # 文件读写
│   │   ├── file_search.py  # 文件搜索
│   │   ├── memory_search.py# 记忆搜索
│   │   └── browser_*.py    # 浏览器相关工具
│   ├── skills_manager.py    # 技能管理器
│   └── skills_hub.py        # 技能中心
│
├── app/                      # 应用层
│   ├── routers/             # API 路由
│   │   ├── agent.py        # Agent 相关 API
│   │   ├── chat.py         # 聊天 API
│   │   ├── providers.py    # 模型提供商 API
│   │   ├── skills.py       # 技能 API
│   │   ├── mcp.py          # MCP API
│   │   ├── envs.py         # 环境变量 API
│   │   └── voice.py        # 语音 API
│   ├── runner/              # Agent 运行时
│   │   ├── runner.py       # 核心运行器
│   │   ├── session.py      # 会话管理
│   │   ├── manager.py      # 聊天管理器
│   │   ├── api.py          # Runner API
│   │   └── repo/           # 数据存储
│   ├── channels/            # 渠道实现
│   │   ├── base.py         # 渠道基类
│   │   ├── dingtalk.py     # DingTalk 渠道
│   │   ├── feishu.py       # Feishu 渠道
│   │   ├── qq.py           # QQ 渠道
│   │   ├── discord.py      # Discord 渠道
│   │   └── telegram.py     # Telegram 渠道
│   ├── mcp/                 # MCP 客户端
│   │   ├── manager.py      # MCP 管理器
│   │   └── watcher.py      # MCP 热加载
│   ├── crons/               # 定时任务
│   │   ├── manager.py      # Cron 管理器
│   │   └── repo/           # 任务存储
│   └── _app.py              # FastAPI 应用入口
│
├── cli/                      # 命令行工具
│   ├── main.py              # CLI 入口
│   ├── init_cmd.py          # init 命令
│   ├── app_cmd.py           # app 命令
│   ├── channels_cmd.py      # channels 命令
│   ├── cron_cmd.py          # cron 命令
│   ├── skills_cmd.py        # skills 命令
│   ├── providers_cmd.py     # models 命令
│   └── env_cmd.py           # env 命令
│
├── providers/                # 模型提供商
│   ├── registry.py          # 提供商注册表
│   ├── store.py             # 配置存储
│   ├── models.py            # 数据模型
│   └── ollama_manager.py    # Ollama 管理
│
├── local_models/             # 本地模型
│   ├── manager.py           # 模型管理
│   ├── factory.py           # 模型工厂
│   ├── chat_model.py        # 聊天模型
│   └── backends/            # 后端实现
│       ├── llamacpp_backend.py
│       └── mlx_backend.py
│
├── config/                   # 配置管理
│   ├── config.py            # 配置处理
│   ├── watcher.py           # 配置热加载
│   └── utils.py             # 配置工具
│
├── envs/                     # 环境变量
│   └── store.py             # 环境变量存储
│
├── utils/                    # 工具函数
│   └── logging.py           # 日志配置
│
├── constant.py               # 常量定义
└── __init__.py               # 包入口
```

---

## 🔄 核心流程

### 1. 消息处理流程

```
用户消息 → Channel → ChannelManager → AgentRunner → Agent → LLM → 响应
                                                    ↓
                                              Tools/Skills
                                                    ↓
                                              Memory
```

**详细步骤：**

1. **消息接收**：Channel（如 DingTalk）接收用户消息
2. **消息转换**：将平台消息转换为统一的 `content_parts` 格式
3. **任务分发**：ChannelManager 将消息放入队列
4. **Agent 处理**：AgentRunner 调用 Agent 处理消息
5. **工具调用**：Agent 可能调用 Tools 或 Skills
6. **模型推理**：调用 LLM 进行推理
7. **记忆更新**：更新短期和长期记忆
8. **响应发送**：将响应通过 Channel 发送给用户

### 2. Agent 执行流程

```python
# 简化的 Agent 执行流程
async def process_message(request: AgentRequest):
    # 1. 加载技能和工具
    skills = load_skills()
    tools = load_tools()
    
    # 2. 构建上下文
    context = build_context(request, memory)
    
    # 3. 调用 LLM
    response = await llm.chat(context, tools=tools)
    
    # 4. 处理工具调用
    while response.tool_calls:
        tool_results = await execute_tools(response.tool_calls)
        response = await llm.chat(context + tool_results)
    
    # 5. 更新记忆
    memory.update(request, response)
    
    return response
```

### 3. 渠道初始化流程

```
启动应用 → 加载配置 → 创建 ChannelManager → 初始化各渠道 → 启动监听
                         ↓
                   ConfigWatcher 监听配置变更
                         ↓
                   热加载渠道配置
```

---

## 🧩 核心模块

### AgentRunner

Agent 运行时的核心组件，负责：
- 管理 Agent 生命周期
- 处理消息队列
- 调用 LLM 和工具
- 管理会话状态

```python
# src/copaw/app/runner/runner.py
class AgentRunner:
    async def start(self): ...
    async def stop(self): ...
    async def process(self, request: AgentRequest): ...
    def set_mcp_manager(self, manager): ...
    def set_chat_manager(self, manager): ...
```

### ChannelManager

渠道管理器，负责：
- 加载和初始化渠道
- 管理消息队列
- 分发消息到 AgentRunner

```python
# src/copaw/app/channels/manager.py
class ChannelManager:
    @classmethod
    def from_config(cls, process, config): ...
    async def start_all(self): ...
    async def stop_all(self): ...
```

### BaseChannel

所有渠道的基类，定义统一的接口：

```python
# src/copaw/app/channels/base.py
class BaseChannel:
    channel: str  # 渠道标识
    
    async def start(self): ...
    async def stop(self): ...
    async def send(self, message): ...
```

### MCPClientManager

MCP 客户端管理器，负责：
- 管理 MCP 服务器连接
- 动态加载工具
- 热加载配置

```python
# src/copaw/app/mcp/manager.py
class MCPClientManager:
    async def init_from_config(self, config): ...
    async def close_all(self): ...
    def get_tools(self): ...
```

---

## 📊 数据存储

CoPaw 使用 JSON 文件存储，无需数据库：

```
<working_dir>/
├── config.json          # 主配置文件
├── chats.json           # 聊天记录
├── jobs.json            # 定时任务
├── envs.json            # 环境变量
├── providers.json       # 模型提供商配置
├── copaw.log            # 日志文件
├── memory/              # 记忆存储
└── customized_skills/   # 自定义技能
```

### 配置文件结构

```json
// config.json
{
  "agent": {
    "name": "Friday",
    "description": "A helpful assistant"
  },
  "channels": {
    "dingtalk": {
      "enabled": true,
      "client_id": "...",
      "client_secret": "..."
    }
  },
  "heartbeat": {
    "enabled": true,
    "prompt": "...",
    "cron": "0 */2 * * *"
  },
  "mcp": {
    "mcpServers": {
      "filesystem": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
      }
    }
  }
}
```

---

## 🔌 扩展机制

### 添加新渠道

1. 创建继承 `BaseChannel` 的类
2. 实现 `start()`, `stop()`, `send()` 方法
3. 设置 `channel` 属性为唯一标识
4. 放入 `custom_channels/` 目录

```python
# custom_channels/my_channel.py
from copaw.app.channels.base import BaseChannel

class MyChannel(BaseChannel):
    channel = "my_channel"
    
    async def start(self):
        # 初始化连接
        pass
    
    async def stop(self):
        # 清理资源
        pass
    
    async def send(self, message):
        # 发送消息
        pass
```

### 添加新技能

1. 创建技能目录
2. 编写 SKILL.md
3. （可选）添加参考文档和脚本

```
customized_skills/
  my_skill/
    SKILL.md
    references/
    scripts/
```

---

## 📖 下一步

- [配置指南](./04-configuration.md) - 详细配置说明
- [开发指南](./05-development.md) - 参与开发
- [常见问题](./06-faq.md) - 问题排查