# npx — Node.js 包执行器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | npm, Inc. (GitHub / Microsoft) |
| 首次发布 | 2017 年（随 npm@5.2.0 发布） |
| 用途 | 无需安装即可运行 npm 包中的命令 |
| 官网 | [docs.npmjs.com/cli/v10/commands/npx](https://docs.npmjs.com/cli/v10/commands/npm-exec) |

## 🎯 用来做什么？

- **免安装运行 CLI 工具** — `npx cowsay` 自动下载并执行，用完即删
- **临时尝鲜** — 想试一个工具但不想污染全局，`npx` 一把梭
- **固定版本** — `npx create-react-app@5` 确保用指定版本
- **不用全局装包** — 团队项目里用 `npx` 跑构建工具，避免版本冲突

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `npx cowsay hello` | 直接运行 cowsay（自动安装再执行） |
| `npx http-server` | 启动静态文件服务器 |
| `npx create-react-app my-app` | 创建 React 项目（不装全局 CLI） |
| `npx prettier --write .` | 格式化代码（项目内有 prettier 时用本地版本） |
| `npx eslint src/` | 运行 ESLint 检查 |
| `npx tsc --init` | 初始化 TypeScript 配置 |
| `npx --package=typescript tsc --version` | 指定包名运行（命令名和包名不同时） |
| `npx --yes some-package` | 跳过「是否安装」确认 |

## 💡 日常使用示例

### 示例 1：一句话生成 ASCII 艺术字

```bash
# 不需要提前装任何东西
npx cowsay "Hello, Product Manual!"
```

输出：

```
 ________________________
< Hello, Product Manual! >
 ------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

### 示例 2：用 http-server 快速共享文件夹

```bash
# 进入要分享的目录
cd ~/Documents
npx http-server -p 3000
# 浏览器打开 http://localhost:3000 即可浏览文件
```

对比 `python3 -m http.server`，`http-server` 支持自动刷新、目录列表更美观，适合前端开发调试。

### 示例 3：运行项目本地工具，不用全局安装

很多项目在 `devDependencies` 里装了 `prettier`、`eslint`、`typescript` 等工具。用 `npx` 自动使用项目内的本地版本：

```bash
# 用项目本地安装的 prettier 格式化（而不是系统全局的）
cd my-project
npx prettier --check src/
npx prettier --write src/
```

如果项目里没装，npx 会自动从 npm registry 下载最新版运行，然后缓存起来。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `npx: command not found` | 没装 Node.js / npm | 装 Node.js（`brew install node`） |
| npx 提示「是否安装」 | 默认需要确认 | 加 `--yes` 或 `-y` 跳过确认 |
| 运行的是旧版本 | npx 默认用项目本地版本 | 指定版本：`npx prettier@3.0.0` |
| 网络慢或下载失败 | npx 需要联网下载包 | 检查网络，或先 `npm install -g pkg` 全局装好 |
| npx 和 npm exec 的关系 | 在 npm@7+ 中 npx 是 `npm exec` 的别名 | 两者等价，习惯用短的 `npx` 即可 |

> 💡 **小技巧**：npx 下载的包缓存到 `~/.npm/_npx/`，一段时间内重复使用不会重复下载。可以用 `npx --cache /tmp/cache` 指定缓存目录。

## 🔗 参考链接

- [npm exec 官方文档](https://docs.npmjs.com/cli/v10/commands/npm-exec)
- [npx 介绍博客（npm 官方）](https://docs.npmjs.com/cli/v10/commands/npx/)
- [Node.js 官网](https://nodejs.org/)
