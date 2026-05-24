# grep — 文本模式搜索神器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Ken Thompson（贝尔实验室，Unix 原生命令） |
| 首次发布 | 1974 年 |
| 用途 | 在文件或文本流中搜索匹配指定模式的行并输出，是最古老也最常用的 Unix 文本处理命令 |

## 🎯 用来做什么？

- **在日志文件中搜索错误** — `grep ERROR app.log` 瞬间找出所有报错行
- **过滤命令行输出** — `ps aux | grep python` 只看 Python 进程
- **递归搜索整个项目** — `grep -r "TODO" src/` 找出所有待办项
- **统计匹配行数** — `grep -c "failed" access.log` 统计失败次数

> **跟 ripgrep 的区别**：grep 是系统内置的经典工具，任何 Unix 系统都有。ripgrep 更快、默认忽略 .gitignore，但需要额外安装。日常简单搜索用 grep 就够了。

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `grep "pattern" file` | 在文件中搜索包含 pattern 的行 |
| `grep -i "pattern" file` | 忽略大小写搜索 |
| `grep -r "pattern" dir/` | 递归搜索目录下所有文件 |
| `grep -v "pattern" file` | 反选——输出不匹配的行（排除法） |
| `grep -c "pattern" file` | 只输出匹配行数，不输出内容 |
| `grep -n "pattern" file` | 显示匹配行的行号 |
| `grep -l "pattern" *.txt` | 只输出有匹配的文件名（不显示内容） |
| `command \| grep "pattern"` | 管道过滤——从前一个命令的输出中搜索 |

## 💡 日常使用示例

### 示例 1：搜索日志中的错误

```bash
# 创建示例日志
cat > /tmp/server.log << 'EOF'
2026-05-25 10:00:01 INFO  Server started
2026-05-25 10:00:15 ERROR Database connection failed
2026-05-25 10:00:20 WARN  Retrying connection...
2026-05-25 10:00:25 ERROR Database connection failed
2026-05-25 10:00:30 INFO  Connection restored
EOF

# 搜索所有 ERROR 行（显示行号）
grep -n "ERROR" /tmp/server.log
# 输出：
# 2:2026-05-25 10:00:15 ERROR Database connection failed
# 4:2026-05-25 10:00:25 ERROR Database connection failed

# 统计 ERROR 数量
grep -c "ERROR" /tmp/server.log
# 输出：2

# 排除 INFO 行，只看警告和错误
grep -v "INFO" /tmp/server.log
# 输出：
# 2026-05-25 10:00:15 ERROR Database connection failed
# 2026-05-25 10:00:20 WARN  Retrying connection...
# 2026-05-25 10:00:25 ERROR Database connection failed
```

### 示例 2：过滤进程列表

```bash
# 列出所有 Python 相关进程（排除 grep 命令本身）
ps aux | grep python | grep -v grep
```

### 示例 3：递归搜索代码中的 TODO

```bash
# 创建示例项目
mkdir -p /tmp/myproject
echo "# TODO: add login" > /tmp/myproject/auth.py
echo "# DONE: basic setup" > /tmp/myproject/main.py
echo "<!-- TODO: fix CSS -->" > /tmp/myproject/index.html

# 递归搜索所有 TODO（忽略大小写）
grep -ri "todo" /tmp/myproject/
# 输出：
# /tmp/myproject/auth.py:# TODO: add login
# /tmp/myproject/index.html:<!-- TODO: fix CSS -->
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| `grep` 误把搜索词当成选项 | 执行 `grep -v test.txt file` 报错 | 用 `--` 分隔：`grep -- -v test.txt`，或用 `grep -e "-v"` |
| macOS grep 和 Linux grep 不同 | macOS 是 BSD 版，某些 GNU 选项（如 `-P`  Perl 正则）不支持 | 如需 GNU grep，用 `brew install grep` 安装，命令为 `ggrep` |
| 二进制文件输出乱码 | `grep "pattern" binary_file` 显示 binary file matches | 加 `-a`（强制文本模式）或 `-I`（忽略二进制文件） |
| 正则特殊字符未转义 | 搜索 `.` 匹配了所有字符 | 用 `grep -F "literal.text"`（固定字符串模式）或转义 `\.` |

## 🔗 参考链接

- [GNU Grep 官方手册](https://www.gnu.org/software/grep/manual/grep.html)
- [BSD grep man page](https://man.freebsd.org/cgi/man.cgi?grep)
- [macOS grep man page](https://ss64.com/osx/grep.html)
