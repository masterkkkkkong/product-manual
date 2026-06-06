# 📖 产品手册 · Product Manual

> 常见开发工具的使用手册，面向初学者，每天更新。

## 这是什么？

这是一个持续更新的**产品说明书集合**，收录各种常用开发工具、命令行工具、编程工具的使用指南。

每篇手册包含：
- 📌 **工具简介**（谁开发的、用来做什么）
- 🔧 **常用命令速查**
- 💡 **完整的实战例子**（复制粘贴就能跑）
- 🚨 **常见坑点**（帮你少踩坑）
- 🔗 **参考链接**

## 为什么做这个？

工具太多，命令记不住——这是每个开发者的日常。这个仓库就是你的"第二大脑"，忘了就来翻翻。

## 📂 最新上架（最近10个）

> 完整列表见 [ProductList/](./ProductList/) 目录 · CHANGELOG 见 [CHANGELOG.md](./CHANGELOG.md)

| 工具 | 类别 | 简介 | 更新时间 |
|------|------|------|---------|
| [plutil](./ProductList/plutil.md) | 🖥 命令行 | macOS plist 文件检查与转换工具，检查语法、读写字段、转换 JSON/XML/binary plist | 2026-06-07 |
| [osascript](./ProductList/osascript.md) | 🖥 命令行 | 在终端运行 AppleScript/JXA，自动控制 macOS 应用和系统动作 | 2026-06-07 |
| [softwareupdate](./ProductList/softwareupdate.md) | 🖥 命令行 | macOS 系统更新命令行工具，检查、下载、安装系统更新和完整安装器 | 2026-06-07 |
| [launchctl](./ProductList/launchctl.md) | 🖥 命令行 | macOS 服务/守护进程管理工具，控制 LaunchAgents 和 LaunchDaemons | 2026-06-06 |
| [caffeinate](./ProductList/caffeinate.md) | 🖥 命令行 | macOS 防休眠工具，长时间下载、编译、渲染时保持系统清醒 | 2026-06-06 |
| [defaults](./ProductList/defaults.md) | 🖥 命令行 | macOS 偏好设置命令行工具，读写隐藏系统和应用配置 | 2026-06-06 |
| [Node.js](./ProductList/node.md) | 🐍 编程语言 | JavaScript 运行时，让 JS 脱离浏览器运行，前后端开发和命令行工具生态核心 | 2026-06-05 |
| [sips](./ProductList/sips.md) | 🖥 命令行 | macOS 自带图片处理工具，查看、缩放、旋转和格式转换图片，无需安装额外软件 | 2026-06-05 |
| [textutil](./ProductList/textutil.md) | 🖥 命令行 | macOS 文档格式转换工具，在 txt、rtf、html、docx 等格式之间批量转换 | 2026-06-05 |
| [ping](./ProductList/ping.md) | 🖥 命令行 | 网络连通性测试工具，检测网络通断、延迟和丢包率，网络诊断第一步 | 2026-06-04 |

> 每天新增 2-3 个工具 🔄

## 🎯 适合谁看？

- **刚入门的新手**——想学工具但不知道从哪开始
- **用得多记得少的老手**——命令记不住，需要速查
- **想系统学习的人**——每天学一个新工具，积少成多

## 🔍 如何使用

### 方式一：在 GitHub 上直接浏览
点开 `ProductList/` 目录，每个工具一个 Markdown 文件，内容就是使用手册。`README.md` 是本仓库的索引。

### 方式二：克隆到本地
```bash
git clone git@github.com:masterkkkkkong/product-manual.git
cd product-manual
# ProductList/ 目录下是所有工具手册
# 直接用浏览器或编辑器打开即可
```

### 方式三：搭配 Obsidian 使用
如果你在用 Obsidian 做笔记，直接把这个仓库作为你的笔记库的一部分，搜索和跳转都很方便。

## 📅 更新计划

每天上午更新，每次新增 2-3 个工具，覆盖：

- 🖥 **命令行工具**（curl, jq, tmux, ssh...）
- 🐍 **编程语言工具**（venv, conda, pytest...）
- 🐳 **开发环境**（Docker, Homebrew, VS Code...）
- 🗄 **数据库**（SQLite, MySQL, Redis...）
- 🔧 **效率工具**（Chrome DevTools, Postman, ngrok...）
- 🤖 **AI 工具**（Ollama, Hugging Face, LangChain...）

## 🤝 贡献

欢迎提 Issue 或 PR 补充更多工具手册！

## 📄 许可

MIT
