# man — 系统手册查看器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 原生命令） |
| 首发年代 | 1970s（Unix 第一版） |
| 用途 | 查看任何命令、函数、配置文件的官方手册，是学习和排查命令的最佳起点 |

## 🎯 用来做什么？

- **查命令用法** — 不需要记住参数，`man <命令>` 就能看到完整文档
- **看配置文件格式** — 如 `man crontab` 看定时任务的配置语法
- **查系统调用和 C 库函数** — `man 2 write`（系统调用）、`man 3 printf`（库函数）
- **离线可用** — 不需要网络，所有文档都在本地

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `man <name>` | 查看命令或函数的 man page（默认显示第一个匹配） |
| `man -k <keyword>` | 按关键词搜索 man page 标题和描述（等价于 `apropos`） |
| `man -f <name>` | 显示所有匹配的手册条目（等价于 `whatis`） |
| `man <section> <name>` | 查看指定章节，如 `man 5 crontab` |
| `man ls \| grep -i “sort”` | 在 man page 中搜索关键词（配合管道） |
| `man -w <name>` | 只显示 man page 的文件路径 |
| `man -P cat <name>` | 用 cat 命令一次性输出全部内容（不分页） |

### 📐 常用章节编号

| 章节 | 内容 |
|------|------|
| **1** | 用户命令（最常用，如 `man 1 ls`） |
| **2** | 系统调用（如 `man 2 open`） |
| **3** | C 库函数（如 `man 3 printf`） |
| **4** | 设备文件和特殊文件 |
| **5** | 文件格式和约定（如 `man 5 crontab`） |
| **6** | 游戏和屏保 |
| **7** | 杂项、宏、标准（如 `man 7 signal`） |
| **8** | 系统管理员命令（如 `man 8 ifconfig`） |

### 🔑 翻阅快捷键

进入 man 页面后（默认用 `less` 分页器）：

| 按键 | 操作 |
|------|------|
| `↑` / `↓` | 上下滚动一行 |
| `Space` / `Ctrl+F` | 向下翻一页 |
| `Ctrl+B` | 向上翻一页 |
| `/keyword` | 向下搜索关键字 |
| `?keyword` | 向上搜索关键字 |
| `n` / `N` | 下一个 / 上一个搜索结果 |
| `q` | 退出 man page |
| `h` | 查看帮助 |

## 💡 日常使用示例

### 例 1：查 `curl` 的用法和所有参数

```bash
$ man curl | head -30
CURL(1)                  General Commands Manual                 CURL(1)

NAME
     curl – transfer a URL

SYNOPSIS
     curl [options / URLs]

DESCRIPTION
     curl is a tool to transfer data from or to a server, using one of
     the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS,
     IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP,
     SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP, WS, WSS).  The
     command is designed to work without user interaction.

     curl offers a busload of useful tricks like proxy support, user
     authentication, FTP upload, HTTP post, SSL connections, cookies,
     file transfer resume, Metalink, and more.
```

*技巧：`/--output` 可以在 man page 内搜索 `--output` 参数。*

### 例 2：用 `man -k` 快速找到不记得名字的命令

想找一个「显示磁盘使用量」的命令但不记得叫啥：

```bash
$ man -k disk | head -10
DR(3), dr(3)           - detect a programming error or unexpected condition
DiskArbitration(8)     - Disk Arbitration command line interface
EVP_DigestInit(3), ...  - EVP hash functions
VR(3), vr(3)           - virtual random access mode
df(1)                  - display free disk space
du(1)                  - display disk usage statistics
disklabel(8)           - read and write disk label
...
```

很快就锁定 `df(1)` 和 `du(1)`。

### 例 3：区分同名命令的不同章节

`crontab` 既是命令（章节 1）也有配置文件格式（章节 5）：

```bash
# 查看 crontab 命令的用法
$ man 1 crontab

# 查看 crontab 配置文件的格式说明
$ man 5 crontab
```

两者内容完全不同。通常第 5 章节才是你看配置文件时需要的信息。

## 🚨 常见坑点

| 现象 / 错误 | 原因 | 解决方法 |
|-------------|------|----------|
| `No manual entry for xxx` | 包未安装，或不是标准手册 | `brew install xxx` 后重试，或检查拼写 |
| 显示内容太长 / 找不到想要的参数 | man page 默认很长，不习惯阅读 | 先用 `man -k` 搜索关键词，配合 `/` 在页内搜 |
| 想一次性输出所有文本（不分页） | 默认用 `less` 分页 | `man -P cat <name>` 或 `man <name> \| cat` |
| macOS 和 Linux 的 man page 内容不同 | BSD 系和 GNU 系参数有差异 | 如果跨平台开发，查一下 macOS 专属 man（`man -k` 可区分） |
| man page 里 `-a` 和 `-A` 傻傻分不清 | man 中选项按字母序排列，容易看花眼 | 配合 `/` 直接跳到关键词，或者用 `tldr <cmd>` 快速参考 |
| 打了 man 但没进入页面，而是变成了纯文本输出 | 可能 `$PAGER` 环境变量被设为 cat | `export PAGER=less` 恢复分页 |

## 🔗 参考链接

- [macOS man pages (ss64)](https://ss64.com/mac/)
- [man 常用章节说明 (Wikipedia)](https://en.wikipedia.org/wiki/Man_page)