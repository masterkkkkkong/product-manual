# osascript — 在命令行运行 AppleScript / JavaScript 自动化脚本

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | 1990 年代随 AppleScript 生态提供 |
| 用途 | 从终端执行 AppleScript 或 JavaScript for Automation（JXA），控制 macOS 应用和系统自动化流程 |

> ⚠️ **macOS 专属工具**：`osascript` 依赖 macOS 的 Open Scripting Architecture。Linux/Windows 没有同等系统命令。

## 🎯 用来做什么？

- 在 Shell 脚本里调用 AppleScript，自动控制 Finder、Safari、Mail 等应用
- 执行一行临时脚本，比如弹窗、获取当前日期、简单计算
- 运行 `.applescript` 文件，把参数从命令行传进去
- 使用 JavaScript for Automation（JXA）写更接近 JavaScript 语法的 macOS 自动化脚本

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `osascript -e 'return 1 + 2'` | 执行一行 AppleScript |
| `osascript -e 'line1' -e 'line2'` | 执行多行脚本，每个 `-e` 一行 |
| `osascript script.applescript arg1 arg2` | 运行脚本文件，并传入参数 |
| `osascript -l JavaScript -e '...'` | 使用 JavaScript for Automation（JXA）执行脚本 |
| `osascript -i` | 进入交互模式，逐行输入脚本 |
| `osascript -s h -e '...'` | 用更适合人读的格式输出结果 |
| `osascript -s o -e '...'` | 只输出脚本结果，减少额外格式信息 |

## 💡 日常使用示例

### 示例 1：在终端执行一行 AppleScript

```bash
$ osascript -e 'return "Hello, AppleScript"'
Hello, AppleScript

$ osascript -e 'return 1 + 2'
3
```

适合在 Shell 脚本里补一个 macOS 自动化动作，例如显示通知、获取应用状态等。

### 示例 2：用多行 `-e` 组合一个小脚本

```bash
$ osascript \
  -e 'set x to 40' \
  -e 'set y to 2' \
  -e 'return x + y'
42
```

多行脚本不用临时文件，适合放在自动化脚本或 Makefile 里。

### 示例 3：运行脚本文件并传入参数

先创建一个脚本文件：

```bash
$ cat > /tmp/arg.applescript <<'APPLESCRIPT'
on run argv
  return item 1 of argv
end run
APPLESCRIPT
```

执行并传参：

```bash
$ osascript /tmp/arg.applescript demo
demo
```

`argv` 是参数列表；AppleScript 的列表下标从 `1` 开始，不是从 `0` 开始。

### 示例 4：用 JavaScript for Automation（JXA）执行脚本

```bash
$ osascript -l JavaScript -e 'JSON.stringify({answer: 42})'
{"answer":42}
```

JXA 适合熟悉 JavaScript 的人，但控制具体 App 时仍要查该 App 的脚本字典。

## 🚨 常见坑点

| 坑点 | 原因 | 解决方法 |
|------|------|----------|
| `osascript -h` 报 `illegal option -- h` | `osascript` 没有 `-h` 选项 | 直接运行 `osascript` 或查看 `man osascript` |
| 控制某个 App 时弹权限框或失败 | macOS 自动化权限限制（Automation / Accessibility） | 在 System Settings → Privacy & Security 中授权 Terminal/iTerm/脚本运行器 |
| AppleScript 下标从 1 开始 | AppleScript 语法和多数编程语言不同 | 访问第一个参数用 `item 1 of argv` |
| 引号在 Shell 和 AppleScript 中互相冲突 | 两层语言都要解析引号 | 外层尽量用单引号；复杂脚本写到 `.applescript` 文件里 |
| JXA 能跑但控制 App 失败 | App 的脚本字典不一定完整，JXA 文档较少 | 用 Script Editor 查看应用字典，简单任务优先 AppleScript |

## 🔗 参考链接

- Apple `osascript` 手册：`man osascript`
- AppleScript Language Guide：https://developer.apple.com/library/archive/documentation/AppleScript/Conceptual/AppleScriptLangGuide/
- JavaScript for Automation Release Notes：https://developer.apple.com/library/archive/releasenotes/InterapplicationCommunication/RN-JavaScriptForAutomation/
