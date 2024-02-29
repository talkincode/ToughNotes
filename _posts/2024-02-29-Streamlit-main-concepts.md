---
layout: post
title:  "Streamlit 的主要概念"
date:   2023-02-29 15:20:00 +0800
categories: streamlit python
---

![](https://raw.githubusercontent.com/jamiesun/images/master/default/J0uPIG.png)

Streamlit 是一个开源Python库，它让数据科学家和开发者能够快速创建和分享数据应用。你只需要用很少的代码，就可以把数据分析转化为互动的web应用。以下是Streamlit的一些主要概念，让我们一起来学习如何使用Streamlit！

## 如何运行 Streamlit 应用

安装好Streamlit库后，你可以通过在Python脚本中添加几行Streamlit命令，然后使用下面的命令来运行脚本：

```bash
streamlit run your_script.py
```

当你运行这个命令后，一个本地的Streamlit服务器就会启动，你的应用将会在默认的网络浏览器的新标签页中打开。

## 开发流程

当你在编辑器中更新你的脚本并保存时，Streamlit会检测到变化，并询问你是否想要重新运行你的应用。选择"Always rerun"可以让你的app每次代码更新后都自动刷新。这个交互式的开发过程非常快速方便。

## 如何显示数据

Streamlit支持多种数据展示方式，例如使用 `st.write()` 可以展示文本、表格等，使用 `st.dataframe()` 和 `st.table()` 用于数据展示。

```python
import streamlit as st
import pandas as pd

# 使用st.write()来显示数据表格
st.write("看看这个表格:")
data = pd.DataFrame({
    '第一列': [1, 2, 3, 4],
    '第二列': [10, 20, 30, 40]
})
st.write(data)

# 使用st.dataframe()来显示交互式表格
st.dataframe(data.style.highlight_max(axis=0))
```

## 绘制图表

Streamlit支持多种绘图库，比如Matplotlib, Altair等。你可以用很少的代码来添加各种图表。

```python
import streamlit as st
import numpy as np

# 绘制折线图
chart_data = pd.DataFrame(np.random.randn(50, 3), columns=["a", "b", "c"])
st.line_chart(chart_data)

# 显示地理位置数据
map_data = pd.DataFrame(
    np.random.randn(100, 2) / [50, 50] + [37.76, -122.4],
    columns=['lat', 'lon']
)
st.map(map_data)
```

## 添加交互控件

Streamlit的一个强大功能是可以添加交互控件。

```python
# 添加一个滑块
x = st.slider('选择一个数字')
st.write('你选择的数字的平方是', x * x)

# 添加一个文本输入框，并通过key来存取输入的值
st.text_input("你的名字", key="name")
st.session_state.name  # 用这个方式可以随时获取输入的名字
```

## 页面布局

Streamlit还允许你用 `st.sidebar` 创建侧边栏，用 `st.columns` 和 `st.expander` 控制布局和内容的显示。

```python
# 添加一个选择框到侧边栏
add_selectbox = st.sidebar.selectbox(
    '你想怎样被联系？',
    ('Email', '家庭电话', '移动电话')
)

# 在主页面中创建两栏布局，左边添加一个按钮，右边添加一个选择器
left_column, right_column = st.columns(2)
left_column.button('点我！')
with right_column:
    chosen = st.radio('排序帽选择', ("格兰芬多", "拉文克劳", "赫奇帕奇", "斯莱特林"))
    st.write(f"你被选到了 {chosen} ！")
```

## 显示进度

当你的应用中包含长时间运行的计算时，可以用 `st.progress()` 来显示实时进度。

```python
import streamlit as st
import time

progress_bar = st.progress(0)
for i in range(100):
    progress_bar.progress(i + 1)
    time.sleep(0.1)
st.write('长时间运算完成！')
```

以上就是使用Streamlit的一些基本概念和功能示例。希望这篇文章能够帮助你快速上手Streamlit，开始构建自己的数据应用。如果你有任何问题，可以访问[Streamlit论坛](https://discuss.streamlit.io/)来获取帮助。让我们开始Streamlit之旅吧！
