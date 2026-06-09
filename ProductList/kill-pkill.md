# kill / pkill — 进程终止与信号管理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs |
| 首次发布 | Unix V1（1971） |
| 用途 | 向进程发送信号，通常用于终止、暂停或重新加载进程 |

## 🎯 用来做什么？

- **终止卡死的进程** — 强制关闭无响应的程序
- **优雅关闭服务** — 先发 SIGTERM（15）让进程自己清理，不行再 SIGKILL（9）
- **按名称管理进程** — `pkill` 可以通过进程名批量操作
- **重载配置** — 向守护进程发送 SIGHUP（1）让其重新加载配置

## 🔧 常用命令

```bash
# 列出所有可用信号
kill -l

# 终止指定 PID 的进程（默认 SIGTERM）
kill 12345

# 强制杀死进程（SIGKILL，无法被捕获）
kill -9 12345
kill -KILL 12345

# 优雅终止（SIGTERM，进程可以自行清理）
kill -15 12345
kill -TERM 12345

# 按进程名终止（pkill）
pkill -9 chrome

# 按进程名精确匹配
pkill -x nginx

# 发送 SIGHUP 重载配置
kill -HUP 12345

# 查看进程 PID（配合 kill）
pgrep chrome
```

## 💡 日常使用示例

### 1. 强制关闭无响应的程序

```bash
# 先找到进程 PID
$ pgrep -i chrome
1234
5678

# 先尝试优雅终止
$ kill -TERM 1234

# 如果没反应，几秒后用 -9 强制杀死
$ kill -9 1234

# 或直接用 pkill 一步到位
$ pkill -9 -i chrome
```

### 2. 让 nginx 重新加载配置而不中断服务

```bash
# 找到 nginx master 进程
$ pgrep nginx
7890
7891

# master 进程（通常 PID 较小的那个）收到 SIGHUP 会重载配置
$ kill -HUP 7890

# 或用 pkill 更直接（只会匹配 master）
$ pkill -HUP nginx
```

### 3. 暂停和恢复进程（配合 Ctrl+Z 和 fg/bg）

```bash
# 运行一个长时间的任务
$ sleep 1000
^Z
[1]  + 12345 suspended  sleep 1000

# 用 kill 发送 SIGCONT 让它在后台继续运行
$ kill -CONT 12345
# 或在终端用 bg 命令
$ bg
[1]  + 12345 continued  sleep 1000
```

## 🚨 常见坑点

| 问题 | 可能原因 | 解决方法 |
|------|---------|----------|
| `kill -9` 后进程还在 | 进程可能是僵尸进程（zombie） | 僵尸进程需等父进程回收，用 `ps aux | grep Z` 确认 |
| `pkill` 杀错了进程 | 匹配模式太宽泛，匹配到同名进程 | 先用 `pgrep` 预览匹配结果，确认后再 `pkill` |
| `Operation not permitted` | 无权限杀死其他用户的进程 | 加 `sudo`（如 `sudo kill -9 12345`） |
| `kill -9` 杀不掉某个进程 | 内核进程或系统守护进程受保护 | 不要尝试杀死内核进程，可能引发系统不稳定 |
| macOS 上 `kill -9 Finder` 无效 | pkill 需要匹配可执行文件路径名 | 用 `pkill -9 -i Finder` 或 `killall Finder`（macOS 专用） |

> **💡 macOS 差异提示**：macOS 提供 `killall` 命令，可直接按进程名（而非模式匹配）杀死进程，比 `pkill` 更安全：`killall -9 Finder`。

## 🔗 参考链接

- 终端运行 `man kill`、`man pkill`、`man killall`
- [Apple 开发者文档 — kill](https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man2/kill.2.html)
- [Unix 信号列表](https://en.wikipedia.org/wiki/Signal_(IPC))
