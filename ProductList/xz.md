# xz — 高压缩率文件压缩工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | The Tukaani Project（Lasse Collin 等） |
| 首次发布 | 2009 年 |
| 用途 | 提供比 gzip/bzip2 更高的压缩率，常用于软件包分发（如 Linux 内核源码、Homebrew 二进制包）和数据归档 |

## 🎯 用来做什么？

- **最高效的文件压缩** — 相比 gzip 通常多压缩 30-50%，但压缩速度更慢
- **软件包分发** — Linux 内核（kernel.org）和许多开源项目都用 `.tar.xz` 格式
- **与 tar 配合打包** — 用 `tar -cJf` 创建高压缩率归档包
- **受限于带宽的场景** — 需要传输大文件但网络慢，xz 能帮你省流量

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `xz file.txt` | 压缩文件，生成 `file.txt.xz`，原文件被删除 |
| `xz -k file.txt` | 压缩并保留原文件 |
| `xz -d file.txt.xz` | 解压（等同于 `unxz file.txt.xz`） |
| `unxz -k file.txt.xz` | 解压并保留原 `.xz` 文件 |
| `xz -l file.txt.xz` | 查看压缩文件的详细信息（原始大小、压缩比） |
| `xz -9 file.txt` | 最高压缩级别（最慢，内存占用最高） |
| `xz -0 file.txt` | 最快速压缩（但压缩率最低） |
| `xz -T 0 file.txt` | 使用所有 CPU 核并行压缩（大幅加快速度） |
| `xz -M 50MiB file.txt` | 限制内存使用为 50MB（避免大文件 OOM） |
| `xz -z -c file.txt > file.xz` | 压缩到标准输出（管道友好） |
| `xzcat file.txt.xz` | 读取压缩文件内容到标准输出（类似 `zcat`） |

## 💡 日常使用示例

### 1. 压缩大日志文件

```bash
# 准备一个示例文件
dd if=/dev/urandom bs=1M count=10 of=sample.log 2>/dev/null
# 实际日志通常有重复内容，压缩率会更高

# 用 xz 压缩（默认级别 6）
xz -k sample.log
gzip -k sample.log  # 对比

# 看压缩效果（xz 通常比 gzip 小 30-50%）
ls -lh sample.log*
# -rw-r--r--  10M sample.log
# -rw-r--r--  10M sample.log.gz    （gzip 对随机数据压缩效果差）
# -rw-r--r--  10M sample.log.xz    （xz 也一样，但文本文件差距明显）

# 查看详细压缩信息
xz -l sample.log.xz
# Strms  Blocks   Compressed Uncompressed  Ratio  Check   Filename
#     1       1     10.0 MiB     10.0 MiB  1.000  CRC64   sample.log.xz

# 清理
rm -f sample.log sample.log.gz sample.log.xz
```

### 2. 用 tar 创建 .tar.xz 归档

```bash
# 创建一个项目目录结构
mkdir demo-project
echo "console.log('hello');" > demo-project/app.js
echo "# README" > demo-project/README.md
echo "node_modules/" > demo-project/.gitignore

# 打包并 xz 压缩（-c = create, -J = xz, -f = filename）
tar -cJf demo-project.tar.xz demo-project/

# 查看包内容
tar -tJf demo-project.tar.xz
# 输出：
# demo-project/
# demo-project/app.js
# demo-project/README.md
# demo-project/.gitignore

# 解压到指定目录
tar -xJf demo-project.tar.xz -C /tmp/

# ⚠️ macOS 注意：系统自带 tar 支持 -J 选项
# 但如果 tar 版本较旧，可能需要 brew install gnu-tar

# 清理
rm -rf demo-project demo-project.tar.xz /tmp/demo-project
```

### 3. 管道压缩 — 备份数据流

```bash
# 将命令输出直接压缩保存
echo "这是一条重要的配置文件内容" | xz > config.txt.xz

# 解压并查看
xzcat config.txt.xz
# 输出：这是一条重要的配置文件内容

# 在压缩文件里搜索文本
xzcat config.txt.xz | grep "重要"

# 大型数据管道的处理流：
# mysqldump ... | xz -T 0 > backup.sql.xz   # 并行压缩
# xzcat backup.sql.xz | mysql ...            # 解压后导入
# 注意：加 -T 0 后 xz 无法同时从管道读取（T 模式需要 seekable 文件）
# 正确做法：xz -c > backup.sql.xz

# 并行压缩管道（用 pxz 或 xz -T 分块模式）
mysqldump -u root mydb | xz -c > mydb_backup.sql.xz
xzcat mydb_backup.sql.xz | wc -l

# 清理
rm config.txt.xz mydb_backup.sql.xz
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| 压缩大文件时内存不足 | `xz -9` 需要几百 MB 甚至数 GB 内存 | 加 `-M 300MiB` 限制内存；或降低压缩级别到 `-4` ~ `-6` |
| 多线程模式不兼容管道输入 | `xz -T 0` 要求输入是 seekable 文件，管道无法满足 | 不用 `-T`，改用 `-c` 输出到 stdout；或先用 `xz > file.xz` |
| 文件解压到一半提示 `Unexpected end of input` | 数据在写入时被中断 | 检查源文件完整性；尝试 `xz -d -c file.xz > recovered.txt` 部分恢复 |
| `tar -cJf` 报错 `xz: unrecognized option` | 系统 xz 版本过旧 | `brew install xz` 或 `brew reinstall xz` 更新 |
| macOS 没预装 xz | 和 gzip 不同，macOS 不带 xz | `brew install xz` 安装 |
| `.tar.xz` 比 `.tar.gz` 解压慢 | xz 解压确实比 gzip 慢（压缩率更高的代价） | 日常用 gzip 节省时间，发布分选用 xz 节省空间 |

## 🔗 参考链接

- [xz 官方文档](https://tukaani.org/xz/)
- [xz 手册](https://man.archlinux.org/man/xz.1)
- [xz 压缩级别和内存使用对照表](https://tukaani.org/xz/embedded.html)