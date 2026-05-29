# zip & unzip — 文件压缩与解压工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Info-ZIP 组织 |
| 首次发布 | 1990 年 |
| 用途 | 将文件和目录打包成 `.zip` 压缩文件（`zip`）或从 `.zip` 中提取文件（`unzip`） |

## 🎯 用来做什么？

- **打包分享** — 把多个文件/文件夹压缩成一个文件，方便传输或存档
- **节省空间** — 压缩日志文件、备份数据，降低磁盘占用
- **解压别人给的包** — 解压 `.zip` 文件，这是跨平台最通用的压缩格式
- **快速查看压缩包内容** — 不用解压就能浏览 zip 里装了哪些文件

## 🔧 常用命令

### zip（压缩）

| 命令 | 说明 |
|------|------|
| `zip archive.zip file.txt` | 将 file.txt 压缩到 archive.zip |
| `zip -r archive.zip folder/` | 递归压缩整个文件夹 |
| `zip -r archive.zip file1.txt file2.txt folder/` | 同时压缩多个文件/文件夹 |
| `zip -9 archive.zip file.txt` | 最高压缩比（更小体积，耗时稍长） |
| `zip -1 archive.zip file.txt` | 最快压缩（体积大但速度快） |
| `zip -d archive.zip file.txt` | 从压缩包中删除指定文件 |
| `zip -u archive.zip file.txt` | 更新压缩包中的文件（只替换变动的） |

### unzip（解压）

| 命令 | 说明 |
|------|------|
| `unzip archive.zip` | 解压到当前目录 |
| `unzip archive.zip -d target/` | 解压到指定目录 |
| `unzip -l archive.zip` | 列出压缩包中的文件（不解压） |
| `unzip -t archive.zip` | 测试压缩包完整性 |
| `unzip -o archive.zip` | 覆盖已有文件（不询问） |
| `unzip -n archive.zip` | 不覆盖已有文件 |

## 💡 日常使用示例

### 示例 1：压缩一个项目文件夹

```bash
# 把 my-project/ 整个目录压缩
$ zip -r my-project.zip my-project/
  adding: my-project/ (stored 0%)
  adding: my-project/src/ (stored 0%)
  adding: my-project/src/main.py (deflated 45%)
  adding: my-project/README.md (deflated 52%)
  adding: my-project/data/ (stored 0%)

# 看看大小
$ ls -lh my-project.zip
-rw-r--r--  1 user  staff   12K May 30 01:00 my-project.zip
```

### 示例 2：解压并指定输出目录

```bash
# 解压到特定目录（推荐做法，避免文件撒得到处都是）
$ unzip data.zip -d ./extracted_data/
Archive:  data.zip
  inflating: ./extracted_data/report.csv
  inflating: ./extracted_data/chart.png
  inflating: ./extracted_data/summary.txt

# 确认解压成功
$ ls extracted_data/
report.csv    chart.png    summary.txt
```

### 示例 3：不解压查看包里有什么

```bash
$ unzip -l large-dataset.zip
Archive:  large-dataset.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
 12345678  2026-05-28 14:30   train.csv
  9876543  2026-05-28 14:30   test.csv
    12345  2026-05-28 14:30   README.md
---------                     -------
 22234566                     3 files
```

这样可以快速确认压缩包里有没有你需要的文件，再决定要不要解压。

### 示例 4：测试压缩包完整性

```bash
# 下载完一个 zip 后检查是否损坏
$ unzip -t download.zip
Archive:  download.zip
    testing: image001.jpg    OK
    testing: image002.jpg    OK
    testing: image003.jpg    OK
No errors detected in compressed data.
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `zip -r` 没加导致文件夹没被打进去 | 不加 `-r` 时 zip 不会递归处理目录 | 压缩文件夹时务必加 `-r` |
| 解压后文件散落一地（没有目录） | 原来压缩时就没带目录结构，或者用了 `-j`（junk paths） | 解压时用 `-d` 指定一个空目录隔离 |
| 压缩后体积没变小多少 | 图片、视频、二进制文件本身已经是压缩格式，zip 再压也没用 | 这是正常现象，zip 主要对文本文件效果好 |
| macOS 上 `__MACOSX` 文件夹出现 | macOS 会自动存储 Finder 扩展属性在 zip 里 | 用 `zip -X` 排除扩展属性，或者解压后无视这个文件夹 |
| 文件解压后中文名乱码 | 旧版压缩工具使用非 UTF-8 编码的 zip 格式 | 用 `unzip -O GBK`（如果支持）或在更高版本 macOS/Linux 上解压 |

## 🔗 参考链接

- [Info-ZIP 主页](http://www.info-zip.org/)
- [zip 手册 (Linux man page)](https://man.freebsd.org/cgi/man.cgi?query=zip)
- [unzip 手册 (Linux man page)](https://man.freebsd.org/cgi/man.cgi?query=unzip)