# which — 查找命令的路径

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs |
| 首次发布 | 1980s |
| 用途 | 查看某个命令的可执行文件在文件系统中的位置 |

## 🎯 用来做什么？

- **查命令从哪里来** — 输入 `which python3` 就能看到它实际在磁盘的哪个位置
- **区分多个同名版本** — 用 `-a` 列出 PATH 中所有匹配的可执行文件
- **快速验证安装** — 检查一个程序是否已安装且在 PATH 中
- **判断执行顺序** — 当有多个版本时，查看哪个会被优先调用
- **脚本中检测工具是否存在** — 用 `-s` 静默检查，只看退出码

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `which python3` | 查找 python3 命令的路径 |
| `which -a python3` | 列出所有路径下的 python3（不只是第一个） |
| `which -s python3` | 静默模式，不输出结果，仅用退出码判断（0=找到，1=未找到） |
| `which git curl vim` | 一次查多个命令 |
| `which -a ls` | 查看 ls 的所有可能位置 |

## 💡 日常使用示例

### 1. 查看常用命令的实际路径

```bash
which python3
# 输出: /usr/bin/python3

which git
# 输出: /usr/bin/git

which brew
# 输出: /opt/homebrew/bin/brew
```

### 2. 列出所有匹配版本（`-a` 选项）

当同一个命令出现在 PATH 的多个位置时：

```bash
which -a python3
# 输出:
# /usr/bin/python3
# /opt/homebrew/bin/python3
# /Users/mk/.hermes/hermes-agent/venv/bin/python3
```

第一个是默认会被执行的版本。这在排查"我明明装了新版但用的还是旧版"时非常有用。

### 3. 静默检查命令是否存在

```bash
which -s python3 && echo "python3 found" || echo "python3 not found"
# 输出: python3 found

which nonexistent_command 2>&1
# 无输出，exit code = 1
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `which` 找到的路径和实际执行的不同 | shell 有 builtin 同名命令（如 `which` 本身、`echo`、`cd`） | 用 `/usr/bin/which` 确保用的是外部命令 |
| `which --help` 报错 | macOS 的 which 不支持 GNU 风格长选项 | 用 `man which` 查看帮助 |
| 找不到明明已安装的命令 | 安装目录不在 PATH 中 | 运行 `echo $PATH` 确认，或指定完整路径 |
| 输出为空但命令能用 | 命令是 shell builtin | 用 `type command` 确认是否为 builtin |

> **补充：`type` vs `which`** — `type` 是 zsh/bash 内置命令，能识别 builtin、alias、function；`which` 只查 PATH 中的外部可执行文件。如果想知道一个命令到底是 builtin 还是外部文件，用 `type`。

## 🔗 参考链接

- [macOS man page - which](https://man.freebsd.org/cgi/man.cgi?query=which)
