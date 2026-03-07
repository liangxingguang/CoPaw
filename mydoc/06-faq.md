# 常见问题 (FAQ)

本文档收集了 CoPaw 使用和开发过程中的常见问题及解决方案。

---

## 📦 安装问题

### Q: pip 安装速度很慢怎么办？

**A:** 使用国内镜像源：

```bash
pip install copaw -i https://pypi.tuna.tsinghua.edu.cn/simple

# 或永久配置
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### Q: Windows 上安装失败？

**A:** 可能的原因和解决方案：

1. **缺少 Visual C++ Build Tools**
   - 下载安装：https://visualstudio.microsoft.com/visual-cpp-build-tools/

2. **权限问题**
   - 以管理员身份运行 PowerShell 或 CMD

3. **使用一键安装脚本**
   ```powershell
   irm https://copaw.agentscope.io/install.ps1 | iex
   ```

### Q: Python 版本不兼容？

**A:** CoPaw 需要 Python 3.10 - 3.13。

```bash
# 检查 Python 版本
python --version

# 如果版本不对，使用 pyenv 或 conda 安装正确版本
# 使用 conda
conda create -n copaw python=3.11
conda activate copaw
```

### Q: 前端构建失败？

**A:** 确保 Node.js 版本 >= 18：

```bash
node --version

# 如果版本低，更新 Node.js
# macOS
brew install node

# Windows: 从 https://nodejs.org/ 下载安装
```

然后重新安装依赖：

```bash
cd console
rm -rf node_modules
npm ci
npm run build
```

---

## 🚀 启动问题

### Q: 启动后无法访问 Console？

**A:** 检查以下几点：

1. **确认服务已启动**
   ```bash
   # 查看日志输出
   copaw app
   # 应该看到 "Uvicorn running on http://127.0.0.1:8088"
   ```

2. **检查端口是否被占用**
   ```bash
   # Linux/macOS
   lsof -i :8088
   
   # Windows
   netstat -ano | findstr 8088
   ```

3. **尝试其他端口**
   ```bash
   copaw app --port 8089
   ```

4. **检查防火墙设置**
   - 确保 8088 端口未被防火墙阻止

### Q: 前端页面显示 "Console is not available"？

**A:** 这说明前端未正确构建：

```bash
# 重新构建前端
cd console
npm ci
npm run build
cd ..

# 复制构建产物
mkdir -p src/copaw/console
cp -R console/dist/. src/copaw/console/

# 重启服务
copaw app
```

### Q: 启动时报错 "No module named 'copaw'"？

**A:** 确保已正确安装：

```bash
# 开发模式安装
pip install -e ".[dev]"

# 或正式安装
pip install copaw
```

---

## 🤖 模型问题

### Q: 配置了 API Key 但不工作？

**A:** 检查以下几点：

1. **确认 API Key 格式正确**
   - DashScope: 以 `sk-` 开头
   - OpenAI: 以 `sk-` 开头
   - ModelScope: 以 `ms-` 开头

2. **检查 API Key 是否有效**
   - 在 Console 中点击"测试连接"
   - 或在终端测试：
   ```bash
   curl https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"model": "qwen-turbo", "messages": [{"role": "user", "content": "hi"}]}'
   ```

3. **检查是否已启用模型**
   - 在 Console 的模型设置中，确保模型已启用

### Q: 本地模型无法加载？

**A:** 检查以下几点：

1. **确认已安装对应后端**
   ```bash
   # llama.cpp
   pip install "copaw[llamacpp]"
   
   # MLX (仅 macOS)
   pip install "copaw[mlx]"
   
   # Ollama
   pip install "copaw[ollama]"
   ```

2. **确认模型文件存在**
   ```bash
   copaw models list
   ```

3. **Ollama 服务是否运行**
   ```bash
   # 检查 Ollama 服务
   curl http://localhost:11434/api/tags
   ```

### Q: Ollama 连接失败？

**A:** 常见原因：

1. **Ollama 服务未启动**
   ```bash
   ollama serve
   ```

2. **端口不对**
   - 默认端口: 11434
   - 在 Console 中检查 Base URL 是否正确

3. **Docker 环境特殊处理**
   ```bash
   # 使用 host.docker.internal 连接宿主机
   docker run --add-host=host.docker.internal:host-gateway ...
   ```
   然后在设置中将 Ollama Base URL 改为 `http://host.docker.internal:11434/v1`

---

## 📱 渠道问题

### Q: DingTalk 渠道无法连接？

**A:** 检查配置：

1. **确认创建的是 Stream 模式机器人**
   - 钉钉开发者后台 → 创建应用 → 添加机器人 → Stream 模式

2. **确认 Client ID 和 Client Secret 正确**

3. **确认已配置事件订阅**
   - 添加"聊天"事件

4. **查看日志**
   ```bash
   export COPAW_LOG_LEVEL=debug
   copaw app
   ```

### Q: QQ 机器人无法收到消息？

**A:** 检查 NapCat/go-cqhttp 配置：

1. **确认 WebSocket 地址正确**
   - 默认: `ws://127.0.0.1:3001`

2. **确认 NapCat 已启动并登录**

3. **确认消息格式支持**
   - 暂时只支持文本消息

### Q: Telegram Bot 无响应？

**A:** 检查：

1. **Bot Token 是否正确**

2. **网络是否可访问 Telegram API**
   - 国内用户可能需要代理

3. **确认启动了轮询模式**
   - CoPaw 使用 getUpdates 轮询

### Q: Discord Bot 无法加入服务器？

**A:** 需要 OAuth2 邀请：

1. **生成邀请链接**
   - Discord Developer Portal → OAuth2 → URL Generator
   - 勾选 `bot` 和 `applications.commands`
   - 选择权限

2. **访问链接邀请 Bot**

---

## 💬 对话问题

### Q: AI 不回复或回复乱码？

**A:** 可能的原因：

1. **模型未正确配置**
   - 检查 API Key 是否有效
   - 检查模型是否已启用

2. **网络问题**
   - 检查是否能访问 API 端点
   - 尝试代理

3. **查看日志**
   ```bash
   tail -f ~/.copaw/working/copaw.log
   ```

### Q: 如何清除对话历史？

**A:** 在对话中输入命令：

```
/new
```

或使用 CLI：

```bash
copaw clean
```

### Q: AI 记忆不准确？

**A:** 记忆管理：

1. **查看记忆文件**
   ```bash
   cat ~/.copaw/working/memory/MEMORY.md
   ```

2. **手动编辑记忆**
   - 直接编辑 MEMORY.md 文件

3. **清除记忆**
   - 删除 memory/ 目录下的文件

---

## ⏰ 定时任务问题

### Q: 定时任务不执行？

**A:** 检查：

1. **任务是否启用**
   - 在 Console 中查看任务状态

2. **Cron 表达式是否正确**
   - 使用在线工具验证：https://crontab.guru/

3. **查看日志**
   ```bash
   tail -f ~/.copaw/working/copaw.log | grep cron
   ```

### Q: 定时任务执行但无通知？

**A:** 检查：

1. **渠道配置是否正确**
   - 定时任务需要配置渠道才能发送消息

2. **消息格式是否支持**

---

## 🛠️ 开发问题

### Q: pre-commit 检查失败？

**A:**

```bash
# 运行检查看具体错误
pre-commit run --all-files

# 常见问题：
# 1. 格式问题 - 运行 black
black .

# 2. 类型问题 - 检查类型注解

# 3. 修复后重新提交
pre-commit run --all-files
```

### Q: 测试失败？

**A:**

```bash
# 查看详细错误
pytest -v

# 运行单个测试
pytest tests/test_xxx.py::test_xxx -v

# 检查是否是环境问题
pytest --collect-only
```

### Q: 热加载不工作？

**A:** 检查：

1. **配置文件是否保存**
   - 确保 config.json 已正确保存

2. **ConfigWatcher 是否启动**
   - 查看启动日志是否有 "config watcher started"

3. **手动重启**
   ```bash
   # 重启服务
   copaw app
   ```

---

## 🐳 Docker 问题

### Q: Docker 容器无法访问宿主机服务？

**A:**

```bash
# 方式一：添加 host 映射
docker run --add-host=host.docker.internal:host-gateway ...

# 方式二：使用 host 网络（仅 Linux）
docker run --network=host ...

# 方式三：使用宿主机 IP
# 在配置中使用宿主机的实际 IP 地址
```

### Q: Docker 数据丢失？

**A:** 确保使用 volume 持久化：

```bash
docker run -v copaw-data:/app/working ...
```

### Q: Docker 容器启动后立即退出？

**A:** 查看日志：

```bash
docker logs <container_id>
```

常见原因：
- 配置文件错误
- 端口冲突
- 资源不足

---

## 📞 获取帮助

如果以上方法都无法解决问题：

1. **查看官方文档**
   - https://copaw.agentscope.io/docs/faq

2. **搜索 Issues**
   - https://github.com/agentscope-ai/CoPaw/issues

3. **提交新 Issue**
   - 描述问题、复现步骤、错误日志

4. **社区讨论**
   - GitHub Discussions: https://github.com/agentscope-ai/CoPaw/discussions
   - Discord: https://discord.gg/eYMpfnkG8h
   - 钉钉群：见 README 中的二维码