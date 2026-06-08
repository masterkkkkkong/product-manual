# ln — 文件链接工具（硬链接与符号链接）

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 创始团队） |
| 首次发布 | 1971 年（Unix V1） |
| 用途 | 创建文件的硬链接（hard link）和符号链接（symbolic link），实现文件复用和快捷访问 |
| macOS 自带 | ✅ 是 |

## 🎯 用来做什么？

- **创建快捷方式**：用符号链接让一个文件出现在多个位置，但不占用额外空间
- **管理 dotfiles**：将配置文件符号链接到 `$HOME`，实际文件存在版本控制的目录里
- **简化路径**：把深层目录映射到浅层路径，方便快速访问
- **节省磁盘**：硬链接让多个路径指向同一份数据，修改一处同步更新

### 硬链接 vs 符号链接

| 特性 | 硬链接 | 符号链接（软链接） |
|------|--------|-------------------|
| 创建命令 | `ln target link` | `ln -s target link` |
| 指向 | 同一 inode（磁盘上的数据块） | 目标文件的路径 |
| 跨文件系统 | ❌ 不能 | ✅ 可以 |
| 指向目录 | ❌ 不能（特殊权限除外） | ✅ 可以 |
| 原文件删除后 | 链接仍可访问数据 | 链接变成断链（broken link） |
| 文件大小 | 0（不占额外空间） | 存储目标路径的字符串大小 |
| 查看 inode | `ls -i` 显示相同的 inode 号 | `ls -i` 显示不同的 inode 号 |

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `ln -s target link_name` | 创建符号链接（软链接） |
| `ln target link_name` | 创建硬链接 |
| `ln -sf target link_name` | 强制创建/覆盖符号链接 |
| `ln -sv target link_name` | 创建符号链接并显示详细信息 |
| `ln -sh target link_name` | 如果 target 是符号链接，不跟踪它（macOS） |
| `ls -l` | 查看文件，符号链接会显示 `->` 指向 |
| `readlink link_name` | 查看符号链接指向的目标路径 |

## 💡 日常使用示例

### 示例 1：用符号链接管理 dotfiles

这是符号链接最经典的使用场景——把配置文件集中管理：

```bash
# 假设你在版本控制下管理 dotfiles
mkdir -p ~/dotfiles

# 把实际的配置文件移到 dotfiles 目录
mv ~/.zshrc ~/dotfiles/zshrc

# 创建符号链接，让系统以为 .zshrc 还在原位置
ln -s ~/dotfiles/zshrc ~/.zshrc

# 验证
ls -l ~/.zshrc
# 输出：lrwxr-xr-x  1 mk  staff  25 Jun  9 14:00 /Users/mk/.zshrc -> /Users/mk/dotfiles/zshrc
# 注意权限位的第一个字母是 l（link），表示符号链接
```

### 示例 2：简化深层路径

```bash
# 假设你经常访问一个深层目录
mkdir -p ~/work/projects/backend/api/v2/docs

# 在 home 目录创建一个快捷方式
ln -s ~/work/projects/backend/api/v2/docs ~/apidocs

# 现在可以直接访问
cd ~/apidocs
pwd
# 输出：/Users/mk/apidocs（但实际是符号链接）
pwd -P
# 输出：/Users/mk/work/projects/backend/api/v2/docs（显示真实路径）
```

### 示例 3：硬链接实现文件共享

```bash
# 创建一个文件
echo "这是重要数据" > original.txt
cat original.txt
# 输出：这是重要数据

# 创建硬链接
ln original.txt copy.txt

# 两个文件名指向同一份数据
ls -li original.txt copy.txt
# 输出：
# 8934721 -rw-r--r--  2 mk  staff  23 Jun  9 14:00 copy.txt
# 8934721 -rw-r--r--  2 mk  staff  23 Jun  9 14:00 original.txt
# 注意：inode 号相同（8934721），链接数（第3列）为 2

# 修改任意一个，另一个自动同步
echo "追加内容" >> original.txt
cat copy.txt
# 输出：
# 这是重要数据
# 追加内容

# 删除原文件，硬链接依然可用
rm original.txt
cat copy.txt
# 输出：
# 这是重要数据
# 追加内容

# 对比：符号链接删除原文件后变成断链
echo "临时文件" > temp.txt
ln -s temp.txt link_to_temp.txt
rm temp.txt
cat link_to_temp.txt
# 输出：cat: link_to_temp.txt: No such file or directory

rm copy.txt link_to_temp.txt
```

### 示例 4：修复断链和查看链接

```bash
# 创建然后断开
echo "hello" > source.txt
ln -s source.txt mylink.txt
mv source.txt /tmp/
cat mylink.txt
# 输出：cat: mylink.txt: No such file or directory（断链）

# 查看链接目标
readlink mylink.txt
# 输出：source.txt

# 修复：重新创建目标文件或重建链接
echo "hello" > source.txt
cat mylink.txt
# 输出：hello

rm mylink.txt source.txt
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **符号链接指向相对路径** | 移动目录后链接全部失效 | 创建时用绝对路径：`ln -s /full/path/to/target link` |
| **`ln -s` 的参数顺序搞反** | 生成了一个奇怪的链接 | 记住：**`ln -s 目标文件 链接名`**（和 `cp` 一样：先源后目标） |
| **已存在同名文件** | `ln -s target existing` 报错 | 加 `-f` 强制覆盖：`ln -sf target link` |
| **macOS 上符号链接导致 GUI 应用问题** | Finder 或某些应用不跟踪符号链接 | 某些 macOS 应用（如 Xcode）不识别符号链接，需要改用硬链接或 alias（macOS 特有） |
| **Git 仓库中的符号链接** | 在 Windows 上 clone 出问题 | 在 Windows 上需要启用开发者模式才能创建符号链接 |
| **硬链接不能跨分区** | `ln: link across devices not permitted` | 硬链接只能在同一个文件系统内创建。跨分区用 `ln -s` 或 `cp` |
| **目录不能硬链接** | `ln: dir: Operation not permitted` | macOS/Unix 不允许对目录创建硬链接（`.` 和 `..` 是特例）。用符号链接代替 |
| **符号链接的权限问题** | 目标文件可读，但链接访问被拒绝 | 检查目标文件及其父目录的权限，符号链接自身的权限通常被忽略 |
| **`rm` 链接还是目标？** | 不确定删除哪个 | `rm link` 只删除链接本身，不影响目标文件。目标文件需要单独删除 |

## 🔗 参考链接

- [macOS ln man page（官方）](https://man.freebsd.org/cgi/man.cgi?query=ln)
- [GNU ln 手册（coreutils）](https://www.gnu.org/software/coreutils/manual/html_node/ln-invocation.html)
- [维基百科 — Symbolic link](https://en.wikipedia.org/wiki/Symbolic_link)
- [Arch Linux Wiki — Symbolic links](https://wiki.archlinux.org/title/Symbolic_links)
