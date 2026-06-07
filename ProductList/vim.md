# vim — 终端文本编辑器

> 指尖不离键盘的编辑器，Unix 世界流传最久的编辑器之一。

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Bram Moolenaar（已故），现由社区维护 |
| 首次发布 | 1991 年（Vi IMproved） |
| 用途 | 在终端中编辑文本文件，全键盘操作，无需鼠标 |
| macOS 版本 | macOS 自带 `/usr/bin/vim`（9.1 版） |
| 替代品 | `nano`（更简单）、`neovim`（现代化 fork） |

## 🎯 用来做什么？

- **快速编辑配置文件** — 终端改 `~/.zshrc`、`nginx.conf`、`package.json`，不需要打开 IDE
- **无图形界面环境** — SSH 连服务器、没有桌面的系统上唯一的编辑方案
- **代码编写** — 语法高亮、代码折叠、宏录制、插件生态，也能做日常开发
- **大文件处理** — 几十 MB 的日志文件用 VSCode 打不开，vim 秒开

## 🔧 常用命令

### 模式切换

| 按键 | 作用 |
|------|------|
| `Esc` | 回到普通模式（Normal mode） |
| `i` | 进入插入模式（Insert mode）——可以打字了 |
| `:` | 进入命令行模式（Command-line mode） |
| `v` / `V` | 进入可视模式（Visual mode）选中字符/行 |

### 文件操作

| 命令 | 作用 |
|------|------|
| `vim file.txt` | 打开文件 |
| `:w` | 保存（write） |
| `:q` | 退出（quit） |
| `:wq` 或 `ZZ` | 保存并退出 |
| `:q!` | 不保存强制退出 |
| `:e file2.txt` | 打开另一个文件 |

### 移动光标（普通模式下）

| 按键 | 作用 |
|------|------|
| `h j k l` | 左 下 上 右（比方向键快） |
| `w` / `b` | 跳到下/上一个单词开头 |
| `0` / `$` | 跳到行首/行尾 |
| `gg` / `G` | 跳到文件头/尾 |
| `:42` + Enter | 跳到第 42 行 |
| `Ctrl+d` / `Ctrl+u` | 向下/上翻半页 |

### 编辑（普通模式下）

| 按键 | 作用 |
|------|------|
| `dd` | 删除当前行 |
| `yy` | 复制（yank）当前行 |
| `p` | 粘贴到光标下方 |
| `u` | 撤销（undo） |
| `Ctrl+r` | 重做（redo） |
| `x` | 删除光标下字符 |
| `/keyword` + Enter | 搜索 `keyword`，按 `n` 跳到下一个 |
| `:%s/old/new/g` | 全文替换 `old` 为 `new` |

## 💡 日常使用示例

### 示例 1：编辑配置文件

```bash
# 打开 ~/.zshrc
vim ~/.zshrc
```

进入 vim 后：
1. 按 `G` 跳到末尾
2. 按 `o` 在下方新建一行并进入插入模式
3. 输入 `alias ll='ls -lah'`
4. 按 `Esc` 回到普通模式
5. 输入 `:wq` 保存退出

### 示例 2：搜索并修改

```bash
# 打开一个文件
echo -e "port=3000\nhost=localhost\ndebug=true" > /tmp/config.txt
vim /tmp/config.txt
```

进入 vim 后：
1. 输入 `/3000` + Enter 搜索 3000
2. 按 `n` 确认位置
3. 按 `ciw`（change inner word）进入插入模式，光标处的单词被删除
4. 输入 `8080`
5. 按 `Esc`，输入 `:wq` 保存

### 示例 3：多文件批量替换

```bash
# 在多个文件中查找替换
vim /tmp/config.txt
```

进入后执行：
```
:%s/localhost/127.0.0.1/g    ← 把全部 localhost 替换为 127.0.0.1
:wq                           ← 保存退出
```

## 🚨 常见坑点

| 症状 | 原因 | 解决 |
|------|------|------|
| 打不出字只会跳光标 | 在普通模式，以为可以直接打字 | 按 `i` 进入插入模式再打字 |
| 按了 `:q` 却说没保存 | 文件被修改过，vim 防止数据丢失 | 用 `:q!` 强制退出，或 `:wq` 先保存 |
| 按方向键出 ABCD | 装了老旧 vim 或兼容模式 | macOS 自带 vim 正常，检查 `~/.vimrc` |
| 退出时 `E37: No write since last change` | 改完忘保存 | `:wq` 或 `ZZ` 保存并退出 |
| `:w` 报 "readonly" | 文件是只读文件或无写权限 | `sudo vim file` 重新以 root 打开 |
| 搜不到想要的词 | 大小写不匹配 | 搜之前输入 `:set ic` 忽略大小写 |
| 复制粘贴缩进错乱 | vim 自动缩进干扰 | 粘贴前 `:set paste`，贴完 `:set nopaste` |

## 🔗 参考链接

- [Vim 官方文档](https://www.vim.org/docs.php)
- [Vim Adventures — 用游戏学 vim](https://vim-adventures.com/)
- [Open Vim — 交互式 vim 教程](https://openvim.com/)
- [Vim Cheat Sheet](https://vim.rtorr.com/)