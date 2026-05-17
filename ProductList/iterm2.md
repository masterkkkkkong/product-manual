# iTerm2 — macOS 终端增强工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | George Nachman |
| 首次发布 | 2010 年 |
| 用途 | macOS 终端（Terminal.app）的替代品，提供分屏、热键窗口、搜索、配置文件等高级功能 |
| macOS 自带 | ❌ 否（开源免费，从 [iTerm2.com](https://iterm2.com) 下载） |

## 🎯 用来做什么？

- **分屏操作**：一个窗口切多个面板，同时看日志、写代码、运行命令
- **热键窗口**：按快捷键瞬间弹出/隐藏终端，像 IDE 内嵌终端一样方便
- **搜索与跳转**：智能搜索历史输出，支持正则匹配和即时过滤
- **配置文件管理**：为不同场景（开发、运维、SSH）保存独立的配色、字体和启动命令

## 🔧 常用快捷键

| 快捷键 | 说明 |
|--------|------|
| `⌘D` | 垂直分屏（左右两栏） |
| `⇧⌘D` | 水平分屏（上下两栏） |
| `⌘[` 或 `⌘]` | 在分屏之间切换 |
| `⌘T` | 新建标签页 |
| `⌘←` / `⌘→` | 切换标签页 |
| `⌘F` | 搜索终端内容（支持正则） |
| `⌘;` | 自动补全（基于历史输出） |
| `⌥⌘E` | 在所有标签页中搜索 |
| `⌘/` | 光标定位查找 |
| `⌘K` | 清屏（保留回滚历史） |
| `⌘⌥Space` | 呼出/隐藏热键窗口（需在 Preferences 中启用） |

## 🔧 常用命令行功能

| 命令/功能 | 说明 |
|-----------|------|
| `右键点击 URL` | 直接打开链接 |
| `Cmd + 点击文件路径` | 在 Finder 中打开文件所在位置 |
| `三指轻点单词` | 查字典/快速查看文件信息 |
| `⌘⇧.` | 显示/隐藏隐藏文件（在 Open 对话框内） |
| `Profile → Open Profile` | 快速打开预设的 SSH 连接等配置 |

## 💡 日常使用示例

### 示例 1：分屏同时做多件事

```bash
# 场景：本地开发时需要同时看日志、运行服务、编辑代码
# 操作步骤（快捷键在 iTerm2 中）：

# 1. 打开 iTerm2，运行开发服务器
python -m http.server 8000

# 2. 按 ⌘D 垂直分屏 → 右边出现新的面板
#    在新面板中查看日志：
tail -f logs/development.log

# 3. 再按 ⇧⌘D 水平分屏 → 右下角出现第三个面板
#    在第三个面板中运行 git status：
git status

# 效果：一个窗口三个面板，互不干扰。
# 快捷键 ⌘[ / ⌘] 在面板间跳转
```

### 示例 2：使用热键窗口（Visor 模式）

```bash
# 设置方法：
# 1. 打开 Preferences → Keys → Hotkey
# 2. 勾选 "Show/hide iTerm2 with a system-wide hotkey"
# 3. 设置快捷键（建议用 ⌘⌥Space）

# 使用场景：
# - 你在浏览器/编辑器里工作时，想执行一条命令
# - 按 ⌘⌥Space → 终端从屏幕顶部滑出
# - 输入命令 → 再按 ⌘⌥Space → 终端隐藏，回到原来的工作

# 这个功能在 macOS 自带终端上没有，是 iTerm2 的核心特色
```

### 示例 3：智能搜索和跳转

```bash
# 🎯 场景：在冗长的构建输出中找到关键错误

# 运行一个构建
npm run build

# 输出很多行，有 WARNING 隐藏在其中

# 按 ⌘F 打开搜索条
# 输入 ERROR → 匹配词自动高亮
# 按 Tab 或 Enter 跳到下一个匹配
# 按 ⇧Enter 跳到上一个

# 更高级：搜索时勾选 Regex 选项
# 搜索正则：error|warning|failed
# 三个关键词一次搜索！所有问题位置一目了然
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **中文显示为方块** | 运行中文程序时字符显示异常 | Preferences → Profiles → Text → 选择支持中文的字体（如 "JetBrains Mono" 或 "Menlo"） |
| **Ctrl+字母快捷键失效** | 在 tmux 或 vim 中 Ctrl 组合键不生效 | Preferences → Profiles → Keys → 确保 "Left Option" 设为 `Esc+` |
| **分屏后窗口太小** | 分 3-4 个面板后每个都窄到看不清 | 用 `⌘W` 关闭不需要的面板，或调整窗口大小（⌘+ / ⌘- 缩放字体） |
| **热键窗口不出现** | 按了快捷键没反应 | 检查是否有其他应用占用了相同快捷键（如 Spotlight），在 Preferences → Keys → Hotkey 中换一个 |
| **滚动历史丢失** | 关闭 iTerm2 后历史输出清空 | Preferences → Profiles → Terminal → 设置 "Scrollback Lines" 为 "Unlimited" |
| **Alt/Option 键行为奇怪** | 按 Option+B 不能向后跳单词 | Preferences → Profiles → Keys → 将 Left Option 改为 `Esc+` |
| **iTerm2 命令行模式** | 在自动化脚本中想打开 iTerm2 执行命令 | 终端里运行：`open -a iTerm .` 打开当前目录，或 `open -a iTerm /path/to/dir` |

## 🔗 参考链接

- [iTerm2 官方网站](https://iterm2.com/)
- [iTerm2 官方文档](https://iterm2.com/documentation.html)
- [iTerm2 快捷键参考](https://iterm2.com/documentation-one-page.html)
- [GitHub 仓库](https://gitlab.com/gnachman/iterm2)
