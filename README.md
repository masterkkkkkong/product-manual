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

|||||||||| 工具 | 类别 | 简介 | 更新时间 |
||||||||||------|------|------|---------|
||||| [dig](./ProductList/dig.md) | 🖥 命令行 | DNS 查询工具，查域名解析、邮件服务器、排查网络问题，网络诊断必备 | 2026-05-30 |
||||| [zip & unzip](./ProductList/zip-unzip.md) | 🖥 命令行 | 文件压缩与解压，打包分享和归档，跨平台最通用的压缩格式 | 2026-05-30 |
||||| [mdfind](./ProductList/mdfind.md) | 🖥 命令行 | macOS Spotlight 命令行搜索，比 find 快几十倍，按元数据精准定位文件 | 2026-05-30 |
||||| [less](./ProductList/less.md) | 🖥 命令行 | 文件分页查看器，大文件秒开，支持搜索/翻页/行号跳转，替代猫和尾巴的全能选手 | 2026-05-29 |
||||| [ps](./ProductList/ps.md) | 🖥 命令行 | 进程状态查看器，显示系统中所有进程的 PID、CPU/内存占用，诊断卡顿的起点 | 2026-05-29 |
||||| [df & du](./ProductList/df-du.md) | 🖥 命令行 | 磁盘空间分析工具，df 看分区还剩多少，du 找谁吃掉了空间 | 2026-05-29 |
||||| [open](./ProductList/open.md) | 🖥 命令行 | macOS 原生打开工具，从终端打开文件、目录、URL，Finder 和图形应用无缝切换 | 2026-05-28 |
||||| [time](./ProductList/time.md) | 🖥 命令行 | 命令执行时间测量工具，分 real/user/sys 三个维度分析性能，脚本优化必备 | 2026-05-28 |
||||| [pbcopy/pbpaste](./ProductList/pbcopy-pbpaste.md) | 🖥 命令行 | macOS 剪贴板 CLI，终端和系统剪贴板之间的数据传输桥梁 | 2026-05-28 |
||||| [cat](./ProductList/cat.md) | 🖥 命令行 | 文件内容查看与拼接，快速预览、合并文件、创建脚本 | 2026-05-27 |
|
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
