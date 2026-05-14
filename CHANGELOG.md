# 📋 更新日志 · CHANGELOG

> 记录 ProductList 的每日更新内容。

---

## 2026-05-15

### 新增工具（3 个）

| 产品 | 说明 |
|------|------|
| Jupyter Notebook | 交互式编程环境，数据科学和机器学习的标配工具 |
| SSH | 安全远程连接工具，加密登录服务器和执行命令 |
| cron | 定时任务调度工具，自动化执行脚本和运维任务 |

### README 更新

- 新增 Jupyter Notebook、SSH、cron 三条记录（排在最前面）
- 因达到 10 条限制，移除 Obsidian、curl、jq 条目（最旧记录）
- 完整内容仍可通过 `ProductList/` 目录访问

## 2026-05-14

### 新增工具（3 个）

| 产品 | 说明 |
|------|------|
| Homebrew | macOS 包管理器，一键安装和更新开发工具 |
| Docker | 容器化应用平台，消除环境差异 |
| VS Code | 轻量级代码编辑器，丰富的扩展生态 |

### README 更新

- 新增 Homebrew、Docker、VS Code 三条记录（排在最前面）
- 因达到 10 条限制，移除 Python venv 条目（最旧记录）
- 完整内容仍可通过 `ProductList/` 目录访问

## 2026-05-13

### 结构调整
- **重构目录结构**：所有产品手册从独立文件夹改为统一放在 `ProductList/` 目录下，每个产品一个 `.md` 文件
- **新增 `CHANGELOG.md`**：在 `ProductList/` 同级目录，记录每天的更新内容
- **更新 README.md**：链接改为指向 `ProductList/` 下的新路径

### 已有产品（8个）
| 产品 | 说明 |
|------|------|
| curl | 命令行网络请求工具 |
| git | 分布式版本控制系统 |
| jq | JSON 命令行处理工具 |
| markdown | 轻量级标记语言 |
| npm | Node.js 包管理器 |
| obsidian | 本地知识管理工具 |
| pip | Python 包管理器 |
| python-venv | Python 虚拟环境工具 |

### README 优化
- **目录精简**：README 中的产品列表改为只展示**最近更新的前10个**，加上了 `更新时间` 列
- **完整列表**：查看更多请直接访问 `ProductList/` 目录和 `CHANGELOG.md`
- **约定**：以后每天新增工具时，README 只保留最新的10条记录，新增的排在最前面
