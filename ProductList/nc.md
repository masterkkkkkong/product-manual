# nc (netcat) — 网络界的瑞士军刀

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | 原作者为 *Hobbit*，macOS 使用的是 BSD 版 |
| 首次发布 | 1995 年 |
| 用途 | 通过 TCP/UDP 读写网络数据，端口扫描、文件传输、聊天、端口监听 |
| macOS 自带 | ✅ 是（预装在 macOS 中） |

## 🎯 用来做什么？

- **端口扫描**：快速检查远程服务器的端口是否开放
- **文件传输**：两台机器之间直接传文件，不需要 FTP 或 SCP
- **简单的聊天/消息发送**：两台机器之间通过 nc 发消息
- **调试网络服务**：模拟 HTTP 请求、测试端口是否可达

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `nc -vz <host> <port>` | 扫描单个端口是否开放（v=verbose, z=zero I/O） |
| `nc -vz <host> <port1>-<port2>` | 扫描端口范围 |
| `nc -l <port>` | 在本地监听端口（等待连接） |
| `nc <host> <port>` | 连接到远程主机的指定端口 |
| `nc -l <port > file` | 接收端：监听端口并将数据写入文件 |
| `nc <host> <port < file` | 发送端：连接远程并将文件内容发送过去 |
| `echo "GET / HTTP/1.0\n\n" \| nc <host> 80` | 发送原始 HTTP 请求 |
| `nc -l -p <port> -s <ip>` | 绑定到指定 IP 和端口监听 |

## 💡 日常使用示例

### 1️⃣ 测试端口是否开放

```bash
# 检查 google.com 的 80 端口是否开放
$ nc -vz google.com 80
Connection to google.com port 80 [tcp/http] succeeded!

# 检查 443 端口（HTTPS）
$ nc -vz google.com 443
Connection to google.com port 443 [tcp/https] succeeded!

# 扫描一个端口范围（1-100）
$ nc -vz -w 2 google.com 1-100 2>&1 | grep succeeded
Connection to google.com port 80 [tcp/http] succeeded!

# -w 2 表示超时 2 秒，防止卡住
```

### 2️⃣ 两台电脑之间传文件

**接收方（机器 A）先启动监听：**

```bash
# 监听 12345 端口，接收到的数据写入 received.zip
$ nc -l 12345 > received.zip
```

**发送方（机器 B）再发送文件：**

```bash
# 连接机器 A 的 12345 端口，发送 myfile.zip
$ nc <机器A的IP> 12345 < myfile.zip
```

传输完成后发送方自动断开，接收方同时退出。注意不要反了：**发送方用 `<` 重定向，接收方用 `>` 重定向。**

### 3️⃣ 发送原始 HTTP 请求，手动调试 API

```bash
# 直接连到 example.com 的 80 端口，手动发 HTTP GET
$ printf "GET / HTTP/1.0\r\nHost: example.com\r\n\r\n" | nc example.com 80

# 响应示例（简略）：
HTTP/1.0 200 OK
Content-Type: text/html; charset=UTF-8
...

<!doctype html>
<html>
<head>
    <title>Example Domain</title>
...
```

这种方式比 curl 更底层，能看到完整的原始交互过程，适合调试协议级问题。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `nc` 连上后立即断开 | 有些服务（如 HTTPS）要求 TLS 加密，nc 不支持 | 用 `openssl s_client` 代替，或使用 `ncat`（Nmap 版） |
| macOS 的 `nc` 没有 `-e` 选项 | macOS 的 BSD 版 nc 功能比 Linux 的 GNU 版少 | 需要反弹 shell 等高级功能，安装 Nmap 版：`brew install nmap` 然后用 `ncat` |
| 端口扫描进度慢 | 没有设置超时，每个端口等默认超时 | 加 `-w 2`（超时 2 秒） |
| `Connection refused` | 端口没开或有防火墙 | 检查服务是否在运行，检查防火墙规则 |
| macOS 上 `-l` 和 `-p` 不能同时用 | BSD nc 限制 | 只用 `-l <port>` 即可，不需要同时用 `-p` |

> **macOS 差异**：macOS 预装的是 BSD 版的 netcat，功能比 Linux 上常见的 GNU netcat 少。没有 `-e`（执行命令）、没有 `-c`（指定 shell）。需要这些功能请安装 Nmap 版：`brew install nmap`（自带 ncat）。FreeBSD 版的 nc 有时也通过 `brew install netcat` 安装。

## 🔗 参考链接

- [macOS man page - nc](https://ss64.com/osx/nc.html)
- [Nmap ncat 官方文档](https://nmap.org/ncat/)
- [GNU Netcat (Linux 版)](http://netcat.sourceforge.net/)