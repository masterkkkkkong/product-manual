# dig — DNS 查询工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Internet Systems Consortium (ISC) |
| 首次发布 | 1990s（BIND 发行版的一部分） |
| 用途 | 向 DNS 服务器发送域名查询请求，获取域名解析、邮件服务器、域名服务商等信息 |

## 🎯 用来做什么？

- **域名解析排查** — 查某个域名指向哪个 IP，确认 DNS 是否生效
- **邮件服务器查询** — 查某个域名的 MX 记录，确认邮件发到哪里
- **域名信息审计** — 查 NS（域名服务器）、TXT（文本记录，含 SPF/DKIM）、CNAME（别名）等各类记录
- **DNS 问题诊断** — 确认是不是 DNS 缓存、TTL、或者上游解析出了问题

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `dig example.com` | 查询域名的 A 记录（IPv4 地址） |
| `dig example.com AAAA` | 查询域名的 AAAA 记录（IPv6 地址） |
| `dig example.com MX` | 查询邮件交换记录 |
| `dig example.com NS` | 查询域名服务器记录 |
| `dig example.com TXT` | 查询文本记录（SPF、DKIM 验证等） |
| `dig example.com CNAME` | 查询别名记录 |
| `dig -x 8.8.8.8` | 反向查询（IP → 域名） |
| `dig @8.8.8.8 example.com` | 指定 DNS 服务器查询（绕过本地缓存） |
| `dig example.com +short` | 只输出精简结果（无冗余信息） |
| `dig example.com +trace` | 跟踪完整解析链路（根服务器 → 顶级域 → 权威服务器） |

## 💡 日常使用示例

### 示例 1：查域名对应的 IP 地址

```bash
$ dig github.com +short
140.82.112.3
```

`+short` 让输出干净利落，只返回 IP。不加的话会显示完整的 DNS 报文，包含 query time、server 等信息。

### 示例 2：查某个域名的 MX 记录（邮件服务器）

```bash
$ dig gmail.com MX +short
20 alt2.gmail-smtp-in.l.google.com.
30 alt3.gmail-smtp-in.l.google.com.
40 alt4.gmail-smtp-in.l.google.com.
10 alt1.gmail-smtp-in.l.google.com.
5  gmail-smtp-in.l.google.com.
```

数字是优先级，越小越优先。这里 `5 gmail-smtp-in.l.google.com.` 是主邮件服务器。

### 示例 3：用 Google DNS 排查本地缓存问题

```bash
$ dig @8.8.8.8 example.com +short
93.184.216.34
```

本地 DNS 可能有缓存，直接查 `8.8.8.8`（Google Public DNS）跳过中间环节。同样适合用来验证你刚改的 DNS 记录是否已在全球生效（虽然 TT L 还有时间）。

### 示例 4：反向查询（从 IP 找域名）

```bash
$ dig -x 8.8.8.8 +short
dns.google.
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `;; connection timed out; no servers could be reached` | 网络不通或 DNS 服务器无响应 | 换一个 DNS 服务器试试，如 `@1.1.1.1` 或 `@8.8.8.8` |
| 查到的 IP 和浏览器打开的不一样 | 可能是 CDN 根据地区返回不同 IP，或者浏览器有 HTTP/3（QUIC）缓存 | `dig` 查的是 DNS 层，跟 HTTP 层可能不同。用 `curl -v` 对比 |
| `+short` 没输出，不加却有结果 | 查询的域名没有该类型的记录，但不报错 | 检查记录类型是否正确。例如查 `dig example.com MX +short` 没有输出说明该域名没有 MX 配置 |
| macOS 和 Linux 输出的格式不同 | 系统自带的 dig 版本不同 | 参数行为一致，只是排版略有差异。关键看 ANSWER SECTION 那几行 |

## 🔗 参考链接

- [BIND 9 官方文档](https://bind9.readthedocs.io/)
- [Linux man page: dig](https://man.archlinux.org/man/dig.1)