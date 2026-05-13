# Python venv — Python 虚拟环境工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Python 核心开发团队 |
| 首次发布 | Python 3.3（2012 年） |
| 用途 | 为每个 Python 项目创建隔离的运行环境，避免依赖冲突 |
| macOS 自带 | ✅ 是（Python 3.3+ 内置，无需额外安装） |

## 🎯 用来做什么？

- **隔离项目依赖**：每个项目有自己独立的 `pip install` 环境，互不干扰
- **避免版本冲突**：项目 A 需要 Flask 2.x，项目 B 需要 Flask 1.x——虚拟环境让它们共存
- **复现环境**：通过 `requirements.txt` 精确锁定依赖版本，别人 clone 后一键复现
- **保护系统 Python**：不污染系统级的 Python 包，避免误删系统关键模块

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `python3 -m venv <目录名>` | 创建虚拟环境（目录名习惯用 `venv` 或 `.venv`) |
| `source venv/bin/activate` | **macOS / Linux**：激活虚拟环境 |
| `venv\Scripts\activate` | **Windows**：激活虚拟环境 |
| `deactivate` | 退出当前虚拟环境 |
| `pip install <包名>` | （激活后）在虚拟环境中安装包 |
| `pip freeze > requirements.txt` | 导出当前环境的依赖清单 |
| `pip install -r requirements.txt` | 根据清单安装所有依赖 |
| `python3 -m venv --system-site-packages venv` | 创建虚拟环境并允许访问系统包 |
| `rm -rf venv` | 删除虚拟环境（删目录即可，清爽干净） |

## 💡 日常使用示例

### 示例 1：从零开始使用虚拟环境

```bash
# 1. 创建一个项目目录
mkdir my-flask-app
cd my-flask-app

# 2. 创建虚拟环境（在项目根目录下）
python3 -m venv venv

# 3. 激活虚拟环境
source venv/bin/activate

# 激活后，终端提示符会变成这样：
# (venv) ➜  my-flask-app
# 前面的 (venv) 表示当前在虚拟环境中

# 4. 安装依赖（安装到虚拟环境中，而非全局）
pip install flask

# 5. 查看已安装的包
pip list
# 输出示例：
# Package         Version
# --------------- -------
# click           8.1.x
# Flask           3.x.x
# itsdangerous    2.x.x
# Jinja2          3.x.x
# MarkupSafe      2.x.x
# pip             xx.x.x
# Werkzeug        3.x.x

# 6. 导出依赖清单
pip freeze > requirements.txt
# requirements.txt 内容示例：
# click==8.1.7
# Flask==3.1.0
# itsdangerous==2.2.0
# Jinja2==3.1.4
# MarkupSafe==2.1.5
# Werkzeug==3.1.3

# 7. 退出虚拟环境
deactivate
```

### 示例 2：复现别人的项目环境

```bash
# 克隆一个项目
git clone https://github.com/example/some-project.git
cd some-project

# 创建并激活虚拟环境
python3 -m venv venv
source venv/bin/activate

# 从 requirements.txt 一键安装所有依赖
pip install -r requirements.txt

# 搞定！可以运行项目了
python app.py
```

### 示例 3：管理多个 Python 版本

macOS 上可能同时存在 Python 3.9（系统）和 Python 3.12（Homebrew 安装）。可以用指定版本的 Python 创建虚拟环境：

```bash
# 查看系统有哪些 Python 版本
ls /usr/local/bin/python3* 2>/dev/null || ls /opt/homebrew/bin/python3* 2>/dev/null

# 用特定版本的 Python 创建虚拟环境
python3.12 -m venv venv-312
# 或
/opt/homebrew/bin/python3.12 -m venv venv-312

source venv-312/bin/activate
python --version
# Python 3.12.x
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **忘记激活虚拟环境** | `pip install` 安装到了全局 | 查看终端提示符前有没有 `(venv)`；或运行 `which pip` 确认路径在 `venv/bin/` 下 |
| **虚拟环境目录被 Git 提交** | 仓库变大，且跨平台不可用 | **不要把 `venv/` 提交到 Git**。在 `.gitignore` 中添加 `venv/` 和 `.venv/` |
| **激活脚本找不到** | `source venv/bin/activate: No such file or directory` | 确认当前目录下确有 `venv` 目录；或者创建时指定的不是 `venv` 而是其他名字 |
| **macOS 系统 Python 版本太旧** | `python3 -m venv` 报错 `ensurepip is not available` | 用 Homebrew 安装新版本：`brew install python@3.12`，然后用新版本创建 |
| **退出虚拟环境后 pip 还在** | `deactivate` 后 `pip` 命令还能用 | 正常运行——pip 命令可能全局也有安装。用 `pip -V` 确认当前 pip 的路径 |
| **虚拟环境装包很慢** | `pip install` 下载速度极慢 | 配置国内镜像：`pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple` |
| **VS Code 不识别虚拟环境** | 运行代码时用的不是 `venv` 里的 Python | 在 VS Code 中按 `Cmd+Shift+P` → `Python: Select Interpreter` → 选择 `venv/bin/python` |

## 🔗 参考链接

- [Python venv 官方文档](https://docs.python.org/zh-cn/3/library/venv.html)
- [Python 包管理最佳实践](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)
- [Pipenv vs venv vs Poetry — 虚拟环境工具对比](https://realpython.com/pipenv-guide/)
