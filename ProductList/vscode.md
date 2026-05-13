# VS Code — 轻量级代码编辑器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Microsoft |
| 首次发布 | 2015 年 |
| 用途 | 免费、开源、跨平台的代码编辑器，拥有丰富的扩展生态，支持几乎所有编程语言 |

## 🎯 用来做什么？

- **编写代码**：支持语法高亮、智能补全（IntelliSense）、括号匹配、代码格式化等
- **调试程序**：内置调试器，支持断点、变量监视、调用堆栈——无需离开编辑器
- **版本控制**：深度集成 Git，可视化 diff、暂存、提交、推送，不用切到终端
- **远程开发**：通过 Remote SSH 或 Dev Containers，在远程服务器或 Docker 容器中写代码，像在本地一样

## 🔧 常用命令和快捷键

| 操作 | 说明 |
|------|------|
| `Cmd + P` （macOS） | 快速打开文件（输入文件名搜索） |
| `Cmd + Shift + P` | 打开命令面板（执行所有 VS Code 操作） |
| `Cmd + B` | 切换侧边栏显隐 |
| `Cmd + \`` | 切换终端面板显隐 |
| `Ctrl + \``（macOS 终端） | 打开/关闭内置终端 |
| `Cmd + D` | 选中当前单词，再按一次选中下一个相同单词（多光标编辑） |
| `Cmd + Shift + F` | 全局搜索（搜索整个项目中的文本） |
| `F5` | 启动调试 |
| `Cmd + /` | 切换行注释 |
| `Shift + Option + F` | 格式化代码 |

## 💡 日常使用示例

### 示例 1：用命令行从终端打开项目

```bash
# 打开当前目录
code .

# 打开指定文件夹
code ~/projects/my-app

# 打开多个文件
code file1.py file2.js

# 打开文件并定位到指定行
code myfile.py:42

# 比较两个文件
code --diff file1.js file2.js
```

> **提示**：如果 `code` 命令找不到，在 VS Code 中按 `Cmd+Shift+P`，输入 `Shell Command: Install 'code' command in PATH` 并执行。

### 示例 2：安装扩展插件

VS Code 的强大之处在于扩展。打开侧边栏的扩展图标（或按 `Cmd+Shift+X`），搜索安装：

| 扩展名 | 用途 |
|--------|------|
| **Prettier** | 自动格式化代码 |
| **ESLint** | JavaScript/TypeScript 代码规范检查 |
| **Python** | Python 语言支持（补全、调试、Jupyter） |
| **GitLens** | 增强 Git 功能，显示每行代码是谁写的 |
| **Material Icon Theme** | 文件图标主题，一眼识别文件类型 |
| **Live Server** | 一键启动本地 HTTP 服务器，实时预览 HTML |
| **Docker** | 管理 Docker 容器和镜像 |

安装方式（终端）：

```bash
code --install-extension esbenp.prettier-vscode
code --install-extension ms-python.python
code --install-extension eamodio.gitlens
```

### 示例 3：配置个人设置

按 `Cmd+,` 打开设置，或直接编辑 `settings.json`（按 `Cmd+Shift+P` → 输入"Open Settings (JSON)"）：

```json
{
  // 默认编辑器和格式化
  "editor.fontSize": 15,
  "editor.fontFamily": "JetBrains Mono, Menlo, monospace",
  "editor.formatOnSave": true,
  "editor.renderWhitespace": "all",

  // 终端设置
  "terminal.integrated.fontSize": 14,
  "terminal.integrated.defaultProfile.osx": "zsh",

  // Git
  "git.autofetch": true,
  "git.confirmSync": false,

  // 文件排除
  "files.exclude": {
    "**/node_modules": true,
    "**/.git": true
  }
}
```

## 🚨 常见坑点

| 症状 | 原因 | 解决方法 |
|------|------|----------|
| `code: command not found` | VS Code 未将 CLI 工具加入 PATH | 执行 `Cmd+Shift+P` → "Shell Command: Install 'code' command in PATH" |
| 扩展装了但没生效 | 扩展需在特定文件类型下激活 | 确认打开了对应的文件类型（如 .py 文件才能用 Python 扩展） |
| 终端里中文乱码 | 终端编码设置问题 | settings.json 中加 `"terminal.integrated.env.osx": {"LANG": "zh_CN.UTF-8"}` |
| Git 报错 `The current branch has no upstream` | 还没推送到远程 | 点左下角 "Publish Branch" 或运行 `git push -u origin <branch>` |
| 文件搜索搜不到结果 | 文件被 gitignore 或 search.exclude 排除 | 检查 `.gitignore` 或 settings.json 中的 `search.exclude` |
| VS Code 占用内存过高 | 打开的扩展太多或项目太大 | 禁用不需要的扩展，或使用 "File" → "Close Folder" 关闭大项目 |
| 调试器无法启动 | 缺少 launch.json 配置 | 点侧边栏调试 → "create a launch.json file" → 选择对应语言模板 |

## 🔗 参考链接

- [VS Code 官网](https://code.visualstudio.com/)
- [VS Code 文档](https://code.visualstudio.com/docs)
- [VS Code 快捷键速查表 (macOS)](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
- [VS Code 扩展市场](https://marketplace.visualstudio.com/)
