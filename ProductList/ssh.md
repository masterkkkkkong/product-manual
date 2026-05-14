# SSH — 安全远程连接工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | OpenBSD 团队（OpenSSH） |
| 首次发布 | 1995 年（SSH 协议 1.0）；1999 年（OpenSSH） |
| 用途 | 通过加密通道安全地登录远程服务器、执行命令和传输文件 |
| macOS 自带 | ✅ 是（预装 OpenSSH，开箱即用） |

## 🎯 用来做什么？

- **远程登录服务器**：在本地终端操作远程 Linux/macOS 服务器，像在本地一样执行命令
- **安全传输文件**：结合 scp/rsync 在本地和远程之间安全传文件（全程加密）
- **端口转发（隧道）**：将远程服务器的端口映射到本地，安全访问内网服务
- **免密登录**：配置 SSH 密钥后，登录服务器无需每次输入密码

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ssh user@hostname` | 使用默认端口（22）登录远程服务器 |
| `ssh -p 2222 user@hostname` | 使用指定端口登录 |
| `ssh -i ~/.ssh/id_ed25519 user@hostname` | 使用指定私钥文件登录 |
| `ssh-keygen -t ed25519 -C "your@email.com"` | 生成 SSH 密钥对（推荐 Ed25519 算法） |
| `ssh-copy-id user@hostname` | 将公钥复制到远程服务器（macOS 可能需 `brew install ssh-copy-id`） |
| `scp file.txt user@hostname:/remote/path/` | 将本地文件复制到远程服务器 |
| `scp user@hostname:/remote/file.txt ./` | 从远程服务器下载文件到本地 |
| `ssh -L 8080:localhost:80 user@hostname` | 本地端口转发：将远程 80 端口映射到本地 8080 |
| `ssh -D 1080 user@hostname` | 动态端口转发（SOCKS 代理） |

## 💡 日常使用示例

### 示例 1：通过密码登录远程服务器

```bash
# 基本登录（默认端口 22，当前用户名）
ssh root@192.168.1.100

# 首次连接会提示确认服务器指纹
# The authenticity of host '192.168.1.100 (192.168.1.100)' can't be established.
# ED25519 key fingerprint is SHA256:xxxxxxxxxx.
# Are you sure you want to continue connecting? (yes/no/[fingerprint])
# 输入 yes 并回车确认

# 然后输入密码（输入时不会显示任何字符，这是正常的）
# 登录成功后，命令行提示符会变成远程服务器的
root@remote-server:~#
```

### 示例 2：配置密钥实现免密登录

```bash
# 1. 在本地生成 SSH 密钥对（一路回车使用默认设置即可）
ssh-keygen -t ed25519 -C "my-laptop"

# 输出示例：
# Generating public/private ed25519 key pair.
# Enter file in which to save the key (/Users/me/.ssh/id_ed25519):
# Enter passphrase (empty for no passphrase):
# Enter same passphrase again:
# Your identification has been saved in /Users/me/.ssh/id_ed25519
# Your public key has been saved in /Users/me/.ssh/id_ed25519.pub

# 2. 将公钥复制到远程服务器
# macOS 不自带 ssh-copy-id，可以手动 append：
cat ~/.ssh/id_ed25519.pub | ssh user@hostname "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# 或者用 brew 安装 ssh-copy-id：
# brew install ssh-copy-id
# ssh-copy-id user@hostname

# 3. 设置文件权限（远程服务器上）
# chmod 700 ~/.ssh
# chmod 600 ~/.ssh/authorized_keys

# 4. 现在可以免密登录了
ssh user@hostname  # 直接登录，不再提示密码
```

### 示例 3：使用 SSH 隧道访问内网服务

```bash
# 场景：远程服务器上运行了一个 Web 服务（端口 3000），
# 但防火墙只开放了 22 端口（SSH）。想在本地浏览器访问它。

# 在本地执行端口转发：
ssh -L 8080:localhost:3000 user@remote-server

# 保持这个 SSH 连接不要关闭
# 然后在本地浏览器中打开 http://localhost:8080
# 就能访问远程服务器 localhost:3000 上的服务了
# ——所有流量都经过 SSH 加密隧道

# 更高级的用法：通过跳板机访问内网
# 公司内网服务器 B（192.168.1.200）只能从跳板机 A 访问
ssh -J user@jump-server user@192.168.1.200
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **连接被拒绝** | `Connection refused` | 服务器 SSH 服务未启动，检查：`systemctl status sshd` 或服务器端的 SSH 配置 |
| **密钥权限太大** | `Permissions 0777 for 'id_ed25519' are too open` | 私钥文件权限必须为 600：`chmod 600 ~/.ssh/id_ed25519` |
| **host key 变更** | `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!` | 服务器重装系统导致指纹变化，执行 `ssh-keygen -R hostname` 清除旧记录后重连 |
| **连接超时** | `ssh: connect to host port 22: Operation timed out` | 检查服务器的防火墙是否开放了 22 端口；确认 IP 地址正确 |
| **macOS 没有 ssh-copy-id** | `command not found: ssh-copy-id` | macOS 默认不包含此命令，用 `cat key.pub \| ssh user@host "cat >> ~/.ssh/authorized_keys"` 替代 |
| **免密登录仍要密码** | 配置了密钥但每次还提示输入密码 | 检查远程服务器 `~/.ssh/authorized_keys` 权限是否为 600；`/etc/ssh/sshd_config` 中 `PubkeyAuthentication yes` |
| **端口转发没反应** | 浏览器访问 localhost:8080 白屏 | 确认远程服务的端口正确；检查 SSH 转发是否还在运行（保持终端窗口打开） |

## 🔗 参考链接

- [OpenSSH 官网](https://www.openssh.com/)
- [SSH 使用教程（Arch Linux Wiki，非常详尽）](https://wiki.archlinux.org/title/OpenSSH)
- [SSH 密钥配置指南（GitHub）](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/about-ssh)
- [macOS 上的 SSH 配置说明](https://support.apple.com/zh-cn/guide/terminal/apss55b3a2e4/mac)
