# tar — 文件归档与压缩工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（最初）；现由 GNU/libarchive 维护 |
| 首次发布 | 1979年 |
| 用途 | 将多个文件和目录打包为单一归档文件（tar 文件），支持 gzip/zstd/bzip2 压缩 |

## 🎯 用来做什么？

- **文件打包分发** — 把整个项目目录打包成 `.tar.gz` 发给别人
- **备份文件** — 将重要目录打包压缩存档，配合 cron 做自动备份
- **下载大文件** — 开源软件通常以 `.tar.gz` 格式发布，一个包就是整个项目
- **增量备份** — tar 支持增量归档，只备份改动过的文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `tar -czf archive.tar.gz dir/` | 将 `dir/` 打包并 gzip 压缩（c=create, z=gzip, f=file） |
| `tar -xzf archive.tar.gz` | 解压 `archive.tar.gz` 到当前目录（x=extract） |
| `tar -tzf archive.tar.gz` | 列出归档内容（只查看不解压） |
| `tar -czf archive.tar.gz file1 file2 dir/` | 打包多个文件和目录 |
| `tar -xzf archive.tar.gz -C /target/dir` | 解压到指定目录（-C 指定目标路径） |
| `tar -czf archive.tar.gz --exclude='*.log' dir/` | 打包时排除特定模式的文件 |
| `tar -cjf archive.tar.bz2 dir/` | 用 bzip2 压缩（更小但更慢，j=bzip2） |
| `tar --list -f archive.tar.gz` | 另一种列出归档内容的方式 |

## 💡 日常使用示例

### 示例 1：打包整个项目目录

```bash
$ mkdir -p /tmp/myproject
$ echo "file1 content" > /tmp/myproject/file1.txt
$ echo "file2 content" > /tmp/myproject/file2.txt
$ mkdir -p /tmp/myproject/subdir
$ echo "subfile content" > /tmp/myproject/subdir/subfile.txt

# 打包为 tar.gz（最常用的格式）
$ cd /tmp
$ tar -czf project_backup.tar.gz myproject

# 查看打包后的文件大小
$ ls -lh project_backup.tar.gz
-rw-r--r--@ 1 user staff   659B May 24 01:00 project_backup.tar.gz

# 列出归档内容（验证）
$ tar -tzf project_backup.tar.gz
myproject/
myproject/file2.txt
myproject/file1.txt
myproject/subdir/
myproject/subdir/subfile.txt
```

### 示例 2：解压到指定目录

```bash
# 解压到 /tmp/extracted（确保目标目录存在）
$ mkdir -p /tmp/extracted
$ tar -xzf project_backup.tar.gz -C /tmp/extracted

# 验证解压结果
$ find /tmp/extracted -type f
/tmp/extracted/myproject/file1.txt
/tmp/extracted/myproject/file2.txt
/tmp/extracted/myproject/subdir/subfile.txt
```

### 示例 3：打包时排除某些文件

```bash
$ mkdir -p /tmp/deploy
$ echo "index.html" > /tmp/deploy/index.html
$ echo "app.js" > /tmp/deploy/app.js
$ mkdir -p /tmp/deploy/logs
$ echo "access.log" > /tmp/deploy/logs/access.log
$ echo "error.log" > /tmp/deploy/logs/error.log
$ mkdir -p /tmp/deploy/node_modules
$ echo "dep.js" > /tmp/deploy/node_modules/dep.js

# 打包时排除 logs 目录和 node_modules
$ cd /tmp
$ tar -czf deploy.tar.gz --exclude='logs' --exclude='node_modules' deploy

# 查看归档内容，确认排除生效
$ tar -tzf deploy.tar.gz
deploy/
deploy/index.html
deploy/app.js
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 解压出来文件散了一地 | 归档内的文件路径是相对路径，解压到当前目录散开 | 先用 `tar -tzf` 查看归档结构，再用 `-C` 解压到新目录 |
| macOS 上 tar 版本与 Linux 不同 | macOS 使用 bsdtar（libarchive），与 GNU tar 有细微差异 | 大多数命令兼容，但遇到奇怪问题检查版本：`tar --version` |
| 压缩比不如预期 | gzip 压缩较快但压缩率一般 | 换 `-cjf`（bzip2）或 `-cJf`（xz），但更慢 |
| 大文件归档内存不足 | tar 默认在内存中构建归档 | 用 `--checkpoint` 观察进度，或换用 `pigz` 并行压缩 |
| 符号链接被解压为实际文件 | tar 默认处理符号链接方式可能不符合预期 | 用 `-h` 选项（跟随符号链接），测试环境先验证 |

## 🔗 参考链接

- [GNU tar 手册](https://www.gnu.org/software/tar/manual/)
- [libarchive/bsdtar 文档](https://libarchive.org/)
- macOS 安装 GNU tar：`brew install gnu-tar`（命令为 `gtar`）
