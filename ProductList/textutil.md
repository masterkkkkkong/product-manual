# textutil — macOS 文档格式转换工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | macOS 10.4 (2005) |
| 用途 | 命令行批量转换文档格式：txt ↔ rtf ↔ html ↔ doc ↔ docx ↔ odt 等 |

## 🎯 用来做什么？

- **格式互转** — 纯文本转富文本、HTML 转 Word、RTF 转 Markdown（间接）等
- **批量处理** — 一次转换整个文件夹的文档，保持原始文件名
- **文档元数据查询** — 查看文件类型、字符数、编码信息
- **脚本集成** — 在自动化工作流中处理文档格式，无需打开 Office

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `textutil -info file.rtf` | 查看文档信息（格式、大小、字符数） |
| `textutil -convert txt file.rtf` | RTF 转纯文本 |
| `textutil -convert html file.rtf` | RTF 转 HTML |
| `textutil -convert docx file.html` | HTML 转 Word (.docx) |
| `textutil -convert rtf file.txt` | 纯文本转 RTF（可指定字体字号） |
| `textutil -convert odt file.docx` | Word 转 OpenDocument |
| `textutil -cat rtf file1.rtf file2.rtf -output merged.rtf` | 合并多个文档 |
| `textutil -convert html -output out.html -stdin < file.txt` | 从 stdin 读入并转换 |

## 💡 日常使用示例

### 例 1：纯文本转富文本（指定字体）
```bash
# 把 readme.txt 转成带字体的 RTF
textutil -convert rtf -font "Menlo" -fontsize 14 readme.txt -output readme.rtf

# 验证结果
textutil -info readme.rtf
```
输出：
```
File:  readme.rtf
  Type:  rich text format (RTF)
  Size:  329 bytes
  Length:  12 characters
```

### 例 2：HTML 转为 Word 文档
```bash
# 把网页保存的 HTML 转成 .docx，方便发给同事
textutil -convert docx report.html -output report.docx

# 查看转换结果信息
textutil -info report.docx
```
输出：
```
File:  report.docx
  Type:  Microsoft Word (DOCX)
  Size:  8192 bytes
  Length:  2456 characters
```

### 例 3：批量转换文件夹中所有 RTF 为纯文本
```bash
# 批量导出
cd ~/Documents/notes
for f in *.rtf; do
  textutil -convert txt "$f" -output "${f%.rtf}.txt"
done
echo "转换完成"

# 或者更简洁的写法（保留原文件夹结构）
mkdir -p txt_output
textutil -convert txt -output txt_output/ *.rtf
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `Unsupported input format` | textutil 不支持输入的格式 | textutil 仅支持：txt, rtf, rtfd, html, doc, docx, odt, wordml, webarchive |
| `-convert` 后格式名写错 | 格式参数区分大小写 | 格式名必须小写：txt, rtf, html, docx 等 |
| 中文乱码 | 导出纯文本时未指定 UTF-8 | 加 `-encoding UTF-8` 参数 |
| `-output` 指定了不存在的目录 | 输出目录需先创建 | 先 `mkdir -p output/` |
| macOS 独占工具 | textutil 只存在于 macOS，Linux/Windows 没有 | 跨平台用 Pandoc（`pandoc -f html -t docx`） |
| HTML 转 docx 样式丢失 | 复杂 CSS 会被简化 | textutil 使用 Cocoa HTML Writer 渲染，只支持基本样式；复杂排版用 Pages 或 Word |

## 🔗 参考链接

- [Apple textutil man page](https://ss64.com/mac/textutil.html)
- `man textutil`（终端中查看完整文档）
- [Pandoc](https://pandoc.org/) — 跨平台文档格式转换（功能更强）