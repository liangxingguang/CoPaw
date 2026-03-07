# 开发指南

本文档帮助开发者快速上手 CoPaw 项目开发，包括环境配置、代码规范、测试、提交代码等。

---

## 🛠️ 开发环境搭建

### 1. 克隆仓库

```bash
git clone https://github.com/agentscope-ai/CoPaw.git
cd CoPaw
```

### 2. 安装 Python 依赖

```bash
# 创建虚拟环境（推荐）
python -m venv .venv

# 激活虚拟环境
# Linux/macOS:
source .venv/bin/activate
# Windows:
.venv\Scripts\activate

# 安装开发依赖
pip install -e ".[dev]"
```

### 3. 构建前端

```bash
cd console
npm ci
npm run build
cd ..

# 复制构建产物
mkdir -p src/copaw/console
cp -R console/dist/. src/copaw/console/
```

### 4. 配置 Pre-commit

```bash
# 安装 pre-commit 钩子
pre-commit install
```

### 5. 初始化配置

```bash
copaw init --defaults
```

---

## 📝 代码规范

### Python 代码规范

#### 格式化工具

- **black**: 代码格式化（行长度 79 字符）
- **flake8**: 代码检查
- **mypy**: 类型检查
- **pylint**: 代码质量检查

#### 文件头部模板

```python
# -*- coding: utf-8 -*-
"""模块说明：简要描述模块功能。"""
from __future__ import annotations

import os
from typing import TYPE_CHECKING, List, Optional

if TYPE_CHECKING:
    from some_module import SomeType
```

#### 导入顺序

```python
# 1. 标准库（按字母顺序）
import os
import sys
from pathlib import Path
from typing import List, Optional

# 2. 第三方库（按字母顺序）
import httpx
from fastapi import FastAPI

# 3. 本地导入（按字母顺序）
from copaw.config import load_config
from copaw.utils.logging import setup_logger

# 4. TYPE_CHECKING 导入
if TYPE_CHECKING:
    from copaw.app.runner import AgentRunner
```

#### 命名规范

| 类型 | 命名风格 | 示例 |
|------|----------|------|
| 函数/变量 | snake_case | `get_user_info`, `user_name` |
| 类 | PascalCase | `AgentRunner`, `ChannelManager` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRIES`, `DEFAULT_PORT` |
| 私有成员 | 前缀下划线 | `_internal_func`, `_private_var` |

#### 类型注解

```python
from __future__ import annotations
from typing import Optional, List

def process_message(
    message: str,
    context: dict[str, str] | None = None,
) -> dict[str, str]:
    """处理消息."""
    ...

async def fetch_data(url: str) -> list[dict]:
    """异步获取数据."""
    ...
```

#### 异常处理

```python
from copaw.utils.logging import setup_logger

logger = setup_logger()

try:
    result = risky_operation()
except ValueError as e:
    logger.error("Invalid value: %s", e)
    raise
except Exception as e:
    logger.exception("Unexpected error")
    # 处理或重新抛出
```

### TypeScript/React 代码规范

#### 文件结构

```typescript
// 1. React 和框架导入
import { useState, useEffect } from "react";
import { Button, Modal } from "antd";

// 2. 第三方库
import { useTranslation } from "react-i18next";

// 3. 本地导入
import { useProviders } from "./useProviders";
import type { ProviderInfo } from "../../../api/types/provider";

// 4. 样式
import styles from "./index.module.less";
```

#### 组件模板

```typescript
interface ComponentProps {
  title: string;
  onConfirm?: () => void;
}

function ComponentName({ title, onConfirm }: ComponentProps) {
  const { t } = useTranslation();
  const [state, setState] = useState<string | null>(null);

  useEffect(() => {
    // 副作用
  }, []);

  return (
    <div className={styles.container}>
      <h1>{title}</h1>
      <Button onClick={onConfirm}>{t("confirm")}</Button>
    </div>
  );
}

export default ComponentName;
```

#### 命名规范

| 类型 | 命名风格 | 示例 |
|------|----------|------|
| 组件 | PascalCase | `UserCard`, `ChatMessage` |
| 函数/变量 | camelCase | `handleSubmit`, `userName` |
| 类型/接口 | PascalCase | `ProviderInfo`, `ApiResponse` |
| 常量 | UPPER_SNAKE_CASE | `API_BASE_URL` |

---

## 🧪 测试

### 运行测试

```bash
# 运行所有测试
pytest

# 运行单个测试文件
pytest tests/test_command_dispatch.py

# 运行单个测试函数
pytest tests/test_command_dispatch.py::test_parse_daemon_query

# 排除慢测试
pytest -m "not slow"

# 生成覆盖率报告
pytest --cov=src/copaw

# 详细输出
pytest -v
```

### 测试编写规范

```python
# tests/test_example.py
# -*- coding: utf-8 -*-
"""测试模块说明。"""
import pytest

from copaw.module import function_to_test


def test_function_normal_case() -> None:
    """测试正常情况。"""
    result = function_to_test("input")
    assert result == "expected"


def test_function_edge_case() -> None:
    """测试边界情况。"""
    result = function_to_test("")
    assert result is None


def test_function_error_case() -> None:
    """测试异常情况。"""
    with pytest.raises(ValueError):
        function_to_test(None)


@pytest.mark.slow
def test_slow_operation() -> None:
    """慢测试需要标记。"""
    ...
```

### 前端测试

```bash
cd console

# 运行 lint
npm run lint

# 格式化检查
npm run format:check
```

---

## 📤 提交代码

### Commit Message 格式

遵循 [Conventional Commits](https://www.conventionalcommits.org/)：

```
<type>(<scope>): <subject>

[optional body]
```

#### 类型

| 类型 | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档更新 |
| `style` | 代码格式（不影响功能） |
| `refactor` | 重构 |
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建/工具相关 |

#### 示例

```bash
feat(channels): add Telegram channel support

- Add TelegramChannel class
- Support text and image messages
- Add configuration documentation

Closes #123
```

### 提交前检查

```bash
# 运行 pre-commit 检查
pre-commit run --all-files

# 如果有格式问题，修复后重新提交
pre-commit run --all-files

# 运行测试
pytest
```

---

## 🔧 添加新功能

### 添加新的 API 端点

1. **创建路由文件**

```python
# src/copaw/app/routers/my_feature.py
# -*- coding: utf-8 -*-
"""我的功能 API。"""
from fastapi import APIRouter

router = APIRouter()

@router.get("/my-feature")
async def get_my_feature():
    """获取我的功能。"""
    return {"status": "ok"}
```

2. **注册路由**

```python
# src/copaw/app/routers/__init__.py
from .my_feature import router as my_feature_router

# 添加到 routers 列表
```

### 添加新的渠道

1. **创建渠道类**

```python
# src/copaw/app/channels/my_channel.py
# -*- coding: utf-8 -*-
"""我的渠道实现。"""
from .base import BaseChannel

class MyChannel(BaseChannel):
    """我的渠道。"""
    
    channel = "my_channel"
    
    async def start(self) -> None:
        """启动渠道。"""
        # 初始化连接
        pass
    
    async def stop(self) -> None:
        """停止渠道。"""
        # 清理资源
        pass
    
    async def send(self, message: str, **kwargs) -> None:
        """发送消息。"""
        # 发送逻辑
        pass
```

2. **注册渠道**

```python
# src/copaw/app/channels/registry.py
from .my_channel import MyChannel

CHANNEL_REGISTRY = {
    "my_channel": MyChannel,
}
```

### 添加新的技能

1. **创建技能目录**

```
src/copaw/agents/skills/my_skill/
├── SKILL.md
├── references/
│   └── guide.md
└── scripts/
    └── helper.py
```

2. **编写 SKILL.md**

```markdown
---
name: my_skill
description: "使用此技能当用户想要... 触发关键词：X, Y, Z"
---

# 我的技能

## 功能说明

描述技能的功能和使用场景。

## 使用方法

1. 步骤一
2. 步骤二

## 示例

用户: 帮我做某事
助手: [执行技能]
```

### 添加新的工具

```python
# src/copaw/agents/tools/my_tool.py
# -*- coding: utf-8 -*-
"""我的工具。"""
from agentscope.tools import tool

@tool
def my_tool(input_text: str) -> str:
    """我的工具说明。
    
    Args:
        input_text: 输入文本
        
    Returns:
        处理结果
    """
    return f"Processed: {input_text}"
```

---

## 📚 文档更新

### 更新官方文档

官方文档位于 `website/public/docs/`：

```
website/public/docs/
├── intro.md           # 介绍
├── quickstart.md      # 快速开始
├── models.md          # 模型配置
├── channels.md        # 渠道配置
├── skills.md          # 技能说明
└── ...
```

### 更新 API 文档

API 文档通过 FastAPI 自动生成：
- Swagger UI: http://localhost:8088/docs
- ReDoc: http://localhost:8088/redoc

---

## 🐛 调试技巧

### 启用调试日志

```bash
export COPAW_LOG_LEVEL=debug
copaw app
```

### 查看日志文件

```bash
# 日志位置
cat ~/.copaw/working/copaw.log

# 实时查看
tail -f ~/.copaw/working/copaw.log
```

### 调试前端

```bash
cd console
npm run dev
```

访问 http://localhost:5173 进行开发调试。

---

## 🔗 相关资源

- **GitHub 仓库**: https://github.com/agentscope-ai/CoPaw
- **官方文档**: https://copaw.agentscope.io/
- **AgentScope 文档**: https://github.com/agentscope-ai/agentscope
- **问题反馈**: https://github.com/agentscope-ai/CoPaw/issues

---

## 📖 下一步

- [常见问题](./06-faq.md) - 开发中常见问题