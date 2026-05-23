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

|||| 工具 | 类别 | 简介 | 更新时间 |
||||------|------|------|---------|
|||||| [awk](./ProductList/awk.md) | 🖥 命令行 | 文本模式扫描与处理语言，按字段提取、统计、格式化，日志分析和 CSV 处理利器 | 2026-05-24 |
|||||| [sed](./ProductList/sed.md) | 🖥 命令行 | 流编辑器，批量查找替换文本、删除/提取行，不打开编辑器就能改文件 | 2026-05-24 |
|||||| [tar](./ProductList/tar.md) | 🖥 命令行 | 文件归档与压缩工具，将多文件打包为单文件并压缩，备份和分发的标配 | 2026-05-24 |
|||||| [ripgrep](./ProductList/ripgrep.md) | 🔧 开发工具 | 超快的代码搜索工具，比 grep 快 5-10 倍，默认遵守 .gitignore | 2026-05-22 |
|||||| [pytest](./ProductList/pytest.md) | 🐍 编程语言 | Python 最流行的测试框架，自动发现测试、丰富的断言、插件生态 | 2026-05-22 |
|||||| [lsof](./ProductList/lsof.md) | 🖥 命令行 | 列出系统打开的文件和网络连接，查端口谁占了的终极工具 | 2026-05-22 |
|||||| [FFmpeg](./ProductList/ffmpeg.md) | 🎬 音视频工具 | 音视频处理瑞士军刀，转码、剪切、合并、提取音频，支持几乎所有格式 | 2026-05-21 |
|||||| [Make](./ProductList/make.md) | 🔧 开发工具 | 构建自动化工具，通过 Makefile 一键编译、测试、部署 | 2026-05-21 |
|||||| [Screen](./ProductList/screen.md) | 🖥 命令行 | 终端会话管理工具，断开后任务继续运行，支持多窗口和回连 | 2026-05-21 |
|||||| [Ollama](./ProductList/ollama.md) | 🤖 AI 工具 | 本地运行大模型，一条命令下载并对话 LLM，完全离线免费 | 2026-05-20 |
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
