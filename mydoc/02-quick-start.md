# 快速开始

本指南将帮助您在 5 分钟内启动 CoPaw 并完成基本配置。

---

## 🎯 目标

1. 启动 CoPaw 服务
2. 配置 AI 模型
3. 在 Web Console 中与 AI 对话

---

## 第一步：启动服务

### 如果您已完成安装

```bash
copaw app
```

终端将显示类似以下信息：

```
INFO:     Started server process [12345]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8088
```

### 访问 Web Console

打开浏览器，访问 **http://127.0.0.1:8088/**

您将看到 CoPaw 的聊天界面。

---

## 第二步：配置 AI 模型

CoPaw 需要配置 AI 模型才能正常工作。有两种方式：

### 方式 A：使用云端模型（推荐新手）

1. 在 Console 中点击 **设置** → **模型**
2. 选择一个模型提供商（如 DashScope、ModelScope、OpenAI）
3. 输入您的 **API Key**
4. 点击 **保存**
5. 启用该模型

**获取 API Key：**

| 提供商 | 获取方式 |
|--------|----------|
| DashScope | https://dashscope.console.aliyun.com/ |
| ModelScope | https://modelscope.cn/ |
| OpenAI | https://platform.openai.com/ |

### 方式 B：使用本地模型（无需 API Key）

如果您已安装本地模型支持：

```bash
# 下载模型
copaw models download Qwen/Qwen3-4B-GGUF

# 选择模型
copaw models

# 启动服务
copaw app
```

然后在 Console 的 **设置** → **模型** 中启用本地模型。

---

## 第三步：开始对话

配置完成后，在 Console 的聊天界面输入消息，即可与 AI 助手对话！

### 示例对话

```
用户: 你好，请介绍一下你自己

CoPaw: 你好！我是 CoPaw，你的个人 AI 助手。我可以帮你：
- 回答问题和提供建议
- 处理文件和文档
- 设置定时提醒
- 连接各种聊天平台（DingTalk、QQ 等）
- 执行自定义技能

有什么我可以帮你的吗？
```

---

## 🔧 CLI 常用命令

```bash
# 初始化配置
copaw init                    # 交互式初始化
copaw init --defaults         # 使用默认配置

# 启动服务
copaw app                     # 默认端口 8088
copaw app --port 3000         # 指定端口

# 模型管理
copaw models                  # 选择模型
copaw models download <repo>  # 下载模型
copaw models list             # 列出已下载模型

# 定时任务
copaw cron list               # 列出定时任务
copaw cron add                # 添加定时任务

# 技能管理
copaw skills list             # 列出技能
copaw skills install <name>   # 安装技能

# 清理
copaw clean                   # 清理临时文件

# 卸载
copaw uninstall               # 卸载（保留配置）
copaw uninstall --purge       # 完全卸载
```

---

## 📱 连接聊天平台

CoPaw 支持多种聊天平台，让您可以在 DingTalk、QQ、Discord 等平台使用 AI 助手。

### 配置步骤

1. 在 Console 中点击 **控制** → **渠道**
2. 点击 **添加渠道**
3. 选择渠道类型（如 DingTalk、QQ 等）
4. 按提示填写配置信息
5. 保存并启用

### 支持的渠道

| 渠道 | 说明 |
|------|------|
| DingTalk | 企业通讯工具 |
| Feishu | 字节跳动协作平台 |
| QQ | 腾讯 QQ |
| Discord | 游戏社区平台 |
| Telegram | 即时通讯软件 |
| iMessage | 苹果消息应用 |

详细配置请参考 [配置指南](./04-configuration.md)。

---

## ⏰ 设置定时任务

CoPaw 内置 Cron 支持，可以设置定时提醒和自动化任务。

### 使用 CLI 添加任务

```bash
# 添加定时任务
copaw cron add

# 按提示输入：
# - 任务名称
# - Cron 表达式（如 "0 9 * * *" 表示每天 9:00）
# - 提示内容
```

### 使用 Console 添加任务

1. 点击 **控制** → **定时任务**
2. 点击 **添加任务**
3. 填写任务信息
4. 保存

---

## 🧠 配置记忆功能

CoPaw 支持长期记忆，可以记住重要的信息。

### 使用方式

直接在对话中告诉 CoPaw：

```
用户: 记住，我下周三有一个重要会议

CoPaw: 好的，我已经记住了：你下周三有一个重要会议。
```

### 查看记忆

在 Console 中点击 **智能体** → **工作空间** 可以查看和管理记忆。

---

## 🎨 自定义技能

技能（Skills）是 CoPaw 的核心扩展机制。

### 内置技能

| 技能 | 功能 |
|------|------|
| cron | 定时任务 |
| file_reader | 文件读取 |
| pdf | PDF 处理 |
| docx | Word 文档处理 |
| xlsx | Excel 处理 |
| pptx | PowerPoint 处理 |
| news | 新闻摘要 |
| browser_visible | 浏览器自动化 |

### 安装社区技能

```bash
copaw skills install <skill-name>
```

### 创建自定义技能

在您的工作目录创建 `customized_skills/` 文件夹，添加技能文件：

```
customized_skills/
  my_skill/
    SKILL.md          # 技能说明（必需）
    references/       # 参考文档（可选）
    scripts/          # 辅助脚本（可选）
```

详细开发指南请参考 [开发指南](./05-development.md)。

---

## ✅ 检查清单

- [ ] 服务启动成功
- [ ] 配置了 AI 模型
- [ ] 能在 Console 中正常对话
- [ ] （可选）连接了聊天平台
- [ ] （可选）设置了定时任务
- [ ] （可选）安装了自定义技能

---

## 📖 下一步

- [配置指南](./04-configuration.md) - 深入了解各种配置选项
- [项目架构](./03-architecture.md) - 了解系统设计
- [开发指南](./05-development.md) - 参与项目开发