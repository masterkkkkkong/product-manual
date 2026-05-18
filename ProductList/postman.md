# Postman — API 调试与测试工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Postman Inc. |
| 首次发布 | 2012 年 |
| 用途 | 发送 HTTP 请求、调试 API、编写接口测试、管理 API 文档 |

## 🎯 用来做什么？

- **发送 HTTP 请求**：支持 GET、POST、PUT、DELETE、PATCH 等所有常见方法，快速测试接口
- **管理 API 集合**：将相关请求归入 Collection，方便组织、分享和复用
- **编写接口测试**：内置 JavaScript 脚本引擎，可在请求前后编写断言脚本
- **生成 API 文档**：从 Collection 自动生成美观的接口文档，直接分享给团队
- **环境变量管理**：通过 Environment 切换开发/测试/生产环境，不用改 URL

## 🔧 常用命令

> Postman 本身是 GUI 应用，命令行工具叫 **Newman**（基于 Node.js）。

```bash
# 安装 Newman
npm install -g newman

# 运行一个 Collection（本地 JSON 文件）
newman run my-collection.json

# 运行 Collection 并指定环境文件
newman run my-collection.json -e production-env.json

# 运行 Collection 并带数据文件（CSV/JSON）
newman run my-collection.json -d test-data.csv

# 只输出简要报告（不打印详情）
newman run my-collection.json --reporters cli --reporter-cli-no-summary

# 设置超时时间（毫秒）
newman run my-collection.json --timeout 10000

# 查看 Newman 版本
newman --version

# 查看所有可用选项
newman run --help
```

## 💡 日常使用示例

### 示例 1：用 Postman GUI 测试一个 GET 接口

1. 打开 Postman，点击 **New → HTTP Request**
2. 输入 URL：`https://api.github.com/users/octocat`
3. 点击 **Send**
4. 在下方看到返回的 JSON 数据，包括用户名、头像、仓库数等
5. 查看 **Status: 200 OK**、**响应时间**、**响应大小**

### 示例 2：用 Newman 命令行批量测试

```bash
# 先导出 Collection（在 Postman GUI 中 → 三个点 → Export → Collection v2.1）
# 假设导出为 github-api.json

# 运行测试
newman run github-api.json

# 输出示例
→ GET https://api.github.com/users/octocat
  ✓  Status code is 200
  ✓  Response has user name

  ┌─────────────────────────┬───────────────────┬──────────────────┐
  │                         │           executed │            total │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │          iterations     │                 1 │                1 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │            requests     │                 1 │                1 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │        test-scripts     │                 2 │                2 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │      prerequest-scripts │                 0 │                0 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │          assertions     │                 2 │                2 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │    total run duration   │              912ms │                 │
  ├─────────────────────────┼───────────────────┼──────────────────┤
  │          failed tests   │                 0 │                0 │
  └─────────────────────────┴───────────────────┴──────────────────┘
```

### 示例 3：在 Postman 中写测试脚本

在请求的 **Tests** 标签页里写入：

```javascript
// 检查状态码
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// 检查响应体包含特定字段
pm.test("Response has user name", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.login).to.eql("octocat");
});

// 提取返回数据并设为环境变量
pm.test("Set user id as environment variable", function () {
    const jsonData = pm.response.json();
    pm.environment.set("user_id", jsonData.id.toString());
});
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| Newman 命令找不到 | 未安装 Node.js 或 Newman | 先运行 `node -v` 确认 Node.js 已安装，再 `npm install -g newman` |
| 请求返回 401 Unauthorized | 未添加认证信息 | 在 Authorization 标签页选择对应的认证方式（Bearer Token、Basic Auth 等） |
| 环境变量不生效 | 运行 Newman 时忘了加 `-e` 参数 | 确认使用了 `newman run xxx.json -e env.json` |
| Postman 请求成功但 Newman 失败 | Newman 默认继承系统的 SSL 证书 | 尝试添加 `--insecure` 参数跳过 SSL 验证（仅开发环境） |
| Collection 导入后 URL 变量未解析 | 选择了错误的 Environment | 在右上角下拉菜单中选中正确的 Environment |

## 🔗 参考链接

- [Postman 官网](https://www.postman.com/)
- [Postman 官方文档](https://learning.postman.com/)
- [Newman GitHub 仓库](https://github.com/postmanlabs/newman)
- [Newman 在 npm 上](https://www.npmjs.com/package/newman)
