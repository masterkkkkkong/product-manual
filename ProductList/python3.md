# Python3 — 全能编程语言解释器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Python Software Foundation |
| 首次发布 | 1991 年（Python 3.0 于 2008 年） |
| 用途 | 执行 Python 脚本、运行交互式 REPL、启动内置模块作为工具 |
| 官网 | [python.org](https://www.python.org/) |

## 🎯 用来做什么？

- **运行 Python 脚本** — 任何 `.py` 文件，直接 `python3 script.py`
- **交互式编程** — 不写文件，直接输入代码当场验证
- **命令行瑞士军刀** — `python3 -m` 可以启动 HTTP 服务器、格式化 JSON、压缩文件等
- **自动化胶水** — 文件处理、数据清洗、API 调用、系统管理等一切粘合工作

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `python3 script.py` | 运行 Python 脚本 |
| `python3` | 进入交互式 REPL（退出输入 `exit()` 或 Ctrl+D） |
| `python3 -c 'print("hello")'` | 执行单行代码，适合快速测试 |
| `python3 -m http.server` | 启动当前目录的 HTTP 文件服务器（默认端口 8000） |
| `python3 -m json.tool file.json` | 格式化 / 验证 JSON 文件 |
| `python3 -m venv .venv` | 创建虚拟环境 |
| `python3 -m pip install pkg` | 安装 Python 包 |
| `python3 --version` | 查看 Python 版本 |

## 💡 日常使用示例

### 示例 1：快速计算，不用打开计算器

```bash
# 直接在终端算
python3 -c "print(2**10 * 3.14)"
# 输出：3215.36

# 列表推导式
python3 -c "print([x*x for x in range(1, 11)])"
# 输出：[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

### 示例 2：用 `-m json.tool` 格式化 JSON

```bash
# 先创建一个乱糟糟的 JSON 文件
echo '{"name":"Alice","scores":[90,85,92],"address":{"city":"Beijing","zip":"100000"}}' > /tmp/data.json

# 格式化输出
python3 -m json.tool /tmp/data.json
```

输出：

```json
{
    "name": "Alice",
    "scores": [
        90,
        85,
        92
    ],
    "address": {
        "city": "Beijing",
        "zip": "100000"
    }
}
```

也可以直接从管道读取：

```bash
curl -s https://api.github.com/repos/python/cpython | python3 -m json.tool | head -20
```

### 示例 3：一键启动 HTTP 文件服务器

```bash
# 在某个目录下运行
cd /path/to/share
python3 -m http.server 8080
# 浏览器打开 http://localhost:8080 即可浏览和下载文件

# 只绑定本地（安全）
python3 -m http.server 8080 -b 127.0.0.1

# 指定目录
python3 -m http.server 8080 -d /path/to/share
```

> ⚠️ 仅限局域网或本地使用，不要暴露到公网。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `command not found: python` | macOS 没有 `python` 命令（只有 `python3`） | 用 `python3` 而不是 `python`，或安装 `python` 包 |
| `pip` 装到了系统 Python | 没激活虚拟环境 | 先 `python3 -m venv .venv && source .venv/bin/activate` |
| `ModuleNotFoundError: No module named 'xxx'` | 包没装或装错环境 | `python3 -m pip list` 检查，确认激活了正确的 venv |
| macOS 提示「需要安装 Developer Tools」 | 第一次运行会触发 Xcode CLI 安装 | 点「安装」等几分钟，或手动 `xcode-select --install` |
| PEP 668 错误 | 系统 Python 阻止直接装包 | 必须用虚拟环境：`python3 -m venv .venv` 再 `pip install` |

## 🔗 参考链接

- [Python 官方文档](https://docs.python.org/3/)
- [Python 内置模块索引](https://docs.python.org/3/py-modindex.html)
- [PEP 668 – 系统包管理保护](https://peps.python.org/pep-0668/)
