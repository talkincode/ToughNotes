---
layout: post
title: "为什么少年编程者们应该学习Streamlit：忘掉海龟画图吧"
date: 2024-02-28 00:00:00 -0000
categories: 编程 教育 Streamlit 青少年
---

![为什么少年编程者们应该学习Streamlit：忘掉海龟画图吧](https://raw.githubusercontent.com/jamiesun/images/master/default/z0y8VH.png)

在编程教育领域，尤其是针对青少年受众，“海龟画图”长久以来一直是学习 Python 编程基础的首选方法。尽管通过指令控制一个数字海龟创建形状和图案的魅力无可否认，但编程的领域广阔且不断进化。是时候让我们的年轻编程者们接触到那些不仅能激发他们的兴趣，而且还能为他们准备好面对技术未来的工具了。Streamlit，一个将数据科学和应用开发转化为易接近、有趣且有吸引力的现代工具，就此登场。

## 海龟画图的局限性

海龟画图是理解编程基础的绝佳方式。它教授控制结构、基本语法和过程编程的概念。然而，在掌握了基础知识后，对学习者来说，向更现代且实用的工具和概念进阶是至关重要的。许多教育机构在海龟画图上逗留过长，无意中限制了学生对更广泛编程应用和技术创新的接触。

## Streamlit：现代编程的通道

Streamlit是一个开源应用框架，即使是最初级的程序员也能轻松创建交互式、数据驱动的Web应用。它是一个连接学习编码和将这些技能应用于解决现实问题之间的桥梁。以下是年轻编程者应该开始探索Streamlit的原因：

- **通过交互性激发兴趣**：与静态的海龟画图不同，Streamlit应用是交互式且动态的，提供即时反馈和参与度。这种交互性激发了好奇心，并促进了更深层次的学习。

- **引入数据科学**：Streamlit为数据科学和AI领域提供了一个温和的介绍，这些领域在当今数字时代变得越来越重要。早期的接触可以点燃对这些尖端领域的热情。

- **创造力和创新**：使用Streamlit，年轻编程者可以从简单的数据可视化到复杂的交互式Web应用构建任何东西。这种创造自由促进了创新并鼓励跳出常规思维。

- **为未来做准备**：学习Streamlit为学生装备了在技术行业高度相关的技能。随着他们的成长，他们在创建数据驱动应用的熟练度将为未来在STEM领域的机会做好准备。

## 实战对比

要从海龟画图到Streamlit的转变，拿出一个简单的案例可以清晰地说明其中的区别和进步。假设初学者想要展示一组关于全球智能手机销量的数据，并且想要通过某种形式的可视化来呈现它。

### 海龟画图：静态和基础的可视化

在海龟画图环境中，学生可能会学会如何绘制条形图来展示不同品牌的智能手机销量。该程序可能会涉及一系列的循环和颜色设置指令，比如：

```python
import turtle

# 每个品牌的智能手机销量数据
sales_data = {'Apple': 200, 'Samsung': 150, 'Xiaomi': 120}

# 设置图形画板
screen = turtle.Screen()
screen.title('Smartphone Sales Visualization')
t = turtle.Turtle()
t.width(3)

# 定位起始点
t.penup()
t.goto(-100, -150)
t.pendown()

# 绘制条形图
for brand in sales_data:
    t.fillcolor(brand_colors[brand])
    t.begin_fill()
    t.left(90)
    t.forward(sales_data[brand])
    t.right(90)
    t.forward(50)
    t.right(90)
    t.forward(sales_data[brand])
    t.left(90)
    t.end_fill()
    t.forward(20)
    
turtle.done()
```

此代码段能够生成一个简单的条形图，但它是静态的，并且一旦代码运行结束，就不能再与之交互。

### Streamlit：交互式和动态的应用

使用Streamlit，学生可以创建一个交互式的应用，这个应用不仅让用户查看智能手机销量的可视化数据，而且用户可以根据自己的选择动态更新图表。一个基础的Streamlit脚本可能看起来像这样：

```python
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# 模拟智能手机销量数据
sales_data = {
    'Brand': ['Apple', 'Samsung', 'Xiaomi'],
    'Sales': [200, 150, 120]
}

# 将数据转换成DataFrame以方便操作
df = pd.DataFrame(sales_data)

# Streamlit应用标题
st.title('Smartphone Sales Visualization')

# 用户可以选取展示特定品牌的销量
selected_brands = st.multiselect('Select brands', df['Brand'].unique(), default=df['Brand'].unique())

# 根据用户的选择过滤数据
filtered_df = df[df['Brand'].isin(selected_brands)]

# 创建条形图
fig, ax = plt.subplots()
ax.bar(filtered_df['Brand'], filtered_df['Sales'])

# 将创建的条形图显示在Streamlit应用上
st.pyplot(fig)
```

![Streamlit：交互式和动态的应用](https://github.com/talkincode/toughradius/assets/377938/589824a9-781d-4c9e-a0e7-2791aa52e9fc)

这段脚本创建了一个用户可以实时交互的Web应用。它不仅具有选择要查看哪些品牌数据的功能，而且还能立即看到其结果的视觉表示。

通过这个简单的对比，学生可以直观地看到编程技术的进步如何从基本的绘图转变为创建富有互动性的实用应用程序。这对于编程教育来说是一次质的飞跃，使学习者得以全面发展编程技能并准备好迈向未来。

### 点评

`海龟画图` 仅仅是一种编程技巧的练习， 再夸张一点也仅仅是一种有趣的技巧练习， 提供一段时间的学习是没有问题， 问题是有些培训机构对学生教了半年的Python 课程还在`海龟画图` 就显得有些滑稽了。

为什么会拿 Streamlit 来对比， 因为 Streamlit同样具备了简单有趣的特性， 同时它面向现实世界， 面向科学世界， 不管学习还是工作，研究， streamlit 总能给你带来惊喜。

## 拥抱 Streamlit

从海龟画图向Streamlit的转变对青少年初学者来说可以是一段激动人心的旅程。教育者和家长可以通过以下方式促进这一转变， 可以试试为自己的学习打造一些很酷的工具，尝试使用 streamlit 结构 AI 实现个性化学习工具，以下是一些小贴士：

- 使用 streamlit 做一些数学学习工具， 比如- 探索多维的几何图像构建：Streamlit可以和各种绘图库结合使用，如Plotly，使得学生可以通过编程创建立体的几何图形，并通过简单的滑块和按钮来观察它们在不同角度的投影和转换，增加几何学习的互动性和趣味性。

- 创建个性化英语学习工具， 比如- 使用 Streamlit 制作词汇量测试应用：学生可以编写一个词汇测试小程序，利用Streamlit框架提供的多选项功能来呈现问题，自动记录回答，并在测试结束后给出结果。例如，学生可以创建一个包含单词和定义的数据库，随机产生问题并让用户匹配正确的定义。

- 打造自己的个性化聊天机器人，比如利用开放的大模型 API 和 Streamlit 用户界面，青少年编程者可以创建一个简单的聊天机器人。他们可以根据自己的爱好和兴趣来定制聊天内容，或者集成学习模块帮助同龄人复习科学或数学题目。
