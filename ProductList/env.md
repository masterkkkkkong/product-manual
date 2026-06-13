# env — 环境变量查看与修改工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs (Unix) |
| 首次发布 | 1979 (V7 Unix) |
| 用途 | 查看当前环境变量，或在修改后的环境中执行命令 |

## 🎯 用来做什么？

- **查看所有环境变量** — 不接参数直接运行 `env`，列出当前 shell 的所有环境变量
- **临时修改环境变量** — 在某个命令前设置变量，不影响当前 shell
- **清空环境执行命令** — 用 `-i` 在完全干净的环境中运行程序，用于调试或隔离
- **删除特定变量** — 用 `-u` 移除某个环境变量后再执行命令
- **macOS 特有** — `-S` 拆分字符串参数，`-P` 指定搜索路径

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `env` | 打印所有环境变量 |
| `env \| grep PATH` | 搜索特定的环境变量 |
| `env MY_VAR=value command` | 设置临时变量后执行命令 |
| `env -i command` | 在空环境中执行命令 |
| `env -u NAME command` | 移除某个变量后执行命令 |
| `env -0` | 用 NUL 字符分隔输出（便于脚本处理） |
| `env -i PATH=/usr/bin HOME=/tmp command` | 只保留指定变量执行命令 |

## 💡 日常使用示例

### 1. 查看当前环境变量

```bash
env | head -5
```

输出示例：
```
HOME=/Users/mk
SHELL=/bin/zsh
PATH=/usr/local/bin:/usr/bin:/bin
USER=mk
LANG=zh_CN.UTF-8
```

### 2. 设置临时环境变量运行命令

运行命令时临时设定环境变量，不影响当前 shell：

```bash
env DEBUG=true python3 -c "import os; print(os.getenv('DEBUG'))"
# 输出: true
```

### 3. 清空环境运行（调试用）

```bash
env -i PATH=/usr/bin HOME=/tmp /usr/bin/env
# 输出:
# PATH=/usr/bin
# HOME=/tmp
```

这会在一个几乎空的环境中运行，只有你明确指定的变量才存在。适用于测试脚本是否依赖了未声明的环境变量。

### 4. 删除某个环境变量

```bash
env -u HOME env | grep HOME
echo "exit: $?"  # 输出 1，表示 HOME 已不存在
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `env --help` 报错 | macOS 的 env 不支持 `--help` 长选项 | 用 `man env` 查看文档 |
| 设置了变量但程序读不到 | 变量名拼写错误，或程序使用了不同的大小写 | 运行 `env` 确认变量存在，注意大小写敏感 |
| `-i` 后命令找不到 | PATH 被清空，shell 找不到可执行文件 | 显式指定 `PATH=/usr/bin:/bin` |
| 变量值含空格时被拆分 | 空格被 shell 解析为参数分隔 | 用引号包裹：`env MSG="hello world" command` |

## 🔗 参考链接

- [macOS man page - env](https://man.freebsd.org/cgi/man.cgi?query=env)
- [GNU Coreutils env](https://www.gnu.org/software/coreutils/manual/html_node/env-invocation.html)
