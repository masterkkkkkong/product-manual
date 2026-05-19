# UV — 超快的 Python 包管理器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Astral Software（Ruff 的同一团队） |
| 首次发布 | 2024 年 |
| 用途 | 用 Rust 编写的 Python 包管理和项目工具，比 pip 快 10-100 倍，同时替代 pip、pip-tools、pipenv、poetry、virtualenv |

## 🎯 用来做什么？

- **超快安装包**：用 Rust 重写，比 pip 快一个数量级，大型项目秒级完成依赖解析和安装
- **管理虚拟环境**：一键创建 venv，甚至自动发现并使用已有的虚拟环境
- **项目依赖管理**：类似 Poetry 的项目管理模式，自动生成 `uv.lock` 锁定文件，确保依赖版本一致
- **管理 Python 版本**：自动下载和管理不同版本的 Python，无需手动安装 pyenv
- **运行脚本**：`uv run` 自动激活虚拟环境并运行脚本，无需手动 source activate

## 🔧 常用命令

```bash
# 创建虚拟环境（默认在当前目录创建 .venv）
uv venv

# 安装包（像 pip 一样用）
uv pip install requests numpy

# 安装 requirements.txt
uv pip install -r requirements.txt

# 创建一个新项目
uv init my-project

# 添加依赖到项目
uv add flask

# 同步项目依赖（根据 pyproject.toml 安装）
uv sync

# 运行命令（自动激活 .venv）
uv run python app.py

# 查看已安装的包
uv pip list

# 查看项目的依赖树
uv tree

# 管理 Python 版本
uv python list          # 列出可用的 Python 版本
uv python install 3.12  # 安装指定 Python 版本
uv python pin 3.12      # 锁定项目使用 Python 3.12

# 钩子工具（全局安装 CLI 工具）
uv tool install ruff

# 打包并发布
uv build
uv publish
```

## 💡 日常使用示例

### 示例 1：用 UV 替代 pip 管理依赖

```bash
# 传统 pip 方式（慢）
pip install numpy pandas matplotlib

# UV 方式（快 10-50 倍）
uv pip install numpy pandas matplotlib

# 输出示例
Resolved 7 packages in 16ms
Downloaded 7 packages in 8.05s
Installed 7 packages in 144ms
 + contourpy==1.3.1
 + cycler==0.12.1
 + fonttools==4.56.0
 + kiwisolver==1.4.8
 + matplotlib==3.10.1
 + numpy==2.2.4
 + pandas==2.2.4

# 从 requirements.txt 安装
uv pip install -r requirements.txt

# 查看已安装的包
uv pip list
# Package       Version
# numpy         2.2.4
# pandas        2.2.4
# matplotlib    3.10.1
```

### 示例 2：用 UV 的 venv 命令替代 python -m venv

```bash
# 1. 创建虚拟环境
uv venv

# 输出示例
Using Python 3.12.0 interpreter at: /Users/mk/.local/share/uv/python/cpython-3.12.0-macos-aarch64-none/bin/python3
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate

# 2. 激活虚拟环境（macOS/Linux）
source .venv/bin/activate

# 3. 或者不用激活，直接用 uv run
uv run python -c "import numpy; print(numpy.__version__)"

# 4. 在指定路径创建 venv
uv venv /path/to/venv --python 3.11

# 5. 删除虚拟环境
rm -rf .venv
```

### 示例 3：用 UV 的项目管理模式（类似 Poetry）

```bash
# 1. 创建一个新项目
uv init my-web-app
cd my-web-app

# 输出示例
Initialized project `my-web-app`

# 2. 查看生成的 pyproject.toml
cat pyproject.toml
# [project]
# name = "my-web-app"
# version = "0.1.0"
# requires-python = ">=3.12"
# dependencies = []

# 3. 添加依赖
uv add flask requests

# 输出示例
Resolved 22 packages in 48ms
Updated my-web-app (pyproject.toml)
Installed 16 packages in 245ms

# 4. 添加开发依赖
uv add pytest --dev

# 5. 同步环境（拉取新队友的修改时）
uv sync

# 6. 查看依赖树
uv tree
# my-web-app v0.1.0
# ├── flask v3.1.0
# │   ├── blinker v1.9.0
# │   ├── click v8.1.8
# │   ├── itsdangerous v2.2.0
# │   ├── jinja2 v3.1.6
# │   ├── markupsafe v3.0.2
# │   └── werkzeug v3.1.3
# ├── requests v2.32.3
# │   ├── certifi v2025.1.31
# │   ├── charset-normalizer v3.4.1
# │   ├── idna v3.10
# │   └── urllib3 v2.3.0
# └── pytest v8.3.5 (dev)
```

### 示例 4：用 UV 管理 Python 版本

```bash
# 1. 查看可安装的 Python 版本
uv python list

# 2. 安装指定版本
uv python install 3.11

# 3. 为项目锁定 Python 版本
uv python pin 3.11

# 4. 直接在 venv 创建时指定版本
uv venv --python 3.11
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `uv` 命令找不到 | 未安装 UV | `curl -LsSf https://astral.sh/uv/install.sh | sh` 或 `brew install uv` |
| `uv venv` 报 Python 版本找不到 | UV 默认尝试自动下载 Python | 用 `uv venv --python <已安装的版本>` 指定本地已有版本，或用 `uv python install` 安装 |
| `uv pip install` 找不到已安装的包 | 未激活虚拟环境 | UV 会优先使用当前目录的 `.venv`，确保在项目目录中运行，或 `source .venv/bin/activate` |
| UV 创建的 venv 在 VS Code 中不自动识别 | VS Code 默认找 `.venv` 目录 | 正确命名虚拟环境为 `.venv`（UV 默认如此），VS Code 会自动发现 |
| `uv sync` 报 lock 文件冲突 | pyproject.toml 和 uv.lock 不一致 | 删除 `uv.lock` 后重新 `uv sync` |
| 国内下载 UV 安装脚本慢 | 网络原因 | 使用 `brew install uv`（macOS），或从 GitHub Releases 下载预编译二进制 |
| macOS 上 `uv add` 编译某些包失败 | 缺少 macOS 编译依赖 | 先安装 `xcode-select --install` 安装 Xcode 命令行工具 |
| `uv run` 运行时提示不在虚拟环境中 | UV 在当前目录找不到 `.venv` | `cd` 到项目目录，或显式 `uv venv` 创建后重试 |

> **macOS 特别说明**：UV 对 macOS (Apple Silicon 和 Intel) 都有原生二进制支持。推荐用 Homebrew 安装：`brew install uv`。UV 会自动利用 `--python` 参数检测系统 Python 或自己下载的 Python。

## 🔗 参考链接

- [UV 官网](https://docs.astral.sh/uv/)
- [UV GitHub 仓库](https://github.com/astral-sh/uv)
- [UV 官方文档](https://docs.astral.sh/uv/getting-started/)
- [从 pip 迁移到 UV 指南](https://docs.astral.sh/uv/pip/)
- [UV 命令参考](https://docs.astral.sh/uv/reference/cli/)
