# open — macOS 打开文件/目录/URL 工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple（macOS 内置） |
| 首次发布 | macOS 原生命令 |
| 用途 | 从命令行打开文件、目录、URL 或应用程序，相当于在 Finder 中双击 |

## 🎯 用来做什么？

- **快速打开文件**——不用先找 Finder，终端里直接打开任何文件
- **在当前目录打开 Finder**——终端和图形界面无缝切换
- **用指定应用打开文件**——比如用 VS Code 打开 .py 文件而不是默认的 Xcode
- **打开 URL**——从终端直接打开网页，脚本中自动启动浏览器

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `open .` | 在当前目录打开 Finder |
| `open file.txt` | 用默认应用打开文件 |
| `open -a TextEdit file.txt` | 用指定应用打开文件 |
| `open -e file.txt` | 用 TextEdit 打开（等效 `open -a TextEdit`） |
| `open -t file.txt` | 用默认文本编辑器打开 |
| `open -R file.txt` | 在 Finder 中定位（reveal）文件 |
| `open https://example.com` | 用默认浏览器打开 URL |
| `open -n -a Safari` | 新建 Safari 实例（即使已运行） |
| `echo "text" \| open -f` | 从管道输入内容创建新 TextEdit 文档 |

## 💡 日常使用示例

### 示例 1：在 Finder 中定位项目文件

```bash
# 在终端里编辑完代码后，想在 Finder 里看看文件在哪
$ ls
src/  data/  README.md  config.yaml

# 在当前目录打开 Finder
$ open .
# Finder 窗口弹出，显示当前目录内容

# 或者定位到 README.md 同时高亮它
$ open -R README.md
# Finder 弹出并选中 README.md 文件
```

### 示例 2：用指定编辑器打开配置文件

```bash
# 假设 .yaml 的默认打开方式是 Xcode，但你想用 VS Code 编辑
$ open -a "Visual Studio Code" ~/.zshrc
# VS Code 打开 .zshrc 文件

# 用 Sublime Text 打开
$ open -a "Sublime Text" ~/.zshrc

# 不知道应用准确名称？用 -b 指定 bundle identifier
$ open -b com.microsoft.VSCode ~/.zshrc
```

### 示例 3：从脚本中打开浏览器

```bash
# 在脚本中自动打开网页
$ cat > open_sites.sh << 'EOF'
#!/bin/bash
# 打开今天的日报页面
open "https://github.com/trending"
# 打开 AI 资讯
open "https://aihot.virxact.com"
EOF
$ chmod +x open_sites.sh
$ ./open_sites.sh
# 默认浏览器打开两个标签页
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `open` 不是 Linux 命令 | `open` 是 macOS 独有，Linux 上没有 | Linux 用 `xdg-open` 替代，两者用法基本一致 |
| 应用名包含空格出错 | shell 会把空格分割成多个参数 | 始终用引号包裹应用名：`-a "Visual Studio Code"` |
| `open -a` 找不到应用 | 应用名写错或不完整 | 用 `mdfind "kMDItemKind == 'Application'"` 列出所有应用名 |
| open URL 时不走代理 | `open` 直接调用系统默认浏览器，不走终端代理 | 用 `https_proxy` 环境变量的场景请用 `curl` 代替 |
| `open -n` 和 `-a` 顺序有要求 | `-n` 必须写在 `-a` 之前 | 写成 `open -n -a Safari` 而非 `open -a Safari -n` |

## 🔗 参考链接

- macOS 手册：`man open`
- [macOS open command on SS64](https://ss64.com/mac/open.html)