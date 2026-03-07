# 安装指南

本文档详细介绍 CoPaw 的各种安装方式，帮助您根据自身情况选择最合适的安装方法。

---

## 📋 系统要求

| 项目 | 要求 |
|------|------|
| **操作系统** | Windows 10+, macOS 10.15+, Linux (主流发行版) |
| **Python 版本** | Python 3.10 - 3.13 |
| **Node.js 版本** | 18+ (仅前端开发需要) |
| **内存** | 建议 8GB+ (运行本地模型需要更多) |

---

## 🚀 安装方式对比

| 方式 | 适用场景 | 难度 |
|------|----------|------|
| **pip 安装** | 日常使用、快速体验 | ⭐ 简单 |
| **一键安装脚本** | 不熟悉 Python 环境 | ⭐ 简单 |
| **Docker** | 服务器部署、隔离环境 | ⭐⭐ 中等 |
| **从源码安装** | 开发贡献代码 | ⭐⭐⭐ 较复杂 |

---

## 方式一：pip 安装（推荐）

最简单的安装方式，适合大多数用户。

### 1. 确认 Python 版本

```bash
python --version
# 或
python3 --version
```

确保版本在 3.10 - 3.13 之间。

### 2. 安装 CoPaw

```bash
pip install copaw
```

### 3. 可选：安装本地模型支持

```bash
# llama.cpp 支持 (跨平台)
pip install "copaw[llamacpp]"

# MLX 支持 (仅 Apple Silicon Mac)
pip install "copaw[mlx]"

# Ollama 支持
pip install "copaw[ollama]"
```

### 4. 初始化配置

```bash
# 使用默认配置快速初始化
copaw init --defaults

# 或交互式初始化
copaw init
```

### 5. 启动服务

```bash
copaw app
```

打开浏览器访问 **http://127.0.0.1:8088/** 即可使用。

---

## 方式二：一键安装脚本

适合不熟悉 Python 环境的用户，脚本会自动处理依赖。

### macOS / Linux

```bash
curl -fsSL https://copaw.agentscope.io/install.sh | bash
```

**可选参数：**

```bash
# 安装特定版本
curl -fsSL https://copaw.agentscope.io/install.sh | bash -s -- --version 0.0.5

# 安装 Ollama 支持
curl -fsSL https://copaw.agentscope.io/install.sh | bash -s -- --extras ollama

# 安装多个可选组件
curl -fsSL https://copaw.agentscope.io/install.sh | bash -s -- --extras ollama,llamacpp

# 从源码安装 (开发/测试)
curl -fsSL https://copaw.agentscope.io/install.sh | bash -s -- --from-source
```

### Windows

**CMD:**
```cmd
curl -fsSL https://copaw.agentscope.io/install.bat -o install.bat && install.bat
```

**PowerShell:**
```powershell
irm https://copaw.agentscope.io/install.ps1 | iex
```

### 卸载

```bash
# 保留配置和数据
copaw uninstall

# 完全删除（包括配置和数据）
copaw uninstall --purge
```

---

## 方式三：Docker 安装

适合服务器部署或需要环境隔离的场景。

### 1. 拉取镜像

```bash
# 从 Docker Hub 拉取
docker pull agentscope/copaw:latest

# 或从阿里云拉取 (国内用户)
docker pull agentscope-registry.ap-southeast-1.cr.aliyuncs.com/agentscope/copaw:latest
```

### 2. 运行容器

```bash
docker run -p 127.0.0.1:8088:8088 -v copaw-data:/app/working agentscope/copaw:latest
```

### 3. 配置环境变量

```bash
# 传递 API Key
docker run -p 127.0.0.1:8088:8088 \
  -e DASHSCOPE_API_KEY=your-api-key \
  -v copaw-data:/app/working \
  agentscope/copaw:latest

# 或使用环境变量文件
docker run -p 127.0.0.1:8088:8088 \
  --env-file .env \
  -v copaw-data:/app/working \
  agentscope/copaw:latest
```

### 4. 连接本地 Ollama

Docker 容器内的 `localhost` 指向容器本身，需要特殊配置：

```bash
docker run -p 127.0.0.1:8088:8088 \
  --add-host=host.docker.internal:host-gateway \
  -v copaw-data:/app/working \
  agentscope/copaw:latest
```

然后在 Console 设置中，将 Ollama 的 Base URL 改为 `http://host.docker.internal:11434/v1`

---

## 方式四：从源码安装

适合需要参与开发或自定义修改的用户。

### 1. 克隆仓库

```bash
git clone https://github.com/agentscope-ai/CoPaw.git
cd CoPaw
```

### 2. 构建前端

```bash
cd console
npm ci
npm run build
cd ..
```

### 3. 复制前端构建产物

```bash
# 创建目标目录
mkdir -p src/copaw/console

# 复制构建结果
cp -R console/dist/. src/copaw/console/
```

### 4. 安装 Python 包

```bash
# 基础安装
pip install -e .

# 开发模式（包含测试、格式化工具）
pip install -e ".[dev]"
```

### 5. 初始化并启动

```bash
copaw init --defaults
copaw app
```

---

## 🔧 开发环境设置

如果您计划参与开发，建议进行以下配置：

### 1. 安装开发依赖

```bash
pip install -e ".[dev]"
```

### 2. 配置 pre-commit 钩子

```bash
pre-commit install
```

这会在每次提交前自动运行代码检查，包括：
- black（代码格式化）
- flake8（代码检查）
- mypy（类型检查）
- pylint（代码质量检查）

### 3. 手动运行检查

```bash
# 运行所有检查
pre-commit run --all-files

# 运行测试
pytest

# 运行单个测试
pytest tests/test_command_dispatch.py

# 运行测试并生成覆盖率报告
pytest --cov=src/copaw
```

### 4. 前端开发

```bash
cd console

# 安装依赖
npm ci

# 启动开发服务器
npm run dev

# 代码格式化
npm run format

# 代码检查
npm run lint
```

---

## ✅ 验证安装

安装完成后，运行以下命令验证：

```bash
# 检查版本
copaw --version

# 查看帮助
copaw --help

# 启动服务
copaw app
```

打开浏览器访问 http://127.0.0.1:8088/，看到 CoPaw 控制台界面即为安装成功！

---

## ⚠️ 常见问题

### pip 安装速度慢？

使用国内镜像源：

```bash
pip install copaw -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### Windows 上安装失败？

1. 确保已安装 Visual C++ Build Tools
2. 尝试使用 PowerShell 以管理员身份运行
3. 使用一键安装脚本

### 前端构建失败？

1. 确保 Node.js 版本 >= 18
2. 删除 `node_modules` 后重新安装：
   ```bash
   cd console
   rm -rf node_modules
   npm ci
   ```

---

## 📖 下一步

安装完成后，请继续阅读：
- [快速开始](./02-quick-start.md) - 启动并使用 CoPaw
- [配置指南](./04-configuration.md) - 配置模型和渠道