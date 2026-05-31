# nano — 简单易用的终端文本编辑器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU 项目（Chris Allegretta 创始） |
| 首次发布 | 1999 年 |
| 用途 | 在终端中编辑文本文件，比 vim/emacs 更容易上手 |
| macOS 自带 | ✅ 是（预装在 macOS 中） |

## 🎯 用来做什么？

- **快速修改配置文件**：几秒钟打开、编辑、保存退出
- **写简单的脚本或笔记**：不需要打开 GUI 编辑器
- **初学者友好**：底部有快捷键提示，不用记复杂命令
- **SSH 远程编辑**：远程连上服务器后直接用 nano 改文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `nano <file>` | 打开（或新建）一个文件编辑 |
| `^O`（Ctrl+O） | 保存文件（WriteOut） |
| `^X`（Ctrl+X） | 退出 nano |
| `^W`（Ctrl+W） | 搜索文本 |
| `^\`（Ctrl+\） | 搜索并替换文本 |
| `^K`（Ctrl+K） | 剪切当前行（Cut） |
| `^U`（Ctrl+U） | 粘贴（UnCut） |
| `^G`（Ctrl+G） | 查看帮助文档 |

> `^` 表示 Ctrl 键，`^O` 即按住 Ctrl 再按 O。

## 💡 日常使用示例

### 1️⃣ 新建并编辑一个文件

```bash
# 新建/打开 test.txt
$ nano test.txt

# nano 界面底部会显示：
# ^G 求助    ^O 写入    ^W 搜索    ^K 剪切    ^J 整理    ^C 游标位置
# ^X 离开    ^R 读档    ^\ 替换    ^U 还原    ^T 拼写    ^_ 行号
#
# 操作步骤：
# 1. 输入文本内容（直接打字即可，和普通编辑器一样）
# 2. 按 Ctrl+O 保存
# 3. 看到提示 "File Name to Write: test.txt"，按 Enter 确认
# 4. 按 Ctrl+X 退出
```

**验证文件已保存：**

```bash
$ cat test.txt
这是我在 nano 中写的内容
```

### 2️⃣ 在 SSH 远程服务器上编辑配置文件

```bash
# 先 SSH 连上远程服务器
$ ssh user@your-server.com

# 编辑 Nginx 配置
$ sudo nano /etc/nginx/nginx.conf

# 修改完后：
# Ctrl+O → Enter（保存）
# Ctrl+X（退出）

# 重载 Nginx 使配置生效
$ sudo nginx -s reload
```

### 3️⃣ 搜索和替换文本

```bash
$ nano notes.txt

# 搜索：按 Ctrl+W，输入 "hello"，回车 → 光标跳到第一个匹配
# 继续搜索下一个：再按 Ctrl+W，直接回车（保留上次搜索词）
#
# 替换：按 Ctrl+\
# 1. 输入被替换的词 "hello"，回车
# 2. 输入替换的词 "world"，回车
# 3. 选择：Y=替换这个，A=全部替换，N=跳过
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| 编辑后提示 `[Error writing ...]` | 没有写权限 | 用 `sudo nano <file>` 重新打开 |
| 不小心按了 Ctrl+S | Ctrl+S 是终端冻结快捷键 | 按 `Ctrl+Q` 解除冻结 |
| 不知道快捷键是什么 | 记不住组合键 | 按 `Ctrl+G` 查看完整帮助 |
| 无法粘贴外部文本 | 鼠标中键可能不工作 | 使用终端的"粘贴"功能（Cmd+V 或 Shift+Ctrl+V） |
| `nano --version` 报错 `TERM not set` | 没有终端环境变量 | 设置 `export TERM=xterm` 或直接用 `nano` 打开文件即可 |
| 拼写检查用不了 | macOS 不自带拼写检查程序 | 不用管，直接跳过即可 |

**macOS 差异：**
- macOS 预装的是 GNU nano，和 Linux 的 nano 基本一致
- 在 macOS 终端中，复制用 `Cmd+C`、粘贴用 `Cmd+V`（不是 Ctrl+Shift+V）
- macOS 自带的 nano 版本可能较旧，可通过 `brew install nano` 安装最新版
- 如果遇到 `<unconverted>` 乱码，用 `nano --encoding=utf-8 <file>` 打开

## 🔗 参考链接

- [GNU Nano 官方文档](https://www.nano-editor.org/docs.php)
- [macOS man page - nano](https://ss64.com/osx/nano.html)
- [Nano 快捷键速查表](https://www.nano-editor.org/dist/latest/cheatsheet.html)