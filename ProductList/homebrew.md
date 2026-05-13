# Homebrew — macOS 包管理器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Homebrew 核心维护团队（Max Howell 最初创建） |
| 首次发布 | 2009 年 |
| 用途 | macOS（也可用于 Linux）的第三方包管理器，一键安装、更新、卸载开发工具和软件 |

## 🎯 用来做什么？

- **安装命令行工具**：像 `wget`、`ffmpeg`、`tree`、`htop` 这些 Linux 上常用的工具，macOS 不自带，用 Homebrew 一键安装
- **安装图形软件**：通过 Homebrew Cask 安装 Chrome、VS Code、Docker 等 GUI 应用，比去官网下载更方便
- **管理软件版本**：用 `brew upgrade` 统一更新所有已安装的工具，保持最新
- **清理系统**：用 `brew cleanup` 删除旧版本和缓存，释放磁盘空间

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `brew install <包名>` | 安装一个命令行工具（formula） |
| `brew install --cask <包名>` | 安装一个图形界面软件（cask） |
| `brew uninstall <包名>` | 卸载软件 |
| `brew update` | 更新 Homebrew 自身及其软件库（先跑这步再升级软件） |
| `brew upgrade` | 一键升级所有已安装的软件 |
| `brew list` | 列出所有已安装的软件 |
| `brew search <关键词>` | 搜索可安装的软件包 |
| `brew info <包名>` | 查看某个软件的详细信息 |
| `brew cleanup` | 清理旧版本和缓存文件 |
| `brew doctor` | 检查 Homebrew 系统状态，排查问题 |

## 💡 日常使用示例

### 示例 1：安装 wget 并下载文件

```bash
# 1. 搜索确认
brew search wget

# 2. 安装
brew install wget

# 3. 验证
wget --version

# 4. 下载一个文件试试
wget -O test.html https://example.com

# 5. 查看已安装
brew list | grep wget
```

### 示例 2：安装 GUI 应用（VS Code）

```bash
# 安装 Visual Studio Code（带 --cask 表示 GUI 应用）
brew install --cask visual-studio-code

# 安装完成后，直接在终端用 code 命令打开文件
code ~/my-project

# 其他常用 cask
brew install --cask google-chrome
brew install --cask iterm2
brew install --cask docker
```

### 示例 3：日常维护

```bash
# 先更新包索引（推荐升级前先做）
brew update

# 查看哪些软件有更新
brew outdated

# 升级所有可升级的软件
brew upgrade

# 只升级某个特定软件
brew upgrade git

# 清理旧版和缓存
brew cleanup

# 做一次系统检查（推荐定期执行）
brew doctor
```

**输出示例：**
```
$ brew update
Updated 2 taps (homebrew/core, homebrew/cask).
==> New Formulae
aws-crt-cpp
==> Updated Formulae
git  ✔️  openssl

$ brew outdated
python (3.12.0) < 3.13.0
git (2.43.0) < 2.45.0
node (20.11.0) < 22.0.0

$ brew cleanup
Removing: /Users/mk/Library/Caches/Homebrew/... (12.5MB)
==> This operation has freed approximately 12.5MB of disk space.
```

## 🚨 常见坑点

| 症状 | 原因 | 解决方法 |
|------|------|----------|
| `Permission denied` 错误 | macOS 系统完整性保护（SIP）阻止写入 /usr/local | 默认装到 `/opt/homebrew`（Apple Silicon）或 `/usr/local`（Intel） |
| `brew: command not found` | Homebrew 没加到 PATH | 执行 `eval "$(/opt/homebrew/bin/brew shellenv)"` |
| `Error: Formula ... not found` | 包名写错了 | 先用 `brew search <关键词>` 找到正确名 |
| `brew update` 很慢 | 国内网络访问 GitHub 慢 | 配置国内镜像源（中科大/清华） |
| `brew install` 卡在 "Downloading" | 大文件下载或网络波动 | 加 `--verbose` 看进度，或挂代理 |
| `Your Xcode is outdated` | Xcode Command Line Tools 版本太旧 | 执行 `xcode-select --install` 重新安装 |

## 🔗 参考链接

- [Homebrew 官网](https://brew.sh/)
- [Homebrew 文档](https://docs.brew.sh/)
- [Homebrew GitHub](https://github.com/Homebrew/brew)
- [中科大 Homebrew 镜像使用帮助](https://mirrors.ustc.edu.cn/help/brew.git.html)
