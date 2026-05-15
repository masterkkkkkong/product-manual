# scp / rsync — 文件传输工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | OpenBSD 项目 (scp) / Andrew Tridgell, Paul Mackerras (rsync) |
| 首次发布 | scp: 1995 年 / rsync: 1996 年 |
| 用途 | 在本地和远程服务器之间安全传输文件和目录 |

> **scp**（Secure Copy）基于 SSH 协议，简单直接，下载上传很快上手。
> **rsync**（Remote Sync）更强大——支持增量传输、同步删除、断点续传、只传差异部分。

## 🎯 用来做什么？

- **上传/下载文件**：把本地文件传到服务器，或从服务器下载
- **备份与同步**：用 rsync 本地增量备份，只传变化的部分，速度极快
- **部署代码**：远程部署时将新版本同步到服务器
- **大文件传输**：rsync 支持断点续传，网络中断后不用从头开始

## 🔧 常用命令

### scp 常用命令

| 命令 | 说明 |
|------|------|
| `scp file.txt user@host:/path/` | 上传本地文件到远程服务器 |
| `scp user@host:/path/file.txt .` | 从远程服务器下载文件到当前目录 |
| `scp -r mydir/ user@host:/path/` | 递归上传整个目录 |
| `scp -P 2222 file.txt user@host:/path/` | 指定 SSH 端口（大写的 P） |
| `scp -i ~/.ssh/key.pem file.txt user@host:/path/` | 指定密钥文件 |

### rsync 常用命令

| 命令 | 说明 |
|------|------|
| `rsync -av source/ dest/` | 本地同步目录（-a 归档模式保留权限, -v 详细输出） |
| `rsync -av source/ user@host:/path/` | 上传到远程服务器 |
| `rsync -av user@host:/path/ ./` | 从远程服务器下载到本地 |
| `rsync -avz source/ dest/` | `-z` 压缩传输（适合慢网络） |
| `rsync -av --progress source/ dest/` | 显示每个文件的传输进度 |
| `rsync -av --delete source/ dest/` | 删除目标端多余文件，保持两边完全一致 |
| `rsync -av --dry-run source/ dest/` | 模拟运行，先看会做什么，不实际传输 |
| `rsync -e 'ssh -p 2222' source/ user@host:/path/` | 指定 SSH 端口 |

## 💡 日常使用示例

### 示例 1：用 scp 上传网站文件到服务器

```bash
# 上传单文件到服务器家目录
scp index.html user@192.168.1.100:~

# 上传整个网站目录（注意 -r 递归）
scp -r dist/ user@192.168.1.100:/var/www/html/

# 指定端口（如果 SSH 不是默认 22 端口）
scp -P 2222 -r dist/ user@192.168.1.100:/var/www/html/

# 从服务器下载日志到本地
scp user@192.168.1.100:/var/log/app.log ./app-backup.log
```

### 示例 2：用 rsync 增量备份项目目录

```bash
# 本地备份：把 project/ 同步到 backup/project/
# 第一次运行：全量复制
# 第二次运行：只复制修改过的文件
rsync -av --progress project/ backup/project/

# 输出示例：
building file list ... done
./
main.py
utils.py
config.json
test/
test/test_api.py

sent 12,345 bytes  received 567 bytes  25,824.00 bytes/sec
total size is 98,765  speedup is 7.64

# 修改一个文件后再次运行 —— 只有它被传输
rsync -av --progress project/ backup/project/
main.py
sent 1,234 bytes  received 56 bytes  2,580.00 bytes/sec
```

### 示例 3：用 rsync 部署代码到远程服务器

```bash
# 先把项目同步到服务器，排除 node_modules 和 .git
rsync -avz --delete --exclude='node_modules' --exclude='.git' \
  ./myapp/ user@myserver.com:/var/www/myapp/

# 参数解释：
# -a      归档模式（保留权限、时间戳、符号链接等）
# -v      显示详细信息
# -z      压缩传输（加快速度）
# --delete 删除目标端多余的文件（确保两边完全一致）
# --exclude 跳过不需要的目录

# 模拟运行 —— 先看看会做什么
rsync -avz --delete --dry-run ./myapp/ user@myserver.com:/var/www/myapp/
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| scp 路径结尾的 `/` 很关键 | `scp -r dir/ host:dest` 拷贝目录**内容**，`scp -r dir host:dest` 拷贝**目录本身** | 确认目标路径以 `/` 结尾表示只拷贝内容 |
| rsync 源路径结尾的 `/` 也很关键 | `rsync -av src/ dest` 拷贝内容，`rsync -av src dest` 拷贝目录本身 | 记住：源路径加 `/` = 只拷贝里面的东西 |
| SSH 连接超时 | 大文件传输中断 | rsync 支持续传：`rsync -av --partial source/ dest/` |
| macOS 上 rsync 版本旧 | macOS 自带的是 openrsync，功能有限 | `brew install rsync` 安装完整版 GNU rsync |
| 第一次连接确认主机 | 首次连接远程主机需要确认指纹 | 输入 `yes` 确认，或使用 `-o StrictHostKeyChecking=no`（不推荐） |
| 权限问题 | 目标目录没有写权限 | 使用 `sudo` 或先确认目标目录权限：`ls -ld /var/www/html/` |
| SCP 在 macOS 默认使用 SFTP 协议 | macOS 新版 scp 底层用 SFTP 而非老式 SCP 协议 | 如果需要老式 SCP 协议，加 `-O` 参数 |

## 🔗 参考链接

- [scp man 手册](https://man.openbsd.org/scp)
- [rsync 官网](https://rsync.samba.org/)
- [rsync man 手册](https://man.openbsd.org/rsync)
- [rsync 使用示例和详解](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories)
