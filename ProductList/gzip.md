# gzip — 文件压缩与解压工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU Project（Jean-loup Gailly 和 Mark Adler） |
| 首次发布 | 1992 年 |
| 用途 | 对单个文件进行压缩/解压，是 Linux/macOS 上最基础的压缩工具之一，常与 tar 配合打包 |

## 🎯 用来做什么？

- **压缩文件节省磁盘空间** — 文本文件、日志、源代码等可压缩到原大小的 20%-30%
- **与 tar 配合打包** — 用 `tar -czf` 将多个文件打包成一个 `.tar.gz`（即 `.tgz`）
- **解压 `.gz` 文件** — 这是网上最常见的压缩格式之一（软件包、数据文件）
- **快速查看压缩文件内容** — 用 `zcat`、`zless` 直接读取 `.gz` 文件，无需解压

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `gzip file.txt` | 压缩文件，生成 `file.txt.gz`，原文件被删除 |
| `gzip -k file.txt` | 压缩并保留原文件（`-k` 即 `--keep`） |
| `gzip -d file.txt.gz` | 解压 `.gz` 文件（等同于 `gunzip file.txt.gz`） |
| `gunzip -k file.txt.gz` | 解压并保留原 `.gz` 文件 |
| `gzip -l file.txt.gz` | 查看压缩文件的压缩比和原始大小 |
| `gzip -9 file.txt` | 最高压缩级别（最慢但压缩率最高），默认是 6 |
| `gzip -r logs/` | 递归压缩目录下所有文件（不会打包，是逐个压缩） |
| `zcat file.txt.gz` | 读取压缩文件内容到标准输出（相当于 `gunzip -c`） |
| `zless file.txt.gz` | 分页查看压缩文件内容 |
| `zgrep 'error' file.txt.gz` | 直接在压缩文件中搜索文本 |

## 💡 日常使用示例

### 1. 压缩日志文件存档

```bash
# 假设有一堆日志文件
ls -lh /var/log/system*.log
# -rw-r--r--  1 root  wheel    12M Jun  2 10:00 system.log
# -rw-r--r--  1 root  wheel    8.5M Jun  1 10:00 system.log.0

# 逐个压缩（保留原文件）
gzip -k /var/log/system.log

# 查看压缩效果
gzip -l /var/log/system.log.gz
#         compressed        uncompressed  ratio uncompressed_name
#              800123           12582912  93.6% system.log
# 压缩率达到 93.6%，12MB → 0.8MB

# 批量压缩当前目录所有 .log 文件
gzip *.log
```

### 2. 配合 tar 实现多文件打包压缩

```bash
# 打包并压缩多个文件/目录
tar -czf project_backup.tar.gz src/ docs/ README.md

# 查看包内容列表（不解压）
tar -tzf project_backup.tar.gz
# 输出：
# src/
# src/main.py
# src/utils.py
# docs/
# docs/guide.md
# README.md

# 解压到指定目录
tar -xzf project_backup.tar.gz -C /tmp/restore/

# 解压时只提取单个文件
tar -xzf project_backup.tar.gz README.md
```

### 3. 通过管道直接使用 gzip

```bash
# 备份数据库并实时压缩（无需中间文件）
mysqldump -u root mydb | gzip > mydb_backup.sql.gz

# 解压并直接查看
zcat mydb_backup.sql.gz | head -20

# 解压并统计行数
zcat mydb_backup.sql.gz | wc -l

# 在压缩文件里搜索
zgrep "CREATE TABLE" mydb_backup.sql.gz

# ⚠️ macOS 注意：zgrep = grep -Z，行为与 gzip -d | grep 相同
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `gzip: file has already been .gz` | 文件已经是 `.gz` 格式 | 用 `file file.gz` 确认格式，用 `gunzip` 解压 |
| 无法压缩多个文件（没有打包效果） | gzip 只能压缩**单个文件**，不会打包成归档 | 先用 `tar` 打包为 `.tar`，再用 `gzip` 压缩（或直接用 `tar -czf`） |
| 压缩后原文件消失了 | gzip 默认删除原文件 | 加 `-k`（keep）参数保留原文件 |
| macOS 上的 gzip 是 Apple 版本 | `Apple gzip` 功能与 GNU gzip 基本兼容，但缺少部分高级选项 | 需 GNU 版本可 `brew install gzip` |
| `.tar.gz` vs `.tgz` | 两者完全一样，`.tgz` 只是短名称 | 文件管理器通常都支持两种扩展名 |

## 🔗 参考链接

- [gzip 官方手册](https://www.gnu.org/software/gzip/manual/gzip.html)
- [gzip Wikipedia](https://en.wikipedia.org/wiki/Gzip)
- [tar + gzip 组合使用指南](https://www.gnu.org/software/tar/manual/tar.html)