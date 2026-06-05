# defaults — macOS 偏好设置命令行工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple（NeXTSTEP 遗产） |
| 首次发布 | 1989 年（NeXTSTEP） |
| 用途 | 通过命令行读写 macOS 应用的偏好设置（User Defaults），实现 GUI 中看不到的隐藏配置 |
| macOS 自带 | ✅ 是（`/usr/bin/defaults`） |

> ⚠️ **仅限 macOS**：`defaults` 是 macOS 独有的工具，源于 NeXTSTEP 操作系统。

## 🎯 用来做什么？

- **修改隐藏设置**：打开 Finder 显示隐藏文件、调整 Dock 动画速度等 GUI 设置面板里找不到的选项
- **批量配置新 Mac**：自动化配置几十项系统偏好，写成一个脚本「一键调教」
- **导出/恢复应用设置**：备份某个应用的偏好，迁移到另一台 Mac
- **排查应用问题**：查看或删除损坏的应用偏好配置文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `defaults read <domain>` | 读取指定应用/域的所有偏好设置 |
| `defaults read <domain> <key>` | 读取指定键的值 |
| `defaults write <domain> <key> <value>` | 写入（修改）指定键的值 |
| `defaults delete <domain> <key>` | 删除指定键的配置 |
| `defaults delete <domain>` | 删除整个域的偏好（慎用！会恢复出厂状态） |
| `defaults domains` | 列出所有已写入配置的域（应用） |
| `defaults find <keyword>` | 在所有域中搜索包含关键词的键或值 |
| `defaults read-type <domain> <key>` | 查看指定键的值的类型（string/number/boolean/...) |

## 💡 日常使用示例

### 示例 1：在 Finder 中显示隐藏文件（.xxx 文件）

```bash
# 查看当前设置（如果从未改过，会提示不存在）
$ defaults read com.apple.finder AppleShowAllFiles
2026-01-01 ... The domain/default pair ... does not exist

# 开启显示隐藏文件（1 = 显示，0 = 隐藏）
$ defaults write com.apple.finder AppleShowAllFiles -bool true

# 重启 Finder 使设置生效
$ killall Finder
```

### 示例 2：加速 Dock 弹出动画（适合喜欢快速操作的人）

```bash
# 查看当前 Dock 自动隐藏延迟（默认 0.5 秒）
$ defaults read com.apple.dock autohide-delay
0.5

# 改成几乎无延迟
$ defaults write com.apple.dock autohide-delay -float 0

# 加速 Dock 弹出动画
$ defaults write com.apple.dock autohide-time-modifier -float 0.15

# 重启 Dock
$ killall Dock
```

不想用了可以恢复默认：

```bash
$ defaults delete com.apple.dock autohide-delay
$ defaults delete com.apple.dock autohide-time-modifier
$ killall Dock
```

### 示例 3：一键备份和恢复所有偏好设置

备份指定应用的配置（例如 iTerm2）：

```bash
# 导出为 plist 文件
$ defaults export com.googlecode.iterm2 - > ~/iterm2-backup.plist

# 在新 Mac 上恢复
$ defaults import com.googlecode.iterm2 ~/iterm2-backup.plist
```

> 注意：`export/import` 适用于 plist 格式，不是每个域都支持。

### 示例 4：搜索关键词——快速找到相关设置

```bash
# 搜索所有包含 "screenshot" 字样的配置
$ defaults find screenshot
Found 14 keys in domain 'com.apple.screencapture':
  location = /Users/mk/Desktop
  type = png
  ...

Found 1 keys in domain 'com.apple.finder':
  SGBDebugScreenshots = 0
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `defaults read` 报错 "does not exist" | 该键从未被写入过，用的是系统默认值 | 报错不代表有问题，直接 `write` 写入即可 |
| 改完设置后没生效 | 应用没有重新读取配置 | 重启应用：`killall <应用名>`（Finder/Dock/SystemUIServer 等） |
| 值的类型搞错导致写入无效 | `-bool true` vs `-int 1` vs `-string "1"` 效果不同 | 先用 `read-type` 查看原类型，再用同类型写入 |
| `defaults delete <domain>` 导致应用行为异常 | 删除整个域 = 删除所有配置 | 只删具体 key：`defaults delete <domain> <key>` |
| macOS 更新后自定义配置被覆盖 | 系统更新可能重置部分偏好 | 保留配置脚本，更新后重新执行 |

## 🔗 参考链接

- [Apple 手册：defaults(1)](https://ss64.com/osx/defaults.html)
- [macOS Defaults 设置大全（社区整理的隐藏配置清单）](https://macos-defaults.com/)
- [Secrets — macOS 隐藏设置数据库](https://secrets.josh.farm/)