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

||||||| 工具 | 类别 | 简介 | 更新时间 |
|||||||------|------|------|---------|
|||||||| [openssl](./ProductList/openssl.md) | 🔒 安全 | 加密与安全通信工具箱，证书管理、TLS 调试、文件加密、哈希计算 | 2026-06-03 |
|||||||| [gzip](./ProductList/gzip.md) | 🖥 命令行 | 文件压缩与解压，常与 tar 配合打包，是 Linux/macOS 最基础的压缩工具 | 2026-06-03 |
|||||||| [xz](./ProductList/xz.md) | 🖥 命令行 | 高压缩率压缩工具，比 gzip 多省 30-50% 空间，软件包分发和归档首选 | 2026-06-03 |
|||||||| [fzf](./ProductList/fzf.md) | 🖥 命令行 | 命令行模糊搜索神器，从文件、历史命令、管道数据中秒速定位 | 2026-06-02 |
|||||||| [bat](./ProductList/bat.md) | 🖥 命令行 | 带语法高亮的 cat 增强版，自动着色、行号、Git 标注，代码查看利器 | 2026-06-02 |
|||||||| [httpie](./ProductList/httpie.md) | 🖥 命令行 | 人类友好的 HTTP 客户端，彩色输出，一行命令调试 REST API | 2026-06-02 |
|||||||| [nc](./ProductList/nc.md) | 🖥 命令行 | 网络瑞士军刀，端口扫描、文件传输、TCP/UDP 调试 | 2026-06-01 |
|||||||| [nano](./ProductList/nano.md) | 🖥 命令行 | 终端文本编辑器，简单易上手，配置文件修改神器 | 2026-06-01 |
|||||||| [base64](./ProductList/base64.md) | 🖥 命令行 | 二进制编码/解码，文本和二进制互相转换，数据传输和 JWT 解析必备 | 2026-06-01 |
|||||||| [dig](./ProductList/dig.md) | 🖥 命令行 | DNS 查询工具，查域名解析、邮件服务器、排查网络问题，网络诊断必备 | 2026-05-30 |
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
