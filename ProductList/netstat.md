# netstat — 网络连接与状态查看工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | The GNU Netstat Project / Apple |
| 首次发布 | 1983 年（BSD 4.2） |
| 用途 | 查看网络连接、路由表、网络接口统计、端口监听状态，网络排障的一线工具 |

## 🎯 用来做什么？

- **查端口占用** — 看某个端口被哪个进程占了，"端口被占用了"的第一反应工具
- **看连接状态** — 列出当前系统的所有 TCP/UDP 连接，判断有没有异常连接
- **查看路由表** — 显示数据包的走向，判断网关是否正确
- **统计网络流量** — 查看各协议的数据包收发统计，分析网络性能

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `netstat -an` | 列出所有连接（不解析主机名和端口名），最常用 |
| `netstat -an \| grep LISTEN` | 只看正在监听的端口（服务器端口） |
| `netstat -an \| grep ESTABLISHED` | 只看已建立的连接 |
| `netstat -rn -f inet` | 查看 IPv4 路由表（网关、子网掩码） |
| `netstat -i` | 查看网卡接口流量统计（收发包数、错误数） |
| `netstat -s` | 按协议（TCP/UDP/IP）统计流量 |
| `netstat -p tcp` | 只显示 TCP 协议（macOS 上需用 `-p`） |
| `netstat -an -p tcp \| wc -l` | 统计当前 TCP 连接数 |

## 💡 日常使用示例

### 例 1：查 3000 端口被谁占了

```bash
netstat -an | grep 3000
```

输出示例：

```
tcp4       0      0  *.3000                 *.*                    LISTEN
tcp46      0      0  *.3000                 *.*                    LISTEN
```

说明有进程在 3000 端口监听。要进一步知道是什么进程，在 macOS 上需要用 `lsof -i :3000`。

### 例 2：查看路由表，确认默认网关

```bash
netstat -rn -f inet
```

输出示例：

```
Routing tables

Internet:
Destination        Gateway            Flags               Netif Expire
default            192.168.31.1       UGScg                 en0
127                127.0.0.1          UCS                   lo0
127.0.0.1          127.0.0.1          UH                    lo0
192.168.31         link#11            UCS                   en0
192.168.31.1       link#11            UHLWIir               en0   1189
```

- `default` 行表示默认网关是 `192.168.31.1`（路由器）
- `Flags` 列：`U`=可用，`G`=网关，`S`=静态，`c`=缓存

### 例 3：查看网卡收发包统计

```bash
netstat -i
```

输出示例：

```
Name    Mtu   Network       Address            Ipkts Ierrs    Opkts Oerrs  Coll
lo0     16384 <Link#1>                       1582097     0  1582097     0     0
en0     1500  <Link#11>    a2:c2:1a:a1:ac:d4 19120800    57 14508990     0     0
```

- `Ipkts` = 接收包数，`Opkts` = 发送包数
- `Ierrs` 非零说明有接收错误，可能信号有问题

## 🚨 常见坑点

| 错误/问题 | 原因 | 解决方法 |
|-----------|------|----------|
| `netstat -p` 显示进程名在 macOS 上不支持 | macOS 的 netstat 没有 Linux 的 `-p`/`--program` 参数 | 改用 `lsof -i :端口` 查看进程 |
| `netstat -tnlp` 在 macOS 上报错 | `-t` `-l` `-p` 是 Linux 特有的参数 | macOS 用 `netstat -an \| grep LISTEN` |
| `netstat -r` 显示域名而非 IP | 默认解析主机名 | 加 `-n` 参数：`netstat -rn` |
| 端口显示为 `http` 而非 `80` | netstat 默认将端口解析为服务名 | 加 `-n` 参数显示数字端口 |
| 看不到某个端口的监听 | 可能只监听 IPv6（显示 `tcp6`） | 检查 `netstat -an -p tcp` 同时看 tcp4 和 tcp6 |

> **macOS vs Linux 差异**：macOS 的 netstat 来自 BSD，参数和输出格式与 Linux 版有较大差异。Linux 上常用的 `-tlnp`、`-pl` 在 macOS 上不存在。macOS 上查端口进程用 `lsof -i :端口`，Linux 用 `netstat -tlnp`。

## 🔗 参考链接

- [macOS netstat man 手册](https://man.openbsd.org/netstat)
- [lsof 替代方案（查端口进程）](../ProductList/lsof.md)
