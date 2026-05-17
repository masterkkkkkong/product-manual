# Chrome DevTools — 浏览器开发者工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Google Chrome 团队 |
| 首次发布 | 2008 年（随 Chrome 一起） |
| 用途 | 内置于 Chrome 浏览器中的开发者工具，用于调试 HTML/CSS/JavaScript、分析性能、监控网络请求等 |
| macOS 自带 | ✅ 是（Chrome 浏览器内置，无需额外安装） |

## 🎯 用来做什么？

- **调试网页样式**：实时修改 CSS，调整布局、颜色、字体，立即看到效果
- **调试 JavaScript**：设置断点、单步执行、查看变量值，定位代码 bug
- **分析网络请求**：查看每个请求的耗时、状态码、请求头和响应体
- **审计性能**：诊断页面加载慢的原因，优化首屏加载速度
- **模拟移动端**：在不同设备和屏幕尺寸下测试页面表现

## 🔧 常用快捷键

| 快捷键 | 说明 |
|--------|------|
| `⌘⌥I` | 打开 DevTools（默认最后打开的面板） |
| `⌘⌥C` | 打开 DevTools 并直接进入 Elements 面板的检查模式 |
| `⌘⇧C` | 切换到检查元素模式（点击页面元素查看对应 HTML/CSS） |
| `⌘⇧P` | 命令菜单（快速切换面板、开启功能） |
| `⌘[` / `⌘]` | 在面板之间左右切换 |
| `⌘F` | 在当前面板内搜索 |
| `⌘⇧F` | 在所有文件/资源中搜索 |
| `⌘D` | 选择下一个匹配（在 Elements 面板中编辑 DOM 时） |
| `F5` 或 `⌘R` | 刷新页面 |
| `⌘⇧R` | 强制刷新（跳过缓存） |

## 🔧 常用面板

| 面板 | 用途 |
|------|------|
| **Elements** | 查看和修改 HTML 结构 + CSS 样式 |
| **Console** | 查看 JavaScript 日志、运行任意 JS 代码、调试输出 |
| **Sources** | 设置断点、单步调试 JavaScript、查看源码 |
| **Network** | 监控所有网络请求：耗时、大小、状态码、请求/响应详情 |
| **Performance** | 录制和分析页面运行时的性能表现 |
| **Application** | 查看 Cookie、LocalStorage、SessionStorage、IndexedDB 等 |
| **Lighthouse** | 生成页面性能、可访问性、SEO 审计报告 |

## 💡 日常使用示例

### 示例 1：调试 CSS 样式（实时修改页面外观）

```javascript
// 🎯 场景：你觉得导航栏的颜色不太协调，想试试其他颜色

// 操作步骤：
// 1. 在页面上右键点击导航栏 → 选择 "检查（Inspect）"
// 2. DevTools 打开，Elements 面板自动定位到对应的 HTML 元素
// 3. 右侧 Styles 面板中找到 background-color 属性（或 color 属性）
// 4. 双击颜色值直接编辑，输入新颜色：
//    #007AFF  (蓝色)
//    #34C759  (绿色)
//    #FF3B30  (红色)

// 5. 页面颜色实时变化！改完满意后，把修改复制到你的 CSS 源文件中

// 💡 小技巧：点击颜色预览小方块（□），会弹出颜色选择器，
// 支持取色（滴管工具）直接从页面上选颜色
```

### 示例 2：调试 JavaScript（设置断点找 bug）

```javascript
// 🎯 场景：一个按钮点击后没反应，想知道为什么

// 操作步骤：
// 1. 打开 DevTools → Sources 面板
// 2. 找到对应的 .js 文件（按 ⌘P 输入文件名快速定位）
// 3. 点击行号设置断点（出现蓝色箭头）
// 4. 回到页面，点击按钮 → 代码会在断点处暂停

// 暂停后你可以：
// - 在右侧 Scope 面板查看所有变量的当前值
// - 按 F10（Step Over）逐行执行
// - 按 F11（Step Into）进入函数内部
// - 在 Console 面板输入变量名直接查看值

// 示例：如果断点停在这里
function handleClick() {
  // 在 Console 面板里可以输入：
  // > event.target
  // > this
  // > userData
  // 查看这些变量当前是什么
  submitForm(data);  // ← 断点停在这一行
}
```

### 示例 3：分析网络请求（排查 API 问题）

```javascript
// 🎯 场景：页面数据显示不出来，怀疑是接口出问题了

// 操作步骤：
// 1. 打开 DevTools → Network 面板
// 2. 勾选 "Disable cache"（禁用缓存），方便调试
// 3. 刷新页面（⌘R）
// 4. Network 面板里出现所有请求

// 关键信息解读：
// ┌────────┬──────────┬──────┬───────────┬────────┐
// │ Name   │ Status   │ Type │ Size      │ Time   │
// ├────────┼──────────┼──────┼───────────┼────────┤
// │ /api   │ 200      │ json │ 12.3 KB   │ 234 ms │  ← 正常
// │ /data  │ 404      │ json │ 312 B     │ 45 ms  │  ← 地址不对！
// │ /stats │ 500      │ json │ 150 B     │ 1.2 s  │  ← 服务器错误
// └────────┴──────────┴──────┴───────────┴────────┘

// 排查方法：
// - 点击 404 的请求 → 查看 Headers 里的 Request URL 是不是拼错了
// - 点击 500 的请求 → 查看 Response 里的错误信息
// - 用 Filter 输入框只显示 XHR/Fetch 请求，排除图片和 CSS 的干扰

// 查找特定的失败请求：
// 在 Filter 框中输入 "status-code:404" 或 "status-code:500"
// 可以快速筛选出出错的请求
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **修改的 CSS 刷新后没了** | 在 DevTools 里改好了样式，一刷新恢复原样 | DevTools 的修改只对当前标签页有效，刷新后重置。改完后要把修改复制到源代码文件中 |
| **断点没停住** | 设置了断点但代码执行没有暂停 | 检查是否在正确文件的正确行号上；确认 JavaScript 源映射（Source Maps）是否已加载；试着在 Console 加 `debugger;` 语句 |
| **Console 看不到日志** | 使用了 `console.log()` 但 Console 是空的 | 检查 Console 面板是否勾选了 "Filter" → 确认没有过滤掉 "Info" 级别的消息。或者检查其他浏览器扩展是否干扰了页面 |
| **Network 面板空白** | 打开了 Network 面板但页面已经加载完了 | 刷新页面（⌘R），或者在加载前打开 Network 面板，勾选 "Preserve log" 会保留之前的请求记录 |
| **检查手机上的元素** | 想调试移动端页面的样式，但用桌面版 Chrome 不方便 | 打开 DevTools → 点击左上角的 📱 图标（Toggle Device Toolbar），选择 iPhone/Android 设备模拟 |
| **Performance 面板数据看不懂** | 录制完性能数据，全是密密麻麻的色块 | 关注红色的长条（长任务 Long Tasks），从上往下看：Network（网络耗时）→ Frames（帧率）→ Main（主线程活动）。绿色的通常是 HTML 解析，黄色的 JS 执行，紫色的样式计算 |
| **Sources 面板找不到文件** | 页面加载了 JS 但在 Sources 里找不到 | 可能是文件被合并压缩了（Webpack/Vite 构建）。用 ⌘P 命令搜索文件名，或者在 "Page" 标签下展开目录树找 |

## 🔗 参考链接

- [Chrome DevTools 官方文档](https://developer.chrome.com/docs/devtools/)
- [DevTools 快捷键参考](https://developer.chrome.com/docs/devtools/shortcuts/)
- [Chrome DevTools 使用教程（Google Web Fundamentals）](https://developers.google.com/web/tools/chrome-devtools)
- [DevTools 命令菜单（⌘⇧P）速查](https://developer.chrome.com/docs/devtools/command-menu/)
