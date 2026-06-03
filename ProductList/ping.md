# ping — 网络连通性测试工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Mike Muuss（最初实现） |
| 首发年代 | 1983 年（4.3BSD） |
| 用途 | 向目标主机发送 ICMP 回显请求，检测网络连通性、延迟与丢包率 |

## 🎯 用来做什么？

- **检查网络通不通** — 目标机器在线吗？是否能路由到达？
- **测量网络延迟** — 返回响应耗时（RTT），判断网络快慢
- **诊断丢包** — 连续 ping 可计算丢包率，定位网络不稳定问题
- **DNS 域名解析验证** — ping 域名时会自动解析 IP，顺便能看出域名能否被解析

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ping <host>` | 持续 ping，按 `Ctrl+C` 停止（macOS 默认无限，Linux 默认 4 次） |
| `ping -c 5 <host>` | 只发 5 次后自动停止 |
| `ping -i 2 <host>` | 每 2 秒发一次（默认 1 秒） |
| `ping -s 1000 <host>` | 发 1000 字节的数据包（默认 56 字节），测大包延迟 |
| `ping -W 5 <host>` | 超时时间设为 5 秒（macOS 特有） |
| `ping -t 64 <host>` | 设置 TTL（生存时间），用于排查路由跳数 |
| `ping -o <host>` | 收到第一个回复后自动停止，适合脚本「等到能连通」 |

> **macOS 差异**：macOS 的 `ping` 默认持续发送直到中断（Linux 默认发 4 次即停）。macOS 没有 `-D`（打印时间戳），需靠 `ping -c 1 | date` 配合。超时选项是 `-W`（秒），而非 Linux 的 `-w`。

## 💡 日常使用示例

### 例 1：检查百度能不能连上

```bash
$ ping -c 4 baidu.com
PING baidu.com (110.242.68.66): 56 data bytes
64 bytes from 110.242.68.66: icmp_seq=0 ttl=50 time=8.423 ms
64 bytes from 110.242.68.66: icmp_seq=1 ttl=50 time=8.550 ms
64 bytes from 110.242.68.66: icmp_seq=2 ttl=50 time=8.413 ms
64 bytes from 110.242.68.66: icmp_seq=3 ttl=50 time=8.528 ms

--- baidu.com ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 8.413/8.479/8.550/0.067 ms
```

解释：4 个包全部收到、0 丢包、平均延迟 8.5ms —— 网络状况良好。

### 例 2：检测 Wi-Fi 是否间歇断连（10 秒 10 次）

```bash
$ ping -c 10 -i 1 192.168.1.1
PING 192.168.1.1 (192.168.1.1): 56 data bytes
Request timeout for icmp_seq 3
64 bytes from 192.168.1.1: icmp_seq=4 ttl=64 time=2.134 ms
64 bytes from 192.168.1.1: icmp_seq=5 ttl=64 time=1.008 ms
Request timeout for icmp_seq 6
64 bytes from 192.168.1.1: icmp_seq=7 ttl=64 time=1.512 ms
64 bytes from 192.168.1.1: icmp_seq=8 ttl=64 time=1.234 ms
64 bytes from 192.168.1.1: icmp_seq=9 ttl=64 time=1.889 ms

--- 192.168.1.1 ping statistics ---
10 packets transmitted, 8 packets received, 20.0% packet loss
```

解释：20% 丢包，说明 Wi-Fi 不稳定。可以靠这个数据跟运营商或路由器厂商沟通。

### 例 3：脚本中用 ping 等待服务器启动

```bash
# 每隔 1 秒 ping 一次，直到收到回包才继续（macOS）
echo "等待服务器 10.0.0.5 启动..."
ping -c 1 -t 1 -o 10.0.0.5 > /dev/null 2>&1 && echo "服务器已在线" || echo "服务器无响应"
```

> - `-o` 收到第一个回复即停
> - `-t 1` 单次超时 1 秒（不是 TTL，macOS 里 `-t` 是 TTL，超时要靠 `-W`，这里简化用 `-c 1` 配合）

修正版：
```bash
# 更稳健的写法：循环尝试 30 次
for i in $(seq 1 30); do
  ping -c 1 -W 1 10.0.0.5 > /dev/null 2>&1 && echo "✅ 服务器 ${i} 秒后上线" && break
  sleep 1
done
```

## 🚨 常见坑点

| 现象 / 错误 | 原因 | 解决方法 |
|-------------|------|----------|
| `Request timeout` | 目标没回复，可能离线、防火墙拦截或丢包 | 检查网络、放行 ICMP 或改用 `nc -zv` |
| `ping: cannot resolve <host>: Unknown host` | DNS 无法解析域名 | 检查域名拼写，或先 `nslookup <host>` |
| `PING: sendto: No route to host` | 路由表中没有到目标的路径 | 查看 `netstat -rn`，检查网关配置 |
| 第一次 ping 很慢 / 丢第一包 | ARP 解析需要时间 | 正常现象，多 ping 几次取稳定值 |
| macOS 上 `ping 127.0.0.1` 也丢包 | 通常是软件防火墙或 VPN 干扰 | 关闭 VPN 或检查防火墙规则 |
| 有些服务器永远 ping 不通但 HTTP 正常 | 目标禁用了 ICMP 回显 | 改用 `curl -I <url>` 或 `nc -zv <host> <port>` |

## 🔗 参考链接

- [macOS ping man page (ss64)](https://ss64.com/mac/ping.html)
- [ping 的工作原理解析 — ICMP 协议](https://www.rfc-editor.org/rfc/rfc792)