# base64 — 二进制数据的文本编码与解码

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | BSD / GNU 项目 |
| 首次发布 | 1987 年（PEM 协议中首次定义） |
| 用途 | 将二进制数据编码成纯 ASCII 文本，或将 Base64 文本解码回原数据 |
| macOS 自带 | ✅ 是（预装在 macOS 中） |

## 🎯 用来做什么？

- **在纯文本协议中传输二进制数据**：如 JSON/XML 中嵌入图片、文件
- **存储二进制数据到数据库**：数据库字段不支持二进制时，用 Base64 编码后存入
- **简化数据分享**：将小文件编码成一段文本，方便复制粘贴
- **解码 JWT Token**：JWT 的 Payload 就是 Base64 编码的 JSON

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `echo "text" \| base64` | 将字符串编码为 Base64 |
| `echo "encoded" \| base64 -d` | 将 Base64 解码为原文 |
| `base64 -i file.txt -o encoded.txt` | 将文件编码为 Base64 |
| `base64 -i encoded.txt -o decoded.txt -d` | 将 Base64 文件解码 |
| `base64 -b 76 < file.png` | 编码并每 76 个字符换行（邮件传输标准） |
| `cat file.png \| base64 \| pbcopy` | 将文件编码并复制到剪贴板（macOS） |
| `cat file \| base64 -d > output.png` | 将 Base64 流解码输出为二进制文件 |

## 💡 日常使用示例

### 1️⃣ 编码和解码字符串

```bash
# 编码字符串 "Hello World"
$ echo -n "Hello World" | base64
SGVsbG8gV29ybGQ=

# 解码回来
$ echo "SGVsbG8gV29ybGQ=" | base64 -d
Hello World
```

> `-n` 参数不加换行符，否则也会把换行编码进去。对比：`echo "Hello World" | base64` 会输出 `SGVsbG8gV29ybGQK`（多一个 `K` 表示换行）。

### 2️⃣ 在 JSON 中嵌入一张小图片

```bash
# 将一张 PNG 图片编码为 Base64
$ base64 -i icon.png -o icon.txt

# 查看编码后的内容（前 50 字符）
$ cat icon.txt | head -c 50
iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9h...

# 然后可以直接嵌入到 JSON 中：
# {
#   "name": "icon",
#   "data": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUg..."
# }
```

浏览器可以直接识别 `data:image/png;base64,` 开头的图片。这样就不需要单独上传图片文件了。

### 3️⃣ 解码 JWT Token 查看内容

JWT（JSON Web Token）由三段 Base64 编码组成，用 `.` 分隔：

```bash
# 一个示例 JWT（通常更长，这里简化）
$ JWT="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"

# 解码 Payload（第二部分）
$ echo "eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ" | base64 -d
{"sub":"1234567890","name":"John Doe","iat":1516239022}
```

> 注意：JWT 的 Base64 使用了 URL-safe 字符集（`-` 代替 `+`，`_` 代替 `/`），且省略了末尾的 `=` 填充。如果解码时报错，试试在末尾补 `=` 到长度为 4 的倍数。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| 解码报错 `Invalid character in input stream` | 输入包含非 Base64 字符（换行、空格等） | 先用 `tr -d '\n'` 去掉换行：`cat encoded \| tr -d '\n' \| base64 -d` |
| `echo "text" \| base64 -d` 解码失败 | `echo` 默认带换行符，Base64 对末尾填充敏感 | 用 `echo -n` 或 `printf` |
| 解码后文件损坏 | 编码时加了换行但解码时没处理 | 编码时用 `-b 0` 禁止换行，或解码前用 `tr -d '\n\r'` 清理 |
| macOS 的 base64 和 Linux 不一致 | macOS 参数格式不同 | macOS 用 `-d` 解码，Linux GNU 版用 `-d` 也需要检查，Ubuntu 可能用 `--decode` |
| 大文件编码后体积太大 | Base64 编码会让体积增加 ~33% | 大文件不适合 Base64，直接用二进制传输（如 SCP/FTP） |

**macOS 差异：**
- macOS 的 `base64` 是 BSD 版，与 Linux 的 GNU 版略有不同
- macOS 用 `-d` 表示解码（GNU 版也用 `-d`，也支持 `--decode`）
- macOS 的 `-b` 参数表示行宽（macOS 和 GNU 一致）
- macOS 有 `-D` 别名等同于 `-d`，Linux 没有
- 要跨平台兼容，推荐写法：`base64 -d`（macOS 和 Linux 都支持）

## 🔗 参考链接

- [macOS man page - base64](https://ss64.com/osx/base64.html)
- [Base64 维基百科](https://en.wikipedia.org/wiki/Base64)
- [RFC 4648 - Base16, Base32, Base64 数据编码](https://datatracker.ietf.org/doc/html/rfc4648)