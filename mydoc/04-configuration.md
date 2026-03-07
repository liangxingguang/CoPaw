# 配置指南

本文档详细介绍 CoPaw 的各种配置选项，包括模型配置、渠道配置、技能配置等。

---

## 📍 配置文件位置

CoPaw 的配置文件位于**工作目录**中：

```
<working_dir>/
├── config.json          # 主配置文件
├── providers.json       # 模型提供商配置
├── envs.json            # 环境变量
├── jobs.json            # 定时任务
├── chats.json           # 聊天记录
└── memory/              # 记忆存储
```

**默认工作目录：**
- Linux/macOS: `~/.copaw/working/`
- Windows: `%USERPROFILE%\.copaw\working\`

---

## 🤖 模型配置

### 方式一：通过 Console 配置（推荐）

1. 打开 **http://127.0.0.1:8088/**
2. 点击 **设置** → **模型**
3. 选择提供商，输入 API Key
4. 启用模型

### 方式二：通过 CLI 配置

```bash
# 交互式选择模型
copaw models

# 下载本地模型
copaw models download Qwen/Qwen3-4B-GGUF
```

### 方式三：通过环境变量

```bash
# DashScope
export DASHSCOPE_API_KEY=sk-xxxxx

# OpenAI
export OPENAI_API_KEY=sk-xxxxx
```

### 支持的模型提供商

| 提供商 | API Key 前缀 | 说明 |
|--------|-------------|------|
| DashScope | `sk-` | 阿里云灵积 |
| ModelScope | `ms-` | 魔搭社区 |
| OpenAI | `sk-` | OpenAI 官方 |
| Azure OpenAI | - | Azure 部署 |
| Anthropic | `sk-ant-` | Claude |
| Ollama | 无需 | 本地部署 |
| llama.cpp | 无需 | 本地模型 |
| MLX | 无需 | Apple Silicon |
| 自定义 | - | OpenAI 兼容 API |

### 配置文件示例

```json
// providers.json
{
  "providers": [
    {
      "id": "dashscope",
      "api_key": "sk-xxxxx",
      "base_url": "https://dashscope.aliyuncs.com/compatible-mode/v1",
      "chat_model": "qwen3-max"
    }
  ],
  "active_llm": {
    "provider_id": "dashscope",
    "model": "qwen3-max"
  }
}
```

### 添加自定义提供商

1. 在 Console 中点击 **设置** → **模型**
2. 点击 **添加提供商**
3. 填写：
   - **ID**: 唯一标识（如 `my-provider`）
   - **名称**: 显示名称
   - **Base URL**: API 地址
   - **API Key**: 密钥
   - **模型列表**: 支持的模型

---

## 📱 渠道配置

渠道用于连接各种聊天平台，让 CoPaw 可以在 DingTalk、QQ 等平台工作。

### 支持的渠道

| 渠道 | 说明 | 配置难度 |
|------|------|----------|
| DingTalk | 钉钉机器人 | ⭐⭐ |
| Feishu | 飞书机器人 | ⭐⭐ |
| QQ | QQ 机器人 | ⭐⭐⭐ |
| Discord | Discord Bot | ⭐⭐ |
| Telegram | Telegram Bot | ⭐ |
| iMessage | 苹果消息 | ⭐⭐⭐ |

### 配置 DingTalk

1. **创建钉钉机器人**
   - 进入钉钉群 → 群设置 → 智能群助手 → 添加机器人
   - 选择"自定义"机器人
   - 安全设置选择"加签"，记录密钥

2. **获取凭证**
   - `Client ID` 和 `Client Secret`：从钉钉开发者后台获取
   - 创建 Stream 模式应用

3. **在 Console 中配置**
   - 进入 **控制** → **渠道**
   - 添加 DingTalk 渠道
   - 填写 Client ID、Client Secret

### 配置 QQ

使用 NapCat 或 go-cqhttp 等 QQ 协议实现：

```json
// config.json
{
  "channels": {
    "qq": {
      "enabled": true,
      "ws_url": "ws://127.0.0.1:3001",
      "access_token": "your-token"
    }
  }
}
```

### 配置 Telegram

1. **创建 Bot**
   - 在 Telegram 中找到 @BotFather
   - 发送 `/newbot` 创建机器人
   - 获取 Bot Token

2. **配置**
   ```json
   {
     "channels": {
       "telegram": {
         "enabled": true,
         "token": "YOUR_BOT_TOKEN"
       }
     }
   }
   ```

### 配置 Discord

1. **创建 Discord 应用**
   - 访问 https://discord.com/developers/applications
   - 创建新应用，添加 Bot
   - 获取 Token

2. **邀请 Bot**
   - 生成 OAuth2 链接
   - 邀请 Bot 加入服务器

3. **配置**
   ```json
   {
     "channels": {
       "discord": {
         "enabled": true,
         "token": "YOUR_BOT_TOKEN"
       }
     }
   }
   ```

---

## 🔧 环境变量配置

环境变量用于存储敏感信息或第三方服务密钥。

### 通过 Console 配置

1. 进入 **设置** → **环境变量**
2. 添加键值对

### 常用环境变量

| 变量名 | 说明 |
|--------|------|
| `DASHSCOPE_API_KEY` | DashScope API Key |
| `OPENAI_API_KEY` | OpenAI API Key |
| `TAVILY_API_KEY` | Tavily 搜索 API |
| `GOOGLE_API_KEY` | Google 搜索 API |

### 配置文件示例

```json
// envs.json
{
  "TAVILY_API_KEY": "tvly-xxxxx",
  "GOOGLE_API_KEY": "AIza-xxxxx",
  "CUSTOM_VAR": "value"
}
```

---

## ⏰ 定时任务配置

### 通过 Console 配置

1. 进入 **控制** → **定时任务**
2. 点击 **添加任务**
3. 填写任务信息：
   - **名称**: 任务名称
   - **Cron 表达式**: 执行时间
   - **提示词**: 要执行的任务内容

### Cron 表达式格式

```
┌───────────── 分钟 (0 - 59)
│ ┌───────────── 小时 (0 - 23)
│ │ ┌───────────── 日期 (1 - 31)
│ │ │ ┌───────────── 月份 (1 - 12)
│ │ │ │ ┌───────────── 星期 (0 - 6, 0=周日)
│ │ │ │ │
* * * * *
```

### 常用示例

| 表达式 | 说明 |
|--------|------|
| `0 9 * * *` | 每天 9:00 |
| `0 9 * * 1-5` | 工作日 9:00 |
| `*/30 * * * *` | 每 30 分钟 |
| `0 0 * * 0` | 每周日 0:00 |

### 配置文件示例

```json
// jobs.json
[
  {
    "id": "morning-reminder",
    "name": "早安提醒",
    "cron": "0 8 * * *",
    "prompt": "提醒我今天的待办事项",
    "enabled": true
  }
]
```

---

## 💓 心跳配置

心跳是 CoPaw 的定期检查机制，可以主动执行任务。

### 通过 Console 配置

1. 进入 **控制** → **心跳**
2. 配置：
   - **启用状态**: 是否启用
   - **Cron 表达式**: 检查频率
   - **提示词**: 要执行的操作

### 配置文件示例

```json
// config.json
{
  "heartbeat": {
    "enabled": true,
    "prompt": "检查今天的日程安排，如有重要事项请提醒我",
    "cron": "0 */2 * * *"
  }
}
```

---

## 🧠 记忆配置

CoPaw 支持长期记忆，可以记住重要信息。

### 记忆存储位置

```
<working_dir>/memory/
├── MEMORY.md           # 长期记忆（重要信息）
├── 2024-01-15.md       # 每日笔记
└── ...
```

### 使用方式

在对话中告诉 CoPaw：

```
记住，我的生日是 3 月 15 日
```

### 手动编辑

您可以直接编辑 `MEMORY.md` 文件来管理长期记忆。

---

## 🔌 MCP 配置

MCP (Model Context Protocol) 用于扩展 Agent 能力。

### 配置 MCP 服务器

```json
// config.json
{
  "mcp": {
    "mcpServers": {
      "filesystem": {
        "command": "npx",
        "args": [
          "-y",
          "@modelcontextprotocol/server-filesystem",
          "/path/to/allowed/dir"
        ]
      },
      "brave-search": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-brave-search"],
        "env": {
          "BRAVE_API_KEY": "your-api-key"
        }
      }
    }
  }
}
```

### 常用 MCP 服务器

| 服务器 | 功能 |
|--------|------|
| `server-filesystem` | 文件系统访问 |
| `server-brave-search` | Brave 搜索 |
| `server-sqlite` | SQLite 数据库 |
| `server-github` | GitHub 操作 |

---

## 📝 Agent 配置

配置 Agent 的基本属性：

```json
// config.json
{
  "agent": {
    "name": "Friday",
    "description": "A helpful personal assistant",
    "system_prompt": "你是一个乐于助人的助手...",
    "max_history_length": 50,
    "temperature": 0.7
  }
}
```

---

## ⚙️ 高级配置

### 日志级别

```bash
# 设置日志级别
export COPAW_LOG_LEVEL=debug  # debug, info, warning, error
```

### 自定义端口

```bash
# 指定端口启动
copaw app --port 3000

# 或通过环境变量
export COPAW_PORT=3000
copaw app
```

### CORS 配置

```bash
# 允许跨域访问
export CORS_ORIGINS="http://localhost:3000,http://example.com"
```

---

## 📖 下一步

- [开发指南](./05-development.md) - 参与项目开发
- [常见问题](./06-faq.md) - 配置问题排查