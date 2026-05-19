# Markdown — 轻量级标记语言

## 📌 简介

| 项目 | 内容 |
|------|------|
| **开发者** | John Gruber（与 Aaron Swartz 合作设计） |
| **首次发布** | 2004 年 |
| **用途** | 一种易读易写的纯文本格式，可以快速转为 HTML |
| **你就记住** | 用简单的符号给纯文本加格式，不需要鼠标点来点去 |

## 🎯 用来做什么？

- **写笔记**（Obsidian、Notion、Bear 都支持）
- **写文档**（README.md、技术文档、API 文档）
- **写文章**（博客、公众号排版）
- **写代码注释**（GitHub Issue、PR 描述、讨论区回复）
- **写幻灯片**（用 Marp 等工具）

**你在 GitHub 上看到的 README、你正在读的这个文件，都是 Markdown。**

## 🔧 语法速查

### 1. 标题
```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```
效果：字号依次减小，类似于 Word 里的标题样式。

### 2. 文字样式
```markdown
*斜体*
**粗体**
***粗斜体***
~~删除线~~
`行内代码`
```
> 注意：要用英文符号，中文星号不生效。

### 3. 列表
```markdown
无序列表：
- 项目一
- 项目二
  - 子项目（缩进 2 空格）
- 项目三

有序列表：
1. 第一步
2. 第二步
   1. 子步骤（缩进 2 空格）
3. 第三步
```

### 4. 链接和图片
```markdown
[显示文字](https://网址.com)

![图片替代文字](https://图片网址.jpg)
```
> 在 Obsidian 里，链接可以用 `[ [ 笔记名 ] ]` 的双括号语法。

### 5. 引用
```markdown
> 这是一段引用
> 可以跨多行
>
>> 嵌套引用
```

### 6. 代码块
````markdown
```python
print("Hello World")
```

```bash
git status
```

如果不指定语言：
```
纯文本代码块
```
````
> 指定语言后，代码会自动高亮显示。

### 7. 分割线
```markdown
---
```
三个以上的 `-` 或 `*` 都行。

### 8. 表格
```markdown
| 名称 | 价格 | 库存 |
|------|------|------|
| 苹果 | 5元  | 100  |
| 香蕉 | 3元  | 50   |
| 橘子 | 4元  | 80   |
```
效果：

| 名称 | 价格 | 库存 |
|------|------|------|
| 苹果 | 5元  | 100  |
| 香蕉 | 3元  | 50   |
| 橘子 | 4元  | 80   |

### 9. 任务列表
```markdown
- [x] 已完成的事
- [ ] 待完成的事
- [ ] 另一个待办
```
效果：
- [x] 已完成的事
- [ ] 待完成的事

### 10. 脚注
```markdown
这句话有一个脚注[^1]。

[^1]: 这是脚注的内容，显示在页面底部。
```

## 💡 日常使用示例

### 示例 1：写一个简单的 README
```markdown
# 我的小项目

一个有趣的 Python 小工具。

## 安装

```bash
pip install my-tool
```

## 使用

```python
from my_tool import magic
magic.do_it()
```

## 许可证

MIT
```

### 示例 2：在 Obsidian 中做笔记
```markdown
# 2026-05-12 学习笔记

## Python 知识点

- `list.append()` 是原地修改，返回 `None`
- `sorted()` 返回新列表，`list.sort()` 原地排序

## 今日疑问

> 为什么 `is` 和 `==` 有时候结果不一样？

参考链接：[Python 官方文档](https://docs.python.org/3/)

相关笔记：Python 基础
```

### 示例 3：写一个好看的表格
```markdown
| 工具     | 难度 | 用途         | 推荐度 |
|----------|------|-------------|--------|
| Git      | ⭐⭐  | 版本控制      | ⭐⭐⭐⭐⭐ |
| pip      | ⭐    | 装 Python 包  | ⭐⭐⭐⭐⭐ |
| Docker   | ⭐⭐⭐ | 容器化部署    | ⭐⭐⭐⭐  |
| Vim      | ⭐⭐⭐⭐ | 终端编辑器    | ⭐⭐⭐   |
```

## 🎨 进阶小技巧

### 在 Obsidian 中的特殊语法
```markdown
[ [ 笔记名 ] ]       → 链接到另一篇笔记
#标签               → 添加标签
! [ [ 图片.png ] ]  → 嵌入图片（Obsidian 本地）
```

### 在 GitHub 上的特殊功能
```markdown
- [x] 任务列表    → GitHub Issue 里可以直接勾选
:smile:           → Emoji 简码，显示为 😄
@someone          → @提醒某人
#123              → 引用 Issue/PR 编号
```

## 🔗 参考链接

- [Markdown 官方语法](https://daringfireball.net/projects/markdown/syntax)
- [GitHub Flavored Markdown](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
- [Obsidian Markdown 指南](https://help.obsidian.md/Editing+and+formatting/Basic+formatting+syntax)
