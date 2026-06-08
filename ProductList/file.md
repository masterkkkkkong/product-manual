# file — 文件类型检测工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Ian Darwin（最初）、Christos Zoulas（当前维护者） |
| 首次发布 | 1973 年（Unix V4） |
| 用途 | 读取文件头部特征字节（magic number）来识别文件真实类型，不依赖扩展名 |
| macOS 自带 | ✅ 是 |

## 🎯 用来做什么？

- **识别未知文件类型**：拿到一个没有扩展名的文件，快速知道它是图片、PDF 还是压缩包
- **验证文件真实性**：检查下载的文件是否真的是声称的格式（防止伪装）
- **批量分析**：配合 find 命令扫描目录，找出所有图片或所有二进制文件
- **安全检查**：识别隐藏在 txt/png 后缀下的恶意文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `file <filename>` | 检测单个文件的类型 |
| `file -b <filename>` | 精简输出，不显示文件名 |
| `file -i <filename>` | 输出 MIME 类型（如 `image/png`、`text/plain`） |
| `file -L <filename>` | 跟踪符号链接，检测目标文件的类型 |
| `file -z <filename>` | 检查压缩包内部文件类型 |
| `file *` | 检测当前目录下所有文件的类型 |
| `file -s <device>` | 检测特殊文件（如磁盘分区、设备文件） |
| `find . -type f -exec file {} \;` | 递归检测目录下所有文件类型 |

## 💡 日常使用示例

### 示例 1：识别常见文件类型

```bash
# 创建一个无扩展名的文件测试
echo "hello world" > test_data
curl -s -o test_logo https://curl.se/logo/curl-logo.svg

# 检测文件类型
file test_data
# 输出：test_data: ASCII text

file test_logo
# 输出：test_logo: SVG Scalable Vector Graphics document

# 用 -i 看 MIME 类型（更标准）
file -i test_logo
# 输出：test_logo: image/svg+xml; charset=utf-8

# 清理
rm test_data test_logo
```

### 示例 2：揭穿伪装文件

```bash
# 把一个 PNG 图片伪装成 txt
cp /System/Library/CoreServices/CoreTypes.bundle/Contents/Resources/GenericFolderIcon.icns fake.txt

file fake.txt
# 输出：fake.txt: Macintosh OS X icon family resource data

# 不管扩展名是什么，file 只看文件头特征字节
# 即使改成 fake.mp3、fake.docx，结果都一样

# 查看文件头（前几个字节）也能看出来：
xxd -l 8 fake.txt
# 输出：00000000: 6963 6e73       (icns — macOS 图标文件魔数)

rm fake.txt
```

### 示例 3：批量分析目录

```bash
# 查看 ~/Downloads 里所有文件类型
cd ~/Downloads
file * 2>/dev/null

# 输出示例：
# report.pdf:   PDF document, version 1.7
# photo.png:    PNG image data, 1920 x 1080
# data.csv:     CSV text
# script.sh:    Bourne-Again shell script, ASCII text executable
# archive.zip:  Zip archive data

# 只找出图片文件
file * | grep -i image
```

### 示例 4：配合 find 递归扫描

```bash
# 在项目目录下找出所有二进制文件（不包含文本文件）
find /usr/local/bin -type f -exec file {} \; | grep -v "ASCII text\|shell script\|symbolic link"
# 输出示例：
# /usr/local/bin/ffprobe:   Mach-O 64-bit executable x86_64
# /usr/local/bin/gh:        Mach-O 64-bit executable x86_64
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **file 不认识新格式** | 显示 `data`（未知类型） | 更新 magic 数据库：`brew upgrade file` 或安装 `libmagic` |
| **很短的文本文件被误判** | 显示 `empty` 或 `very short file` | 文件内容太少时 file 无法确定类型，这是正常行为 |
| **macOS 上 file 版本较旧** | 某些新型文件格式无法识别 | 用 Homebrew 安装新版：`brew install file` |
| **文件名含空格** | file 只处理了空格前部分 | 用引号包裹：`file "my file.txt"` |
| **符号链接不显示目标类型** | 只显示 `symbolic link to xxx` | 加 `-L` 参数跟踪到目标文件 |
| **大文件检测慢** | 检测大视频文件需要几秒 | file 默认只读前 16KB，通常很快；如果确实慢，可能文件是未知格式导致回退到更多扫描 |

## 🔗 参考链接

- [macOS file man page（官方）](https://man.freebsd.org/cgi/man.cgi?query=file)
- [file 命令官方仓库（GitHub）](https://github.com/file/file)
- [Wikipedia — Magic number (programming)](https://en.wikipedia.org/wiki/Magic_number_(programming))
