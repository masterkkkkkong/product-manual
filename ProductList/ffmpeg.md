# FFmpeg — 音视频处理瑞士军刀

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | FFmpeg 社区（Fabrice Bellard 发起） |
| 首次发布 | 2000 年 |
| 用途 | 处理几乎所有格式的音视频：转码、剪切、合并、录制、流媒体 |

## 🎯 用来做什么？

- **视频格式转换** — MP4↔MOV↔AVI↔MKV，调整编码、分辨率、码率
- **音频提取 / 格式转换** — 从视频中提取 MP3/WAV，或转换音频格式
- **视频剪切与拼接** — 截取片段、合并多段视频
- **截图与 GIF 制作** — 从视频中提取帧、生成高质量动图
- **流媒体录制** — 录制直播流或屏幕

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ffmpeg -i input.mp4 output.avi` | 最基础的格式转换 |
| `ffmpeg -i input.mp4 -vn output.mp3` | 从视频中提取音频（-vn 表示禁用视频） |
| `ffmpeg -i input.mp4 -ss 00:01:30 -t 10 output.mp4` | 从 1 分 30 秒开始截取 10 秒视频 |
| `ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4` | 将视频缩放至 1280×720 |
| `ffmpeg -i input1.mp4 -i input2.mp4 -filter_complex concat output.mp4` | 拼接两段视频 |
| `ffmpeg -i input.mp4 -ss 00:00:05 -vframes 1 screenshot.png` | 在第 5 秒截取一帧图片 |
| `ffmpeg -i input.mp4 -vf "fps=10,scale=640:-1" output.gif` | 将视频转为 GIF |
| `ffmpeg -i input.mp4 -c:v libx264 -crf 23 -c:a aac output.mp4` | 指定编码器压缩视频 |

## 💡 日常使用示例

### 示例 1：压缩视频以节省空间

将一个大视频用 H.264 编码压缩，`-crf` 值越高文件越小（23 是平衡点，28 更小）。

```bash
# 查看原始视频信息
ffprobe -v error -show_entries format=duration,size -of default=noprint_wrappers=1 input.mp4

# 压缩 — CRF 28，音频降到 96kbps
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -c:a aac -b:a 96k compressed.mp4
```

**输出示例**（取决于源文件）：
```
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'input.mp4':
  Duration: 00:05:30.12, start: 0.000000, bitrate: 12000 kb/s
  Stream #0:0[0x1](und): Video: h264 (Main) (avc1 / 0x31637661), yuv420p(tv, bt709, progressive), 1920x1080 [SAR 1:1 DAR 16:9], 11500 kb/s
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 128 kb/s
```

### 示例 2：从视频中提取音频片段

从长视频中截取特定段落并保存为 MP3：

```bash
# 从第 2 分钟开始截取 30 秒音频
ffmpeg -i input.mp4 -ss 00:02:00 -t 30 -q:a 0 -map a clip.mp3
```

- `-q:a 0`：最佳 MP3 音质（0-9，0 最好，9 最差）
- `-map a`：只处理音频流

### 示例 3：制作视频缩略图拼贴

生成一张包含多帧的预览图（每 30 秒一帧）：

```bash
ffmpeg -i input.mp4 -vf "fps=1/30,scale=320:-1,tile=4x3" -frames:v 1 preview.jpg
```

- `fps=1/30`：每 30 秒取一帧
- `scale=320:-1`：缩放到宽度 320 像素，高度等比
- `tile=4x3`：拼成 4 列 × 3 行的网格

## 🚨 常见坑点

| 错误现象 | 原因 | 解决方法 |
|----------|------|----------|
| `Unknown encoder 'libx264'` | FFmpeg 编译时未包含 x264 编码器 | 用 Homebrew 重装：`brew install ffmpeg`（完整版） |
| `Protocol 'https' not supported` | 编译时未包含 HTTPS 协议 | 同上，用 brew 安装完整版 |
| 生成的 MP4 无法播放 | 没有将 moov atom 移到文件头 | 加 `-movflags +faststart` 参数 |
| macOS 上 `ffmpeg: command not found` | 未安装 | `brew install ffmpeg` |
| 视频转 GIF 文件太大 | 帧率或分辨率太高 | 用 `fps=10` 降帧 + `scale=480:-1` 降分辨率 |

## 🔗 参考链接

- [FFmpeg 官方文档](https://ffmpeg.org/documentation.html)
- [FFmpeg Wiki](https://trac.ffmpeg.org/wiki)
- [FFmpeg 命令速查表](https://gist.github.com/steven2358/ba153c642fe2bb1e47485962bd07d3c6)
