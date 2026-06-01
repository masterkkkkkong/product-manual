# HTTPie — 人类友好的 HTTP 请求工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Jakub Roztocil |
| 首次发布 | 2012 年 |
| 仓库 | https://github.com/httpie/cli |
| 官网 | https://httpie.io/ |
| 用途 | 比 curl 更直观的命令行 HTTP 客户端，支持 JSON、语法高亮、会话管理 |

## 🎯 用来做什么？

- **调试 API 接口**：发送 GET/POST/PUT/DELETE 请求，彩色输出直观易读
- **测试 RESTful API**：一行命令即可发 JSON 请求，自动处理 Content-Type 头
- **文件上传**：模拟 multipart/form-data 表单上传
- **会话管理**：跨请求保持 Cookie 和认证状态，方便测试登录流程

## 🔧 常用命令

```bash
# 1. GET 请求
http https://api.github.com

# 2. POST JSON 数据（自动设置 Content-Type: application/json）
http POST https://httpbin.org/post name=john age:=30

# 3. POST 表单数据
http -f POST https://httpbin.org/post name=john age=30

# 4. PUT 更新资源
http PUT https://httpbin.org/put id:=1 name=jane

# 5. DELETE 请求
http DELETE https://httpbin.org/delete

# 6. 设置请求头
http GET https://httpbin.org/headers Authorization:"Bearer token123"

# 7. 下载文件
http --download https://example.com/file.zip

# 8. 查看完整请求和响应（verbose 模式）
http -v GET https://httpbin.org/get
```

## 💡 日常使用示例

### 示例 1：快速查看 GitHub API

```bash
# 查看某用户的信息
http GET https://api.github.com/users/torvalds

# 输出效果：JSON 自动语法高亮 + 带行号 + HTTP 状态码和头信息一目了然
```

输出示例（文字描述）：
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
...

{
    "login": "torvalds",
    "id": 1024025,
    "type": "User",
    "name": "Linus Torvalds",
    "public_repos": 6,
    "followers": 229000,
    ...
}
```

### 示例 2：POST JSON 数据到测试 API

```bash
# 发 JSON 请求，httpie 会自动推断类型
http POST https://httpbin.org/post \
    title="Hello World" \
    body="This is a test" \
    userId:=1

# 注意：
#   key=value    → 字符串（JSON string）
#   key:=value   → 原始值（JSON number/boolean/null）
#   key:=@file   → 从文件读取 JSON

# 等价于 curl：
# curl -X POST https://httpbin.org/post \
#   -H "Content-Type: application/json" \
#   -d '{"title":"Hello World","body":"This is a test","userId":1}'
```

### 示例 3：带认证的 API 请求

```bash
# 使用 Basic Auth
http -a username:password GET https://httpbin.org/basic-auth/username/password

# 使用 Bearer Token
http GET https://api.github.com/user Authorization:"Bearer ghp_xxxxx"

# 或者用冒号简写（自动 Basic Auth）
http -a : GET https://httpbin.org/basic-auth/user/pass
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `http: command not found` | 没有安装 | `brew install httpie` |
| POST 数据变成了 URL 参数 | 忘记写请求方法 | 显式写 `http POST http://...`（不写方法默认为 GET） |
| JSON 数字传成了字符串 | 用了 `=` 而不是 `:=` | 数字用 `key:=123`，布尔用 `key:=true`，null 用 `key:=null` |
| 响应内容太大输出被截断 | 默认输出有长度限制 | 用 `http --print=B` 只输出 body 部分，或管道给 less |
| macOS 上 Python 版本冲突 | httpie 依赖 Python 3 | 用 brew 安装，不要用 pip 全局安装 |
| 自签名证书报错 | httpie 默认验证 SSL | 加 `--verify=no` 跳过证书验证 |

## 🔗 参考链接

- [官网 & 文档](https://httpie.io/docs/cli)
- [GitHub 仓库](https://github.com/httpie/cli)
- [Homebrew 安装](https://formulae.brew.sh/formula/httpie)
- [在线交互教程](https://httpie.io/run)