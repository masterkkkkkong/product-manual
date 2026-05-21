# lsof — 列出打开的文件

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Victor A. Abell（Purdue University） |
| 首次发布 | 1994年 |
| 用途 | 列出当前系统上所有打开的文件和网络连接，排查"端口被谁占了"等问题的终极工具 |

## 🎯 用来做什么？

- **找出哪个进程占用了某个端口** — 启动服务时提示"端口已用"，用它查凶手
- **查看某个进程打开了哪些文件** — 分析程序行为或排查文件泄露
- **监控网络连接** — 查看到本机的所有 TCP/UDP 连接
- **检查文件是否被占用** — 想卸载 U 盘时提示"设备忙"，用它查是谁在用

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `lsof -i :PORT` | 查看哪个进程占用了指定端口 |
| `lsof -i TCP | UDP` | 查看所有 TCP 或 UDP 网络连接 |
| `lsof -p PID` | 查看指定进程 ID 打开的所有文件 |
| `lsof -u USER` | 查看指定用户打开的文件 |
| `lsof +D /path` | 查看指定目录下所有被打开的文件 |
| `lsof -P -i` | 显示端口号而不是服务名（更快） |
| `lsof -t -i :PORT` | 只列出占用端口的 PID（适合脚本用） |
| `lsof -i :PORT | grep LISTEN` | 只看监听态的端口（服务端口） |

## 💡 日常使用示例

### 示例 1：追查端口冲突（最常见的场景）

你启动服务时提示 `port 3000 already in use`，查谁占了：

```
$ lsof -i :3000
COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
node     4321   mk   32u  IPv4 0x1234567890abcdef      0t0  TCP *:3000 (LISTEN)
```

知道 PID 是 4321，确认无误后杀死：

```bash
kill -9 4321    # 或：kill -9 $(lsof -t -i :3000)
```

### 示例 2：查看当前所有网络监听端口

想知道本机正对外提供哪些服务：

```
$ lsof -i -P | grep LISTEN
ControlCe   594   mk    8u  IPv4  ...  TCP *:7000 (LISTEN)
ControlCe   594   mk   10u  IPv4  ...  TCP *:5000 (LISTEN)
rapportd    611   mk    8u  IPv4  ...  TCP *:65133 (LISTEN)
```

- `-P`：显示纯数字端口号（不查 /etc/services，速度更快）
- `grep LISTEN`：过滤出真正在监听的服务

### 示例 3：查看某个进程打开的文件

看看当前终端进程打开了什么文件：

```
$ lsof -p $$
COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF    NODE NAME
bash    94238   mk  cwd    DIR               1,18      256 1069303 /Users/mk/projects
bash    94238   mk  txt    REG               1,18  1310224  ...   /bin/bash
bash    94238   mk    0u   CHR                3,2      0t0     339 /dev/null
bash    94238   mk    1   PIPE   0x7154f06384ef659c    16384        ->pipe
bash    94238   mk    2   PIPE   0x7154f06384ef659c    16384        ->pipe
```

字段说明：
- `FD`：文件描述符（`cwd`=当前目录，`txt`=可执行文件，`0u`=标准输入，`1`=标准输出）
- `TYPE`：文件类型（`REG`=普通文件，`DIR`=目录，`CHR`=字符设备，`IPv4`=网络连接）

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `lsof: command not found` | macOS 精简版或 Linux 没装 | macOS 一般都自带，试试 `sudo lsof` |
| 输出太多看不懂 | `lsof` 默认显示所有打开的文件 | 加 `-i` 或 `-p PID` 过滤 |
| 某些连接看不到 | 需要 root 权限才能看到所有进程 | 加 `sudo`：`sudo lsof -i :80` |
| macOS 上 `lsof -i` 特别慢 | 默认会反向 DNS 查询主机名 | 加 `-P` 跳过端口名和 `-n` 跳过主机名解析 |
| 查不到某个端口 | 可能端口没被监听（只是对外发起连接） | 去掉 `grep LISTEN`，看所有连接 |

## 🔗 参考链接

- [官方手册 (lsof man page)](https://man7.org/linux/man-pages/man8/lsof.8.html)
- [lsof 官网](https://lsof.readthedocs.io/)
- macOS 自带，无需安装
