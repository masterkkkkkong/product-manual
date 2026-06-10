# ifconfig — 网络接口配置与查看工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | 加州大学伯克利分校（BSD） |
| 首次发布 | 1983 年（BSD 4.2） |
| 用途 | 查看和配置网络接口参数（IP、MAC、状态、MTU），网络诊断第一步 |

## 🎯 用来做什么？

- **查本机 IP** — 看当前 Wi-Fi/有线网卡的 IP 地址和子网掩码
- **看 MAC 地址** — 查看网卡的硬件地址（ether），用于局域网识别或 MAC 过滤
- **查网络状态** — 判断网卡是否 active、有没有分配到 IP
- **临时改配置** — 手动设置 IP、启用/禁用网卡、调整 MTU（调试用，重启后失效）

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ifconfig` | 列出所有网卡的完整信息 |
| `ifconfig en0` | 查看 Wi-Fi 网卡的详细信息 |
| `ifconfig lo0` | 查看回环接口（127.0.0.1） |
| `ifconfig en0 \| grep inet` | 只查看 IP 地址 |
| `ifconfig en0 \| grep ether` | 只查看 MAC 地址 |
| `ifconfig en0 \| grep status` | 只看连接状态（active/inactive） |
| `sudo ifconfig en0 down` | 禁用 Wi-Fi 网卡（临时断网） |
| `sudo ifconfig en0 up` | 重新启用 Wi-Fi 网卡 |

## 💡 日常使用示例

### 例 1：快速查看本机 IP 和 MAC 地址

```bash
# 看 Wi-Fi 网卡 en0
ifconfig en0
```

输出示例：

```
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
    options=6460<TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
    ether a2:c2:1a:a1:ac:d4
    inet6 fe80::1830:238b:a50:409c%en0 prefixlen 64 secured scopeid 0xb
    inet 192.168.31.242 netmask 0xffffff00 broadcast 192.168.31.255
    inet6 2409:8a28:de0:3f40::7c4 prefixlen 64 dynamic
    media: autoselect
    status: active
```

关键信息解读：
- `UP` — 网卡已启用
- `ether a2:c2:1a:a1:ac:d4` — MAC 地址
- `inet 192.168.31.242` — IPv4 地址
- `netmask 0xffffff00` — 子网掩码（即 255.255.255.0）
- `status: active` — 已连接

### 例 2：只取 IP 地址（用于脚本）

```bash
# 提取 IPv4 地址
ifconfig en0 | grep 'inet ' | awk '{print $2}'
```

输出：

```
192.168.31.242
```

### 例 3：查网卡连接状态（排查网络问题）

```bash
# 检查所有网卡的状态
for iface in en0 en5 en1 lo0; do
  status=$(ifconfig "$iface" 2>/dev/null | grep status)
  [ -n "$status" ] && echo "$iface: $status"
done
```

输出示例：

```
en0: status: active
lo0: status: active
```

如果 `en0` 显示 `status: inactive`，说明 Wi-Fi 已断开或未连接。

## 🚨 常见坑点

| 错误/问题 | 原因 | 解决方法 |
|-----------|------|----------|
| `ifconfig: en0: permission denied` | 修改配置需要 root | 查询不需要 sudo，修改需要加 `sudo` |
| `ifconfig en0 down` 后网络断了 | 禁用了网卡 | 用 `sudo ifconfig en0 up` 重新启用 |
| 找不到 eth0 | macOS 网卡命名和 Linux 不同 | macOS Wi-Fi = `en0`，有线 = `en5`/`en1`，回环 = `lo0` |
| `ifconfig` 输出的 IP 和系统偏好设置不一致 | 可能有 VPN/虚拟网卡 | 检查所有接口，utun* 是 VPN 隧道接口 |
| `inet6` 显示 IPv6 地址但不需要 | macOS 默认开启 IPv6 | 忽略即可，不影响 IPv4 使用 |

> **macOS vs Linux 差异**：Linux 的 ifconfig 通常来自 net-tools 包，输出略有不同。macOS 的 ifconfig 来自 BSD，参数更丰富（如 `ifconfig en0 ether xx:xx:xx:xx:xx:xx` 临时改 MAC 地址）。推荐 macOS 上用 `ifconfig`，Linux 上更推荐用 `ip addr` 替代。

## 🔗 参考链接

- [macOS ifconfig man 手册](https://man.openbsd.org/ifconfig)
- [iproute2（Linux 上的替代工具 `ip addr`）](https://wiki.linuxfoundation.org/networking/iproute2)
