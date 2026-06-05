# caffeinate — macOS 防休眠工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | macOS 10.8 Mountain Lion（2012 年） |
| 用途 | 阻止 Mac 进入睡眠状态，保持屏幕亮起或磁盘活动，常用于长时间运行的下载、渲染、编译等场景 |
| macOS 自带 | ✅ 是（`/usr/bin/caffeinate`） |

> ⚠️ **仅限 macOS**：caffeinate 是 macOS 独有的工具，Linux/Windows 没有对应命令。

## 🎯 用来做什么？

- **防止 Mac 休眠**：运行长时间任务（编译、下载、渲染、视频转码）时，避免 Mac 自动进入睡眠
- **保持屏幕常亮**：做演示或监控时，阻止显示器自动熄灭
- **阻止磁盘休眠**：大文件传输或磁盘操作时，防止磁盘进入休眠模式
- **绑定到已有进程**：caffeinate 自动跟随某个进程运行，进程结束后自动释放防休眠锁

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `caffeinate` | 默认模式：阻止系统闲置休眠（按 Ctrl+C 退出） |
| `caffeinate -d` | 阻止显示器休眠（保持屏幕常亮） |
| `caffeinate -i` | 阻止系统闲置休眠（与默认等价） |
| `caffeinate -m` | 阻止磁盘休眠 |
| `caffeinate -s` | 阻止系统休眠（仅当接电源时有效） |
| `caffeinate -u` | 模拟用户活动状态，点亮屏幕（默认 5 秒超时） |
| `caffeinate -t <秒>` | 指定防休眠的时长，到达后自动退出 |
| `caffeinate -w <PID>` | 绑定到指定进程：进程结束后自动退出 |
| `caffeinate -s command` | 在执行命令期间保持防休眠，命令结束自动退出 |

## 💡 日常使用示例

### 示例 1：编译大型项目时防止休眠

```bash
$ caffeinate make -j8
```

编译过程中 Mac 不会自动休眠。编译结束后 caffeinate 自动退出。

### 示例 2：下载大文件时保持系统清醒

```bash
$ caffeinate -i wget https://example.com/large-file.iso
```

`-i` 阻止系统闲置休眠，下载完成后释放锁。

### 示例 3：在后台运行，同时绑定到已有进程

```bash
# 先启动一个长时间运行的任务
$ ffmpeg -i input.mp4 output.mp4 &
[1] 12345

# 用 caffeinate 绑定到该进程，确保 Mac 不睡
$ caffeinate -w 12345
```

caffeinate 会一直运行直到进程 12345 退出，然后自动退出。

### 示例 4：限时 1 小时保持屏幕常亮

做演示或展示时用：

```bash
$ caffeinate -d -t 3600
```

屏幕保持常亮 1 小时（3600 秒），之后自动释放。

### 示例 5：组合场景——边看监控边阻止休眠

```bash
# 同时阻止系统休眠和显示器休眠
$ caffeinate -d -i
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| Mac 仍然休眠了 | 没加 `-s` 参数，合盖时会无视 caffeinate | `-s` 仅 AC 电源有效；合盖休眠是硬件行为，caffeinate 无法阻止 |
| 终端关闭后 caffeinate 失效 | caffeinate 是在终端前台运行的 | 使用 `nohup` 或 `tmux`/`screen`，或绑定到已有进程（`-w`） |
| `-s` 参数在电池下无效 | `-s` 的设计就是只在外接电源时生效 | 换成 `-i`（阻止闲置休眠，电池下也有效） |
| 忘记退出 caffeinate | 终端卡在前台 | 用 `t` 参数限时，或用 `-w` 绑定进程自动退出 |
| 系统偏好中的"防止休眠"设置冲突 | macOS 的 Energy Saver 设置优先 | 先检查 `pmset -g` 查看当前电源策略 |

## 🔗 参考链接

- [Apple 手册：caffeinate(8)](https://ss64.com/osx/caffeinate.html)
- [macOS 电源管理快速查看：`pmset -g`]