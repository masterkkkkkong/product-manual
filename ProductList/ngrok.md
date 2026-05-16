# ngrok — 内网穿透工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Alan Shreve，后被 ngrok Inc. 收购，现为公开上市工具 |
| 首次发布 | 2013 年 |
| 用途 | 将本地开发服务器暴露到公网，生成一个可公开访问的临时 URL，方便调试 Webhook、移动端联调、分享本地页面给他人 |

## 🎯 用来做什么？

- **Webhook 调试**：开发支付回调、GitHub Webhook、Stripe 通知时，ngrok 让你的本地服务器能被外部服务访问
- **移动端联调**：手机或同事远程访问你本地的开发环境，不需要部署到服务器
- **分享进展**：把本地正在做的页面丢一个公网链接给别人看，省去截图或部署的麻烦
- **HTTPS 测试**：ngrok 自动提供 HTTPS 证书，可以在本地测试需要 HTTPS 的功能

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ngrok http 3000` | 将本地的 3000 端口暴露到公网（最常用） |
| `ngrok http --domain=xxx.ngrok.app 3000` | 使用固定域名（需付费或配置） |
| `ngrok http 8080 -host-header=localhost:8080` | 转发时修改 Host 头（解决虚拟主机问题） |
| `ngrok http 3001 -auth="user:pass"` | 给外部 URL 加上用户名密码认证 |
| `ngrok http 3000 --log=stdout` | 将日志输出到终端（便于调试） |
| `ngrok config check` | 检查配置文件语法 |
| `ngrok version` | 查看 ngrok 版本 |
| `ngrok help` | 查看所有可用命令 |

## 💡 日常使用示例

### 示例 1：用 ngrok 调试 GitHub Webhook

```bash
# 1. 本地启动一个 Python HTTP 服务来接收 Webhook
python3 -m http.server 3000
# Serving HTTP on :: port 3000 (http://[::]:3000/) ...

# 2. 另开一个终端，启动 ngrok
ngrok http 3000

# 3. ngrok 启动后输出类似：
# Forwarding  https://a1b2c3d4.ngrok.app -> http://localhost:3000
# Forwarding  http://a1b2c3d4.ngrok.app -> http://localhost:3000

# 4. 去 GitHub 仓库 Settings → Webhooks → 添加
#    Payload URL: https://a1b2c3d4.ngrok.app
#    Content type: application/json
#    勾选触发事件（如 push、pull_request）

# 5. 现在每次 GitHub 推送事件，你的本地 http.server 就能收到请求了
#    在 ngrok 终端界面还能看到实时请求详情：Method、Header、Body
```

### 示例 2：分享本地前端页面给别人查看

```bash
# 1. 在你本地的 React/Vue 项目目录启动开发服务器
npm run dev
# 输出：Local: http://localhost:5173

# 2. 用 ngrok 暴露这个端口
ngrok http 5173

# 3. 复制 ngrok 生成的 URL（如 https://a1b2c3d4.ngrok.app）
#    发给同事或客户，他们在浏览器打开就能看到你本地的最新效果
#    无需部署，无需打包，所见即所得
```

### 示例 3：给 ngrok URL 加密码保护

```bash
# 开放 Spring Boot 后端调试时，不想让陌生人访问：
ngrok http 8080 -auth="admin:mysecret123"

# 现在访问 https://xxx.ngrok.app 时浏览器会弹登录框
# 只有输入 admin / mysecret123 才能访问
# 适合临时、不安全的调试环境
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| 免费版 URL 每次重启都会变 | 免费用户每次启动 ngrok 会得到一个新的随机域名 | 升级付费版可保留固定域名；或一次启动后不要关闭直到调试完成 |
| ngrok 限制连接数 | 免费版每分钟约 40 个请求的限制 | 付费版无限；或者考虑用 bore、localtunnel 等替代品 |
| macOS 上提示"无法验证开发者" | 首次下载 ngrok 未签名 | 去系统设置 → 隐私与安全 → 仍要打开；或用 `brew install ngrok` 安装 |
| ngrok 的默认连接超时 | ngrok 在 60 秒无活动后可能断开 WebSocket 连接 | 付费版可调整超时；或者在本地用 heartbeat 保持连接 |
| 本机防火墙或代理干扰 | 企业网络限制可能导致 ngrok 连接失败 | 检查是否在公司代理后，需配置 `ngrok config` 中的 http_proxy |
| 本地服务尚未启动就开 ngrok | ngrok 能转发端口但服务不响应，显示 502 Bad Gateway | 先确保 `curl http://localhost:3000` 能正常响应，再启动 ngrok |

## 🔗 参考链接

- [ngrok 官网](https://ngrok.com/)
- [ngrok 下载页面](https://ngrok.com/download)
- [ngrok 官方文档](https://ngrok.com/docs)
- [ngrok GitHub](https://github.com/ngrok/ngrok)
- [bore（开源替代品）](https://github.com/ekzhang/bore)
- [localtunnel（开源替代品）](https://github.com/localtunnel/localtunnel)
