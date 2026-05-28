# ps — 进程状态查看器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 基础工具） |
| 首次发布 | 1970s（Unix 早期版本） |
| 用途 | 查看系统中正在运行的进程信息，包括 PID、CPU/内存占用、启动时间、命令行参数等 |

## 🎯 用来做什么？

- **查看进程列表** — 看看系统里有哪些程序在运行
- **定位问题进程** — 找出谁占用了大量 CPU 或内存
- **查找特定进程的 PID** — 配合 kill 终止进程使用
- **检查进程启动时间** — 确认某个服务是否正常运行

## 🔧 常用命令

**注意：macOS（BSD 版）和 Linux（GNU 版）的参数有差异。以下命令在 macOS 上已验证。**

| 命令 | 说明 |
|------|------|
| `ps aux` | 显示所有用户的所有进程，含 CPU/内存占用（最常用） |
| `ps -ef` | 显示所有进程的完整格式，含 PPID（父进程 ID） |
| `ps aux \| grep nginx` | 查找特定进程（示例：查 nginx） |
| `ps ax` | 显示所有进程的简洁版 |
| `ps -eo pid,ppid,%cpu,%mem,comm` | 自定义输出列，只显示需要的字段 |
| `ps -p 1234` | 查看指定 PID（1234）的进程 |
| `ps aux --sort=-%cpu` | ⚠️ Linux 用法；macOS 用 `ps aux -r` 按 CPU 占用排序 |
| `ps aux -r` | macOS: 按 CPU 占用率降序排列 |
| `ps aux -m` | macOS: 按内存占用降序排列 |

### macOS vs Linux 差异速查

| 功能 | macOS (BSD ps) | Linux (GNU ps) |
|------|---------------|----------------|
| 按 CPU 排序 | `ps aux -r` | `ps aux --sort=-%cpu` |
| 按内存排序 | `ps aux -m` | `ps aux --sort=-%mem` |
| 只显示进程名和 PID | `ps -eo pid,comm` | `ps -eo pid,comm --no-headers` |
| 树状显示进程关系 | 需用 `pstree`（brew install pstree） | `ps -ef --forest` |

## 💡 日常使用示例

### 示例 1：找出 CPU 消耗最大的进程

```bash
# macOS：按 CPU 占用降序排列，显示前 10 行
ps aux -r | head -10
```

输出示例：

```
USER               PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
mk                 766   4.6  2.5 412550384 418304   ??  S    Wed02AM  15:19.22 python -m hermes_cli.main gateway run
mk                 288   4.1  1.4 499790496 241504   ??  Ss   Wed11AM 331:58.80 com.apple.WebKit.WebContent
_networkd          467   0.3  0.1 426956304  17088   ??  Ss   Wed02AM   1:25.60 /usr/libexec/symptomsd
_driverkit         483   0.3  0.2 426906800  36912   ??  Ss   Wed02AM  14:45.97 com.apple.DriverKit-AppleBCMWLAN
```

**各字段含义**：
- `USER` — 运行进程的用户
- `PID` — 进程 ID（唯一标识）
- `%CPU` — CPU 占用百分比
- `%MEM` — 内存占用百分比
- `VSZ` — 虚拟内存大小
- `RSS` — 实际物理内存占用
- `STARTED` — 启动时间
- `TIME` — 累计 CPU 使用时间
- `COMMAND` — 运行的命令

### 示例 2：查找并终止一个进程

```bash
# 先找到进程的 PID
ps aux | grep "Python"
# 输出：mk 766 4.6 ... python -m hermes_cli.main gateway run

# 终止进程（PID 为 766）
kill 766
# 如果杀不掉，强制终止
kill -9 766
```

### 示例 3：查看进程的父子关系（macOS）

```bash
# 用 -ef 格式可以看到 PPID（父进程 ID）
ps -ef | head -10
```

输出示例：

```
UID   PID  PPID   C STIME   TTY           TIME CMD
    0     1     0   0 Wed02AM ??        24:01.32 /sbin/launchd
  501   273     1   0 Wed11AM ??        12:12.44 /Applications/Clash Verge.app/...
  501   286     1   0 Wed11AM ??         0:01.03 /usr/libexec/webprivacyd
  501   287     1   0 Wed11AM ??        10:46.25 com.apple.WebKit.GPU.xpc
```

- PID=1 是 `launchd`（macOS 的 init 进程，所有进程的祖先）
- PID 为 273, 286, 287 的 PPID 都是 1，说明它们由 launchd 启动

### 示例 4：只查看自己的进程

```bash
# 只显示当前用户（mk）的进程
ps -U mk | head -15
# 或更简单的办法
ps -x
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `ps --sort=%cpu` 报错 "illegal option" | macOS 的 BSD ps 不支持 `--sort` | 用 `ps aux -r`（macOS 专属） |
| `ps aux` 输出被截断，看不到完整命令 | 默认输出宽度有限 | 用 `ps auxww`（双 w 显示完整命令行） |
| 用 `kill -9` 杀不掉某个进程 | 进程可能是僵尸（Z）或被内核保护 | 僵尸进程只能等父进程回收，或 kill 父进程 |
| 看到很多重复的进程名 | 一个程序可能有多个线程/子进程 | 用 `ps -ef` 看 PPID 区分父子关系 |
| 找不到某进程 | 可能因为名字被截断或大小写 | 用 `ps auxww \| grep -i 关键词`（忽略大小写） |

## 🔗 参考链接

- [macOS ps 手册（man page）](https://ss64.com/osx/ps.html) — macOS 版本官方文档
- [Linux ps 手册](https://man7.org/linux/man-pages/man1/ps.1.html) — Linux GNU 版文档
- 系统自带工具，无需安装