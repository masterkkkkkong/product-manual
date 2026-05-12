# pip — Python 包管理器

## 📌 简介

| 项目 | 内容 |
|------|------|
| **全称** | Pip Installs Packages（递归缩写） |
| **开发者** | Python Packaging Authority（PyPA） |
| **首次发布** | 2008 年 |
| **用途** | Python 的官方包管理器，安装、卸载、管理 Python 第三方库 |

> Python 3.4+ 以及 Python 2.7.9+ 自带 pip，不需要额外安装。

## 🎯 用来做什么？

- 安装别人写好的 Python 库（如 pandas, requests, numpy）
- 管理项目依赖（哪些库、什么版本）
- 卸载不再需要的库
- 升级已安装的库到最新版本

## 🔧 常用命令

### 1. 安装包
```bash
# 安装最新版本
pip install requests

# 安装指定版本
pip install requests==2.28.0

# 安装大于等于某个版本
pip install "requests>=2.28"

# 一次性安装多个
pip install requests pandas numpy matplotlib
```

### 2. 卸载包
```bash
pip uninstall requests
# 会询问确认，加 -y 跳过确认
pip uninstall requests -y
```

### 3. 查看已安装的包
```bash
# 查看所有安装的包
pip list

# 查看某个包的详细信息
pip show requests

# 检查哪些包可以升级
pip list --outdated
```

### 4. 升级包
```bash
# 升级某个包
pip install --upgrade requests

# 升级 pip 本身
pip install --upgrade pip
```

### 5. 依赖管理（重要！）
```bash
# 导出当前环境的包列表到文件
pip freeze > requirements.txt

# 从 requirements.txt 安装所有依赖
pip install -r requirements.txt
```
> 这是 Python 项目的标准做法，`requirements.txt` 通常提交到 Git 仓库，
> 别人克隆项目后直接 `pip install -r requirements.txt` 就能安装所有依赖。

### 6. 其他实用命令
```bash
# 检查哪些包有安全漏洞
pip audit

# 检查依赖是否兼容
pip check

# 下载包但不安装
pip download requests

# 查看可供安装的版本
pip index versions requests
```

## 💡 日常使用示例

### 示例 1：搭建数据科学环境
```bash
pip install jupyter pandas numpy matplotlib seaborn scikit-learn
```
安装完成后，直接运行 `jupyter notebook` 就能开始写 Python 数据分析代码。

### 示例 2：复刻别人的项目
```bash
git clone https://github.com/某人/某项目.git
cd 某项目
pip install -r requirements.txt
```
> 如果有 `requirements.txt`，一键装完所有依赖，非常方便。

### 示例 3：为不同项目隔离环境（强烈推荐）
```bash
# 创建虚拟环境
python -m venv myenv

# 激活（macOS/Linux）
source myenv/bin/activate

# 激活（Windows）
myenv\Scripts\activate

# 现在安装的包只在这个项目里有效
pip install flask

# 退出虚拟环境
deactivate
```
> 不同项目可能依赖不同版本的同一个库，虚拟环境避免了版本冲突。

## 📦 pip vs pip3

在 macOS/Linux 上，`pip` 可能指向 Python 2，`pip3` 指向 Python 3。
```bash
# 推荐统一用 pip3，确保操作的是 Python 3
pip3 install requests

# 或者用模块方式调用（最保险）
python3 -m pip install requests
```

## 🚨 常见问题

| 问题 | 原因 | 解决 |
|------|------|------|
| `pip: command not found` | pip 没装 | `python -m ensurepip --upgrade` |
| `Permission denied` | 没权限装到系统目录 | 加 `--user`：`pip install --user requests` |
| 下载太慢 | 默认从国外服务器下载 | 换国内镜像（见下方） |
| `No matching distribution found` | 包名写错了或版本不存在 | 检查拼写和版本号 |

### 国内镜像加速
```bash
# 临时使用
pip install requests -i https://pypi.tuna.tsinghua.edu.cn/simple

# 永久配置
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
其他可选镜像：
- 阿里云：`https://mirrors.aliyun.com/pypi/simple/`
- 中科大：`https://pypi.mirrors.ustc.edu.cn/simple/`
- 豆瓣：`https://pypi.douban.com/simple/`

## 🔗 参考链接

- [pip 官方文档](https://pip.pypa.io/en/stable/)
- [PyPI（Python 包索引）](https://pypi.org/)
- [Python 虚拟环境指南](https://docs.python.org/zh-cn/3/tutorial/venv.html)
