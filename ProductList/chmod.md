# chmod — 文件权限修改工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 原生命令） |
| 首发年代 | 1971（Unix 第 1 版） |
| 用途 | 修改文件或目录的读/写/执行权限，是 Unix/Linux/macOS 文件系统安全的基础 |

## 🎯 用来做什么？

- **赋予脚本可执行权限** — `chmod +x script.sh` 让脚本能运行
- **设置文件权限** — 只有自己能读写，别人只能读：`chmod 644 file`
- **保护私密文件** — 如 SSH 密钥必须为 `600`，否则 SSH 拒绝工作
- **批量更改目录及内部文件权限** — 配合 `-R` 递归操作

## 🔧 常用命令

### 符号模式（字母方式）

| 命令 | 说明 |
|------|------|
| `chmod +x <file>` | 给所有用户增加执行权限（脚本常用） |
| `chmod -x <file>` | 去掉所有用户的执行权限 |
| `chmod u+w <file>` | 仅给文件所有者（user）增加写权限 |
| `chmod g+r <file>` | 给文件所属组（group）增加读权限 |
| `chmod o-rwx <file>` | 去掉其他人的所有权限 |
| `chmod a=r <file>` | 所有人（all）权限设为只读 |
| `chmod u+x,go-w <file>` | 组合：所有者加执行，组和其他人去掉写 |

### 数字（八进制）模式

每三位数字对应 **所有者(User) — 组(Group) — 其他人(Other)**：

| 数字 | 权限 | 对应字母 | 含义 |
|------|------|----------|------|
| `7` | 读+写+执行 | `rwx` | 完全控制 |
| `6` | 读+写 | `rw-` | 编辑但不执行 |
| `5` | 读+执行 | `r-x` | 可读可执行但不能改 |
| `4` | 只读 | `r--` | 只能看 |
| `0` | 无权限 | `---` | 拒绝所有 |

常用组合：

| 数字 | 说明 | 典型用途 |
|------|------|----------|
| `600` | 所有者可读写，其他人啥都不能 | SSH 私钥 (`id_rsa`) |
| `644` | 所有者可读写，其他人可读 | 普通文件 / 网页静态文件 |
| `755` | 所有者可读写执行，其他人可读可执行 | 脚本 / 程序 / 目录 |
| `700` | 只有所有者能读、写、执行 | 私密脚本或 `.ssh` 目录 |
| `777` | 所有人都能读写执行 | **⚠️ 不推荐**，太开放 |

### 目录权限的特殊说明

| 类型 | 含义 |
|------|------|
| 目录 `r` | 能列出目录里的文件名（`ls`） |
| 目录 `w` | 能在目录里增删改名（需要 `x` 配合） |
| 目录 `x` | 能进入目录（`cd`） |

> 目录默认权限通常是 `755`；如果目录没有 `x`，即使有 `r` 也无法进入。

## 💡 日常使用示例

### 例 1：让一个脚本可以运行

```bash
$ echo 'echo "Hello, World!"' > hello.sh
$ ls -l hello.sh
-rw-r--r--  1 mk  staff  24 Jun  4 09:00 hello.sh

# 现在加执行权限
$ chmod +x hello.sh
$ ls -l hello.sh
-rwxr-xr-x  1 mk  staff  24 Jun  4 09:00 hello.sh

# 运行它
$ ./hello.sh
Hello, World!
```

### 例 2：保护 SSH 私钥（权限不对会被直接拒绝）

```bash
# 假设刚复制了一份 id_rsa，权限过松
$ ls -l ~/.ssh/id_rsa
-rw-rw-rw-  1 mk  staff  1679 Jun  4 09:05 /Users/mk/.ssh/id_rsa

# 这样 SSH 会直接拒绝使用
$ ssh example.com
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0666 for '/Users/mk/.ssh/id_rsa' are too open.

# 修正为 600
$ chmod 600 ~/.ssh/id_rsa
$ ls -l ~/.ssh/id_rsa
-rw-------  1 mk  staff  1679 Jun  4 09:05 /Users/mk/.ssh/id_rsa

# 现在 SSH 正常
$ ssh example.com
Last login: Thu Jun  4 09:06 2026 from 192.168.1.100
```

### 例 3：递归修改网站文件权限

场景：一个 web 项目，文件要 644，目录要 755。

```bash
# 先将所有文件设为 644
$ find /path/to/project -type f -exec chmod 644 {} \;

# 再将所有目录设为 755
$ find /path/to/project -type d -exec chmod 755 {} \;

# 验证：看看有没有权限异常的
$ find /path/to/project -type f ! -perm 644 -o -type d ! -perm 755
# 如果没有输出，说明全部合规
```

## 🚨 常见坑点

| 现象 / 错误 | 原因 | 解决方法 |
|-------------|------|----------|
| `Operation not permitted` 拒绝修改权限 | 文件属于其他用户，或启用了 SIP | 加 `sudo chmod ...`；macOS 系统文件受 SIP 保护 |
| 改了权限后脚本还是报 `Permission denied` | 目录没有 `x` 权限，进不去 | `chmod +x 目录名` 给目录加执行权限 |
| `sudo chmod 777` 后还是不能写 | 文件位于其他用户的家目录，或受 ACL 限制 | 检查 `ls -le` 看 ACL，或用 `chown` 改所有者 |
| macOS 上 /tmp 下的文件改了权限也没用 | macOS 的 `/tmp` 是符号链接到 `/private/tmp` | 用绝对路径操作，或换到 `~/Desktop/` 测试 |
| `chmod -R 755 *` 递归后，SSH 拒绝连接 | 不小心把 `~/.ssh/` 的权限也改了 | SSH 私钥必须是 `600`，`~/.ssh/` 必须是 `700` |
| 给目录 `666` 后还是无法删除文件 | 目录必须有 `w` + `x` 才能增删文件 | 目录权限 `666` 没有 `x`（执行），要给 `777` 或合理组合 |
| `chmod` 对符号链接无效 | chmod 不能直接改符号链接的权限 | 直接操作符号链接指向的源文件 |

## 🔗 参考链接

- [macOS chmod man page](https://ss64.com/mac/chmod.html)
- [Linux chmod 数字权限详解](https://www.gnu.org/software/coreutils/manual/html_node/File-permissions.html)