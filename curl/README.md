# curl — 命令行网络请求工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Daniel Stenberg |
| 首次发布 | 1997 年 |
| 用途 | 通过命令行发送 HTTP、HTTPS、FTP 等各种网络请求 |
| macOS 自带 | ✅ 是（预装在 macOS 中） |

## 🎯 用来做什么？

- **发送 HTTP 请求**：GET、POST、PUT、DELETE 等，测试 API 接口
- **下载文件**：从网络下载文件，支持断点续传
- **检查响应头和状态码**：排查网络请求问题
- **脚本化网络操作**：在 Shell 脚本中集成 API 调用

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `curl <url>` | 发送 GET 请求，输出响应内容到终端 |
| `curl -o file.txt <url>` | 下载文件并保存为指定文件名 |
| `curl -O <url>` | 下载文件并保持远程文件名 |
| `curl -s <url>` | 静默模式，不显示进度条和错误信息 |
| `curl -I <url>` | 只获取响应头（HEAD 请求） |
| `curl -d "key=value" <url>` | 发送 POST 请求，带表单数据 |
| `curl -H "Header: value" <url>` | 自定义请求头 |
| `curl -v <url>` | 详细模式，显示完整的请求和响应过程 |
| `curl -f <url>` | 失败时（HTTP 错误码）不输出响应体，返回非零退出码 |
| `curl -w "%{http_code}" <url>` | 输出指定的格式信息（如 HTTP 状态码） |

## 💡 日常使用示例

### 示例 1：测试 API 接口（GET 请求）

```bash
# 请求一个公开的 API，获取 JSON 数据
curl -s https://api.github.com/repos/curl/curl

# 输出片段（节选）：
# {
#   "name": "curl",
#   "full_name": "curl/curl",
#   "description": "A command line tool and library for transferring data with URL syntax",
#   "stargazers_count": 36000,
#   "language": "C",
#   ...
# }
```

### 示例 2：发送 POST 请求并检查状态码

```bash
# 向测试服务器发送 POST 数据
curl -s -o /dev/null -w "HTTP状态码: %{http_code}\n" \
  -d "name=test&value=hello" \
  https://httpbin.org/post

# 输出：
# HTTP状态码: 200
```

分解说明：
- `-o /dev/null`：把响应体丢弃，只看状态码
- `-w "%{http_code}"`：输出 HTTP 状态码
- `-d`：发送 POST 表单数据

### 示例 3：下载文件并显示进度

```bash
# 下载一个文件，显示进度条，保存到本地
curl -o logo.png -# https://curl.se/logo/curl-logo.svg

# -o logo.png   → 保存为 logo.png
# -#            → 用进度条代替默认的进度显示
```

如果要保持远程文件名，直接用 `-O`：

```bash
curl -OL https://curl.se/logo/curl-logo.svg
# -O 自动用远程文件名 curl-logo.svg 保存
# -L 跟随重定向（很多下载链接会跳转）
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **SSL 证书错误** | `SSL certificate problem` | macOS 上一般正常，如果遇到添加 `-k` 跳过验证（不推荐生产环境使用） |
| **重定向问题** | 下载到的是 HTML 而不是目标文件 | 加 `-L` 参数自动跟随重定向 |
| **中文乱码** | 响应内容显示乱码 | 用 `-o file` 保存到文件后用编辑器打开，或通过管道传给 `cat` |
| **后台运行的 curl 没有输出** | 在脚本里用 `-s` 但没看到结果 | curl 默认输出到 stdout，确保没有重定向到 /dev/null |
| **POST 数据包含特殊字符** | 请求失败或数据不完整 | 用 `--data-urlencode` 替代 `-d` 自动编码 |
| **超时** | 请求卡住很久不返回 | 加 `--connect-timeout 10` 和 `--max-time 30` 限制连接和总超时时间 |

## 🔗 参考链接

- [curl 官方文档](https://curl.se/docs/)
- [httpbin.org — 测试 HTTP 请求的在线服务](https://httpbin.org/)
- [Everything curl — 在线书籍](https://everything.curl.dev/)
