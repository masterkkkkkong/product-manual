# mdfind — macOS Spotlight 命令行搜索工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | macOS 10.4 Tiger (2005) |
| 用途 | 通过 Spotlight 索引在 macOS 上极速搜索文件，比 `find` 快几十倍 |

## 🎯 用来做什么？

- **极速搜文件** — 搜文件名、文件内容、关键词，结果秒出（基于预建索引）
- **按属性过滤** — 根据文件类型、作者、创建日期、图片尺寸等元数据精准定位
- **替代 Finder 搜索** — 在终端里就能完成 Spotlight 级别的搜索，适合脚本和自动化
- **快速找配置文件** — 比如 `mdfind -name .env` 找所有名字包含 `.env` 的文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `mdfind 关键词` | 搜索文件名和内容中包含关键词的文件 |
| `mdfind -name 文件名` | 只搜索文件名（更快、更精准） |
| `mdfind -onlyin ~/Documents/ 关键词` | 限定搜索范围到某个目录 |
| `mdfind -count 关键词` | 只返回匹配的文件数量 |
| `mdfind "kMDItemKind == 'PDF'"` | 按文件类型搜索 |
| `mdfind "kMDItemFSName == '*.py' && kMDItemContentModificationDate >= \$time.today(-7)"` | 最近 7 天修改的 Python 文件 |
| `mdfind -0 关键词 \| xargs -0 open` | 用 NUL 分隔结果，交给其他命令处理 |

## 💡 日常使用示例

### 示例 1：按文件名快速搜索

```bash
# 搜所有文件名含 "report" 的文件
$ mdfind -name report
/Users/mk/Desktop/sales-report-2026.xlsx
/Users/mk/Documents/meeting-notes/report.txt
/Users/mk/Library/Mobile Documents/com~apple~Numbers/Weekly Report.numbers

# 限定到 Documents 目录
$ mdfind -onlyin ~/Documents/ -name report
/Users/mk/Documents/meeting-notes/report.txt
```

不加 `-onlyin` 时 mdfind 会搜索整个系统的 Spotlight 索引，范围很大。建议每次都加。

### 示例 2：搜索包含特定内容的文件

```bash
# 搜所有内容包含 "API_KEY" 的文件（小心暴露密钥！）
$ mdfind API_KEY
/Users/mk/projects/myapp/.env
/Users/mk/projects/myapp/config.yaml
/Users/mk/projects/myapp/README.md
```

mdfind 不仅搜文件名，还会搜文件内容（对文本文件有效）。适合找配置项、你记得的代码片段。

### 示例 3：按文件类型搜索

```bash
# 搜所有图片文件
$ mdfind "kMDItemKind == 'Image'" -onlyin ~/Pictures/ | head -5
/Users/mk/Pictures/screenshot.png
/Users/mk/Pictures/photo.jpg
/Users/mk/Pictures/diagram.png
/Users/mk/Pictures/logo.svg
/Users/mk/Pictures/banner.gif

# 搜最近一周修改的 Markdown 文件
$ mdfind "kMDItemFSName == '*.md' && kMDItemContentModificationDate >= \$time.today(-7)" | head -5
/Users/mk/obsidian_files/obs_repository/Product manual/ProductList/dig.md
/Users/mk/obsidian_files/obs_repository/Product manual/ProductList/zip-unzip.md
```

### 示例 4：统计匹配数量

```bash
# 看看系统里有多少个 PDF 文件
$ mdfind -count "kMDItemKind == 'PDF'"
14327
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| 搜不到刚保存的文件 | Spotlight 索引需要时间更新 | 等几十秒再搜，或者用 `mdutil -E /` 强制重建索引 |
| `mdfind` 搜不到外置硬盘/网络盘的内容 | 默认只索引内置磁盘 | 去系统设置 → Spotlight → 搜索隐私 确认路径已被包含 |
| 看起来搜索范围太大、结果太多 | 没有加 `-onlyin` 限制目录 | 养成习惯：`mdfind -onlyin <目标目录> 关键词` |
| `kMDItem*` 属性记不住 | 苹果元数据属性很多 | 用 `mdls 文件名` 查看某个文件的完整元数据属性列表，照着写 kMDItem 的条件 |
| mdfind 搜内容不如 grep 细 | mdfind 搜的是索引，不是逐字匹配 | 确定文件在哪个目录后用 `grep -r` 做精确匹配 |

### 小提示：用 `mdls` 查看文件元数据

想知道某个文件有哪些可搜索的属性？用 `mdls`：

```bash
$ mdls ~/Documents/report.pdf
kMDItemContentCreationDate     = 2026-05-28 10:00:00 +0000
kMDItemContentModificationDate = 2026-05-29 14:30:00 +0000
kMDItemKind                    = "PDF"
kMDItemFSName                  = "report.pdf"
kMDItemFSSize                  = 2345678
kMDItemTitle                   = "Quarterly Report"
...
```

这些 `kMDItem*` 属性名可以直接用在 mdfind 的查询条件里。

## 🔗 参考链接

- [Apple 开发者文档: mdfind](https://ss64.com/mac/mdfind.html)
- [macOS 终端命令速查：mdfind](https://developer.apple.com/library/archive/documentation/Darwin/Reference/ManPages/man1/mdfind.1.html)
- [用 mdls 查询文件元数据](https://ss64.com/mac/mdls.html)