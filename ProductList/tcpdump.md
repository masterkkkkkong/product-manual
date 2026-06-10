# tcpdump — 网络抓包分析工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | The Tcpdump Group |
| 首次发布 | 1988 年 |
| 用途 | 在命令行中捕获和分析网络数据包，排查网络故障、调试协议、分析流量 |

## 🎯 用来做什么？

- **抓包分析** — 实时捕获经过网卡的数据包，查看 HTTP/DNS/TCP 等协议的通信细节
- **网络排障** — 检查连接是否建立、数据是否到达、重传是否频繁，定位网络瓶颈
- **协议调试** — 过滤特定端口、IP、协议的流量，深入分析请求-响应过程
- **安全审计** — 检测异常连接、扫描行为或未经授权的数据传输

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `tcpdump -D` | 列出所有可抓包的网卡接口 |
| `tcpdump -i en0` | 在 en0（Wi-Fi）接口上抓包 |
| `tcpdump -c 10` | 只抓 10 个包后自动停止 |
| `tcpdump -n` | 不解析域名（显示 IP 而非主机名），更快更清晰 |
| `tcpdump port 443` | 只抓 443 端口的流量（HTTPS） |
| `tcpdump host 192.168.1.1` | 只抓与某 IP 通信的流量 |
| `tcpdump -X` | 以十六进制 + ASCII 形式显示包内容 |
| `tcpdump -w output.pcap` | 将抓到的包保存到文件，后续用 Wireshark 分析 |

## 💡 日常使用示例

### 例 1：查看当前所有网卡接口

```bash
tcpdump -D
```

输出示例：

```
1.ap1 [Up, Running, Wireless, Not associated]
2.en0 [Up, Running, Wireless, Associated]
3.awdl0 [Up, Running, Wireless, Associated]
4.llw0 [Up, Running, Connection status unknown]
5.utun0 [Up, Running]
6.utun1 [Up, Running]
...
```

`en0` 是 Wi-Fi 网卡，`lo0` 是本地回环，`utun*` 是 VPN/隧道接口。

### 例 2：抓取 DNS 查询（端口 53）

```bash
sudo tcpdump -i en0 -n port 53 -c 5
```

输出示例：

```
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on en0, link-type EN10MB (Ethernet), snapshot length 524288 bytes
11:23:45.123456 IP 192.168.31.242.54321 > 223.5.5.5.53: 12345+ A? www.baidu.com. (32)
11:23:45.123789 IP 223.5.5.5.53 > 192.168.31.242.54321: 12345 1/0/0 A 39.156.66.10 (48)
11:23:45.124123 IP 192.168.31.242.54322 > 223.5.5.5.53: 12346+ AAAA? www.baidu.com. (32)
...
```

可以看到请求发往 DNS 服务器 `223.5.5.5`，返回了 `39.156.66.10`（百度的 IP）。

### 例 3：保存抓包结果并在 Wireshark 中分析

```bash
# 抓取 100 个 HTTPS 包保存到文件
sudo tcpdump -i en0 -n port 443 -c 100 -w https_traffic.pcap

# 查看保存的文件内容
tcpdump -r https_traffic.pcap -n | head -10
```

## 🚨 常见坑点

| 错误/问题 | 原因 | 解决方法 |
|-----------|------|----------|
| `You don't have permission to capture on that device` | tcpdump 需要 root 权限 | 加上 `sudo` 执行 |
| `tcpdump: no suitable device found` | 指定的网卡不存在 | 先用 `tcpdump -D` 列出可用网卡 |
| 抓到太多包，终端刷屏 | 没有加过滤条件 | 加 `-c N` 限制数量，或用 `port/host` 过滤 |
| 域名解析太慢 | tcpdump 默认做反向 DNS 查询 | 加 `-n` 参数禁用域名解析 |
| 抓 HTTPS 包只看到乱码 | TLS 加密，看不到明文 | 抓包位置需配合 SSLKEYLOGFILE 或在代理侧解密 |
| macOS 上没有 eth0 | macOS 网卡命名不同 | Wi-Fi 用 `en0`，有线以太网通常用 `en1` 或 `en5`，回环用 `lo0` |

## 🔗 参考链接

- [tcpdump 官方主页](https://www.tcpdump.org/)
- [tcpdump 完整 man 手册](https://www.tcpdump.org/manpages/tcpdump.1.html)
- [Wireshark（配合分析 GUI）](https://www.wireshark.org/)
