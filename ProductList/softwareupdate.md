# softwareupdate — macOS 系统更新命令行工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | Mac OS X 早期版本 |
| 用途 | 在终端中检查、下载、安装 macOS 系统更新、Command Line Tools 和完整 macOS 安装器 |

> ⚠️ **macOS 专属工具**：`softwareupdate` 是 macOS 自带命令，路径通常是 `/usr/sbin/softwareupdate`。Linux 发行版使用 `apt`、`dnf`、`pacman` 等包管理器，不使用此命令。

## 🎯 用来做什么？

- 检查当前 Mac 可安装的系统更新、Safari 更新、Command Line Tools 更新
- 下载或安装推荐更新，适合远程维护 Mac
- 查看历史安装记录，排查「最近更新了什么」
- 列出或下载完整 macOS 安装器，用于制作安装盘或升级系统

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `softwareupdate --help` | 查看可用参数 |
| `softwareupdate --list` | 扫描并列出可用更新 |
| `softwareupdate --list --no-scan` | 不重新扫描，显示上次扫描到的可用更新 |
| `softwareupdate --download --all` | 下载所有可用更新但不安装 |
| `softwareupdate --install --recommended` | 安装推荐更新 |
| `softwareupdate --install --all` | 安装所有可用更新 |
| `softwareupdate --install --all --restart` | 安装所有更新，必要时自动重启 |
| `softwareupdate --history` | 查看通过 softwareupdate 安装的更新历史 |
| `softwareupdate --list-full-installers` | 列出可下载的完整 macOS 安装器 |
| `softwareupdate --fetch-full-installer --full-installer-version <版本>` | 下载指定版本的完整 macOS 安装器 |

## 💡 日常使用示例

### 示例 1：查看可用更新（不触发重新扫描）

```bash
$ softwareupdate --list --no-scan
Software Update Tool

Software Update found the following new or updated software:
* Label: Command Line Tools for Xcode 26.5-26.5
	Title: Command Line Tools for Xcode 26.5, Version: 26.5, Size: 920416KiB, Recommended: YES,
* Label: macOS Tahoe 26.5.1-25F80
	Title: macOS Tahoe 26.5.1, Version: 26.5.1, Size: 2089246KiB, Recommended: YES, Action: restart,
```

如果想强制重新联网扫描，去掉 `--no-scan`：

```bash
$ softwareupdate --list
```

### 示例 2：查看系统更新历史

```bash
$ softwareupdate --history
Display Name                                       Version    Date
------------                                       -------    ----
Command Line Tools for Xcode                       16.2       2026/05/04 21:09:44
Command Line Tools for Xcode                       16.4       2026/05/04 21:09:44
macOS Sequoia 15.7.7                               15.7.7     2026/06/01 00:47:32
macOS Tahoe 26.5                                   26.5       2026/06/01 01:20:10
```

这个命令只显示通过 `softwareupdate` 机制安装的更新，不等于所有 App Store 应用更新记录。

### 示例 3：列出完整 macOS 安装器

```bash
$ softwareupdate --list-full-installers
Finding available software
Software Update found the following full installers:
* Title: macOS Tahoe, Version: 26.5.1, Size: 17872095KiB, Build: 25F80, Deferred: NO
* Title: macOS Tahoe, Version: 26.5, Size: 17872251KiB, Build: 25F71, Deferred: NO
* Title: macOS Sequoia, Version: 15.7.7, Size: 15288576KiB, Build: 24G720, Deferred: NO
```

下载指定安装器：

```bash
$ softwareupdate --fetch-full-installer --full-installer-version 15.7.7
```

下载完成后通常会出现在 `/Applications/Install macOS <name>.app`。这是大文件，执行前确认磁盘空间足够。

## 🚨 常见坑点

| 坑点 | 原因 | 解决方法 |
|------|------|----------|
| `--install --all --restart` 会重启机器 | 系统更新可能要求重启 | 远程机器慎用；先用 `--download --all` 下载，维护窗口再安装 |
| Apple Silicon 上安装某些更新要求 owner 认证 | macOS 安全策略要求本机 owner 用户授权 | 按帮助信息使用 `--user` / `--stdinpass`，不要在脚本中明文保存密码 |
| `--list --no-scan` 看不到新更新 | 只显示上次扫描结果，不联网刷新 | 去掉 `--no-scan` 重新扫描 |
| 完整安装器体积很大 | macOS installer 常见十几 GB | 下载前用 `df -h` 检查剩余空间 |
| 企业设备策略导致更新不可见 | MDM/延迟更新策略可能限制可见版本 | 检查 MDM 策略或 System Settings 中的软件更新状态 |

## 🔗 参考链接

- Apple `softwareupdate` 手册：`man softwareupdate`
- Apple Platform Deployment：Managing software updates：https://support.apple.com/guide/deployment/manage-software-updates-depc4c80847a/web
