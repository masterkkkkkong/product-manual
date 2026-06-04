# sips — macOS 脚本化图像处理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | macOS 10.4 (2005) |
| 用途 | 命令行批量处理图像：调整尺寸、旋转、裁剪、格式转换、查询元数据 |

## 🎯 用来做什么？

- **批量尺寸调整** — 一次性把数百张图片缩放到统一尺寸
- **格式转换** — PNG ↔ JPEG ↔ HEIC ↔ GIF 等格式互转（无需安装额外工具）
- **图像元数据查询** — 快速获取图片的宽高、DPI、色彩空间、ICC 配置
- **自动化脚本集成** — Shell 脚本和 CI 流程中处理图片，不依赖 Photoshop

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `sips -g pixelWidth image.png` | 查询图片宽度 |
| `sips -Z 800 image.jpg` | 等比缩放，最长边不超过 800px |
| `sips -z 600 800 image.jpg` | 强制缩放到 600×800（不保持比例） |
| `sips -r 90 image.jpg` | 顺时针旋转 90 度 |
| `sips -c 300 400 image.jpg --out cropped.jpg` | 从左上角裁剪 300×400 |
| `sips -s format jpeg image.png --out image.jpg` | PNG 转 JPEG（`-s format` 后跟目标格式） |
| `sips -s format heic image.jpg --out image.heic` | JPEG 转 HEIC（苹果高效格式） |
| `sips --formats` | 列出所有支持的读写格式 |

## 💡 日常使用示例

### 例 1：批量缩放文件夹中的所有图片
```bash
# 把当前目录下所有 .jpg 缩小到最长边 1200px
mkdir -p resized
for img in *.jpg; do
  sips -Z 1200 "$img" --out "resized/$img"
done
```
输出样例（每张图）：
```
/Users/mk/photos/DSC_001.jpg
  /Users/mk/photos/resized/DSC_001.jpg
```

### 例 2：查询图片信息
```bash
sips -g all photo.jpg
# 或者只查特定属性
sips -g pixelWidth -g pixelHeight -g dpiWidth photo.jpg
```
输出：
```
/Users/mk/photo.jpg
  pixelWidth: 4032
  pixelHeight: 3024
  dpiWidth: 72.000
```

### 例 3：批量转换 PNG → JPEG 并设置压缩质量
```bash
# sips 不支持直接设置 JPEG 压缩质量，但可以结合 sips 和 macOS 的 ColorSync 设置
# 简单的 PNG → JPG 转换：
for img in *.png; do
  base=${img%.png}
  sips -s format jpeg "$img" --out "${base}.jpg"
done
```
输出样例：
```
/Users/mk/screenshot.png
  /Users/mk/screenshot.jpg
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `sips: no matching arguments` | 命令参数顺序不对，文件名必须放在参数之后 | 先写 `-Z 800` 再写文件名 |
| 不支持 `-quality` 参数 | sips 没有 JPEG 压缩质量选项 | 改用 `magick` (ImageMagick) 或 macOS 的 `cmyk` 配置 |
| `--out` 路径不存在报错 | 输出目录不存在 | 先 `mkdir -p output_dir` |
| macOS 独占工具 | sips 只存在于 macOS，Linux/Windows 没有 | 跨平台改用 ImageMagick 的 `convert` / `magick` |
| HEIC 导出在旧 macOS 上失败 | 旧版本对 HEIC 支持有限 | 升级 macOS 或改用 JPEG/PNG |

## 🔗 参考链接

- [Apple sips man page](https://ss64.com/mac/sips.html)
- `man sips`（终端中查看完整文档）