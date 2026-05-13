# 📋 更新日志 · CHANGELOG

> 记录 ProductList 的每日更新内容。

---

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
