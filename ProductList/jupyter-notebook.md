# Jupyter Notebook — 交互式编程环境

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Jupyter 社区（Project Jupyter） |
| 首次发布 | 2014 年（IPython Notebook 的前身可追溯到 2001 年） |
| 用途 | 在浏览器中创建和共享包含代码、公式、图表和说明文字的交互式文档 |
| macOS 安装 | `pip3 install jupyter` 或 `brew install jupyterlab` |

## 🎯 用来做什么？

- **数据探索与分析**：边写代码边看结果，支持 DataFrame 预览、图表渲染，适合做 EDA（探索性数据分析）
- **机器学习实验**：训练模型、查看损失曲线、调整超参数，所有中间结果一目了然
- **教学与演示**：代码、注释、图表混排在一个文档里，适合写教程和汇报材料
- **文档式开发**：把代码和文档写在一起，结果（图片/表格/视频）直接嵌入无需截图

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `pip3 install jupyter` | 安装 Jupyter Notebook（Python 3 版本） |
| `pip3 install jupyterlab` | 安装 JupyterLab（下一代界面，功能更强大） |
| `jupyter notebook` | 启动 Notebook 服务器，在浏览器中打开 |
| `jupyter lab` | 启动 JupyterLab 服务器 |
| `jupyter notebook --port=9999` | 指定端口启动（默认 8888） |
| `jupyter notebook --no-browser` | 启动但不自动打开浏览器（适合远程服务器） |
| `jupyter kernelspec list` | 查看已安装的内核列表 |
| `jupyter nbconvert notebook.ipynb --to html` | 将 Notebook 导出为 HTML 文件 |

## 💡 日常使用示例

### 示例 1：安装并启动 Jupyter Notebook

```bash
# 1. 安装 Jupyter（建议先在虚拟环境中安装）
python3 -m venv myenv
source myenv/bin/activate

# 2. 安装 Jupyter
pip3 install jupyter

# 3. 启动 Notebook 服务器
jupyter notebook

# 终端会输出类似这样的信息：
# [I 15:32:00.001 NotebookApp] Serving notebooks from local directory: /Users/me/project
# [I 15:32:00.001 NotebookApp] Jupyter Notebook 6.5.6 is running at:
# [I 15:32:00.001 NotebookApp] http://localhost:8888/?token=abc123...
# [I 15:32:00.001 NotebookApp] Use Control-C to stop this server

# 浏览器会自动打开 http://localhost:8888/
# 如果没有自动打开，手动复制上面的地址粘贴到浏览器
```

### 示例 2：创建一个数据分析 Notebook

在 Jupyter 界面中点击 "New" → "Python 3 (ipykernel)" 新建笔记本。在单元格中依次输入：

```python
# 单元格 1：导入库
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
```

```python
# 单元格 2：生成示例数据
np.random.seed(42)
data = {
    '月份': ['1月','2月','3月','4月','5月','6月'],
    '销售额': np.random.randint(100, 500, 6),
    '用户数': np.random.randint(1000, 5000, 6)
}
df = pd.DataFrame(data)
df
```

运行后表格会以带样式的 HTML 表格直接渲染在下方。

```python
# 单元格 3：绘制柱状图
plt.figure(figsize=(8, 4))
plt.bar(df['月份'], df['销售额'], color='skyblue')
plt.title('月度销售额')
plt.xlabel('月份')
plt.ylabel('销售额')
plt.show()
```

图表会直接嵌入在单元格下方，无需保存图片文件。

### 示例 3：将 Notebook 导出为其他格式

```bash
# 导出为 HTML（分享给不装 Python 的人看）
jupyter nbconvert analysis.ipynb --to html

# 导出为 PDF（需要 LaTeX 环境）
jupyter nbconvert analysis.ipynb --to pdf

# 导出为 Python 脚本（提取所有代码）
jupyter nbconvert analysis.ipynb --to script

# 导出为 Markdown（适合放 GitHub 仓库）
jupyter nbconvert analysis.ipynb --to markdown
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **安装后找不到 jupyter 命令** | `jupyter: command not found` | 确认已激活虚拟环境；或改用 `python3 -m jupyter notebook` |
| **内核连接失败** | 浏览器显示 "Kernel not found" 或连接中断 | 重启内核：Kernel → Restart；或在终端按 Ctrl+C 两次重启服务器 |
| **文件太大加载慢** | Notebook 中有大量图片/输出，打开很慢 | 清除所有输出：Cell → All Output → Clear；减少单元格中的 print |
| **远程服务器打不开浏览器** | 在服务器上启动后无法访问 | 使用 `--no-browser` + `--port=9999`，本地用 SSH 隧道转发：`ssh -L 9999:localhost:9999 user@server` |
| **matplotlib 中文乱码** | 图表中的中文显示为方框 | 设置中文字体：`plt.rcParams['font.sans-serif'] = ['Arial Unicode MS']`（macOS） |
| **nbconvert 导出 PDF 报错** | `pdflatex not found` | 安装 LaTeX：`brew install mactex-no-gui`（macOS），约 2GB 空间 |
| **Token 忘了** | 笔记本页面要求输入 Token | 在启动服务器的终端中查看 log 中的 `?token=...`，或运行 `jupyter notebook list` |

## 🔗 参考链接

- [Project Jupyter 官网](https://jupyter.org/)
- [Jupyter Notebook 官方文档](https://docs.jupyter.org/en/latest/)
- [JupyterLab 文档](https://jupyterlab.readthedocs.io/)
- [nbconvert 文档](https://nbconvert.readthedocs.io/)
