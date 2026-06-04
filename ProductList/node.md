# Node.js — JavaScript 运行时环境

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | OpenJS Foundation (Ryan Dahl 创始) |
| 首次发布 | 2009 年 |
| 用途 | 在服务端运行 JavaScript，构建后端 API、CLI 工具、前端构建流水线 |

## 🎯 用来做什么？

- **构建后端服务** — 用 Express / Koa / Fastify 写 REST API、GraphQL 服务
- **CLI 工具** — 替代 Shell 脚本处理批量任务，跨平台一致性更好
- **前端生态基础** — npm/yarn/pnpm 管理依赖，Vite/Webpack 打包代码
- **原型快速验证** — 不需要配置，一行 `node -e` 就能跑逻辑

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `node -v` | 查看版本号 |
| `node script.js` | 运行 JS 文件 |
| `node -e "console.log(1+1)"` | 直接执行内联代码（无需文件） |
| `node --inspect script.js` | 开启调试模式，连接 Chrome DevTools |
| `node --watch script.js` | 文件变动自动重启（Node 18+） |
| `node --help` | 查看全部命令行选项 |
| `node -p "1+1"` | `-e` 的简写 + 自动打印结果 |

## 💡 日常使用示例

### 例 1：快速计算与验证
```bash
# 不用写文件，直接算
node -e "
const prices = [29, 49, 99, 199];
const total = prices.reduce((a, b) => a + b, 0);
console.log('总计:', total, '含税:', total * 1.13);
"
```
输出：
```
总计: 376 含税: 424.88
```

### 例 2：读取 CSV 文件并统计
```bash
# 假设有一个 sales.csv
node -e "
const fs = require('fs');
const data = fs.readFileSync('sales.csv', 'utf-8');
const lines = data.trim().split('\n').slice(1); // 跳过表头
let total = 0;
for (const line of lines) {
  total += parseFloat(line.split(',')[2]); // 第三列是金额
}
console.log('总销售额:', total);
"
```

### 例 3：启动一个简单的 HTTP 服务器
```bash
# 一行启动静态文件服务器（需要先 npm install http-server）
npx http-server . -p 3000

# 或者用 Node 内置模块，无需安装
node -e "
const http = require('http');
http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(3000, () => console.log('Server running at http://localhost:3000'));
"
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `require is not defined` | 使用 ESM（.mjs 文件或 package.json 配了 `"type": "module"`） | 改用 `import` 语法，或用 `.cjs` 后缀强制 CommonJS |
| 端口被占用 `EADDRINUSE` | 上个进程没关闭 | `lsof -i :3000` 找到 PID，`kill -9 PID` |
| `npm install` 报权限错误 | 全局安装没加 `sudo` 或 nvm 没配好 | 用 nvm 管理 Node 版本，从不用 sudo 安装 |
| 中文乱码 | 文件保存为 GBK 而非 UTF-8 | 用 UTF-8 保存，或加 `// @ts-check` 头 |
| macOS 上 `node --watch` 不好使 | macOS 的文件事件通知在 Docker/CI 环境下有限制 | 用 `nodemon` 替代：`npx nodemon script.js` |

## 🔗 参考链接

- [Node.js 官网](https://nodejs.org/)
- [Node.js API 文档](https://nodejs.org/api/)
- [npm 官方文档](https://docs.npmjs.com/)