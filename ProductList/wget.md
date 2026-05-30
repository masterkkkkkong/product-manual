# wget — 命令行网络文件下载工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU 项目 / Hrvoje Nikšić |
| 首次发布 | 1996 年 |
| 用途 | 从网络下载文件，支持 HTTP/HTTPS/FTP，支持递归下载、断点续传 |

> macOS 默认不安装 wget，需要 `brew install wget` 安装。GNU Wget 1.25.0 是目前最新稳定版。

## 🎯 用来做什么？

- **下载文件** — 从 URL 直接下载单个文件，比浏览器更高效
- **递归下载整站** — 带 `-r` 或 `-m` 可以把整个网站镜像到本地
- **断点续传** — 下载中断后 `-c` 接着下，不用重新开始
- **批量下载** — 配合 `-i` 从文件中读取多个 URL 依次下载
- **后台下载** — `-b` 让下载跑到后台，不占终端

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `wget URL` | 下载文件（默认用 URL 最后的文件名保存） |
| `wget -O 文件名 URL` | 下载并指定输出文件名 |
| `wget -c URL` | 断点续传 |
| `wget -q URL` | 安静模式（不输出日志） |
| `wget -P 目录 URL` | 下载到指定目录 |
| `wget -i url.txt` | 从文件中读取多个 URL 批量下载 |
| `wget -r -l 2 URL` | 递归下载，最大深度 2 层 |
| `wget -m URL` | 镜像整个网站（`-N -r -l inf` 的简写） |
| `wget -t 5 URL` | 设置重试次数（0=无限重试） |
| `wget --limit-rate=200k URL` | 限制下载速度（单位：k/M） |

## 💡 日常使用示例

### 示例 1：下载单个文件并重命名

```bash
# 直接下载，文件名为 random-data.json
$ wget https://httpbin.org/json
--2026-05-31 01:00:01--  https://httpbin.org/json
正在解析主机 httpbin.org (httpbin.org)... 3.220.122.222
正在连接 httpbin.org (httpbin.org)|3.220.122.222|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：429 [application/json]
正在保存至: “json”

json  100%[===================>]     429  --.-KB/s  用时 0s

已保存 “json” [429/429]

# 指定输出文件名
$ wget -O mydata.json https://httpbin.org/json
```

### 示例 2：断点续传（下载中断也不怕）

```bash
# 假设下载一个大文件中途断开了
$ wget -c https://example.com/bigfile.zip
# -c 会检查本地已有文件的大小，从断点处继续下载
# 如果本地没有对应的 .part 文件，则重新开始
```

### 示例 3：从文件读取 URL 列表批量下载

```bash
# 准备 URL 列表
$ cat urls.txt
https://httpbin.org/robots.txt
https://httpbin.org/json
https://httpbin.org/html

# 批量下载，安静模式，指定目录
$ wget -q -i urls.txt -P ./downloads/
$ ls downloads/
html    json    robots.txt
```

### 示例 4：镜像网站到本地

```bash
# 递归镜像（不追溯父目录）
$ wget -m -np https://example.com/docs/

# 转换链接为本地版本（离线浏览）
$ wget -m -k https://example.com/docs/
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `command not found: wget` | macOS 未预装 wget | `brew install wget` |
| 下载的文件名奇怪 | URL 以 `?` 结尾导致 wget 用默认名 | 用 `-O 文件名` 指定输出名 |
| 下载到一半断开了 | 网络波动或服务器超时 | 加 `-c` 断点续传，加 `-t 10` 增加重试 |
| HTTPS 证书报错 | 某些自签证书不被信任 | `wget --no-check-certificate URL`（不推荐） |
| 递归下载下载了整个互联网 | 没加 `-l` 限制深度，或没加 `-np` | 加 `-l 2 -np` 限制深度并禁止回溯父目录 |
| macOS 上进度条不显示 | macOS Terminal 默认字符集问题 | 加 `--show-progress` 强制显示 |

## 🔗 参考链接

- [GNU Wget 官方文档](https://www.gnu.org/software/wget/manual/)
- [Homebrew wget 包](https://formulae.brew.sh/formula/wget)