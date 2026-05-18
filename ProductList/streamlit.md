# Streamlit — Python 快速构建数据应用

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Streamlit Inc.（现属 Snowflake） |
| 首次发布 | 2019 年 |
| 用途 | 用纯 Python 快速构建交互式数据应用，无需 HTML/CSS/JavaScript |

## 🎯 用来做什么？

- **数据仪表板**：把 pandas 分析结果变成可交互的 Web 应用，几行代码搞定
- **机器学习 Demo**：快速给模型做一个前端界面，让非技术人员也能使用
- **原型验证**：用 Python 逻辑直接生成 UI，省去前后端联调过程
- **内部工具**：团队内部的报表、监控面板、数据审核工具

## 🔧 常用命令

```bash
# 安装 Streamlit
pip install streamlit

# 检查版本
streamlit --version

# 运行一个 Streamlit 应用
streamlit run app.py

# 运行内置的 Hello 演示应用
streamlit hello

# 在浏览器中打开文档
streamlit docs

# 查看当前配置
streamlit config show

# 清除缓存
streamlit cache clear
```

## 💡 日常使用示例

### 示例 1：Hello World 数据应用

```python
# app.py
import streamlit as st
import pandas as pd
import numpy as np

st.title("我的第一个 Streamlit 应用")

st.write("""
这是一个简单的数据展示应用。
下面是一组随机生成的表格数据。
""")

# 生成随机数据
df = pd.DataFrame(
    np.random.randn(20, 3),
    columns=['A', 'B', 'C']
)

st.dataframe(df)

st.line_chart(df)
```

运行方式：

```bash
streamlit run app.py
```

浏览器会自动打开 `http://localhost:8501`，显示一个带表格和折线图的页面。

### 示例 2：交互式数据筛选器

```python
# iris_explorer.py
import streamlit as st
import pandas as pd
import plotly.express as px

st.title("鸢尾花数据集探索器")

# 加载数据
df = pd.read_csv(
    "https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv"
)

st.sidebar.header("筛选条件")

# 侧边栏筛选项
species = st.sidebar.multiselect(
    "选择物种",
    df['species'].unique(),
    default=df['species'].unique()
)

# 数据筛选
filtered_df = df[df['species'].isin(species)]

st.write(f"当前显示 **{len(filtered_df)}** 条记录")

# 散点图
fig = px.scatter(
    filtered_df,
    x="sepal_length",
    y="sepal_width",
    color="species",
    size="petal_length",
    hover_data=["petal_width"],
    title="花萼长度 vs 花萼宽度"
)
st.plotly_chart(fig, use_container_width=True)

# 显示原始数据
if st.checkbox("显示原始数据"):
    st.dataframe(filtered_df)
```

### 示例 3：文件上传 + 实时分析

```python
# file_analyzer.py
import streamlit as st
import pandas as pd

st.title("CSV 文件分析器")

uploaded_file = st.file_uploader("上传一个 CSV 文件", type=['csv'])

if uploaded_file is not None:
    df = pd.read_csv(uploaded_file)

    st.subheader("数据预览")
    st.dataframe(df.head(10))

    st.subheader("数据概况")
    col1, col2, col3 = st.columns(3)
    col1.metric("行数", df.shape[0])
    col2.metric("列数", df.shape[1])
    col3.metric("缺失值总数", df.isna().sum().sum())

    st.subheader("数值列统计")
    numeric_df = df.select_dtypes(include=['float64', 'int64'])
    st.dataframe(numeric_df.describe())
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `streamlit` 命令找不到 | 未激活虚拟环境 | 确保已 `pip install streamlit` 并且虚拟环境已激活 |
| 页面反复重新运行 | Streamlit 默认热重载，每次保存文件都会 rerun | 这是正常行为，如需禁用：`streamlit run app.py --server.runOnSave false` |
| 图表加载慢 | 大数据集在每次交互时重新计算 | 使用 `@st.cache_data` 装饰器缓存数据加载 |
| 端口 8501 被占用 | 其他 Streamlit 应用正在运行 | 指定其他端口：`streamlit run app.py --server.port 8502` |
| macOS 安装报错 | 缺少 Xcode 命令行工具 | 运行 `xcode-select --install` 安装 |
| 应用部署后无法保存用户输入 | Streamlit 默认无服务器端状态保存 | 使用 `st.session_state` 管理状态，或使用 Streamlit Community Cloud |

### macOS 特别说明

- Streamlit 在 macOS 上表现与 Linux 一致，但首次安装可能需要 Xcode 命令行工具
- 默认端口 8501 在 macOS 上没有特殊限制
- 如果使用 Homebrew 安装的 Python，确保使用的是 pip3 而非 pip

## 🔗 参考链接

- [Streamlit 官网](https://streamlit.io/)
- [Streamlit 官方文档](https://docs.streamlit.io/)
- [Streamlit API 参考](https://docs.streamlit.io/develop/api-reference)
- [Streamlit 应用画廊](https://streamlit.io/gallery)
- [Streamlit GitHub 仓库](https://github.com/streamlit/streamlit)
