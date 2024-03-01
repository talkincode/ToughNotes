---
layout: post
title:  "如何使用命令行安装Streamlit"
date:   2024-02-29 14:20:00 +0800
categories: tech tutorial python streamlit
---

![如何使用命令行安装Streamlit](https://raw.githubusercontent.com/jamiesun/images/master/default/ezcpOz.png)

今天，我们将一起学习如何使用命令行来安装流行的Python库Streamlit。本教程针对初学者以及对流行数据可视化工具Streamlit感兴趣的开发者。安装Streamlit之前，我们会首先创建一个虚拟环境，并在这个环境中进行操作。虚拟环境是用于项目间隔离包依赖关系的实用工具。本教程中，我们将使用Python自带的`venv`和`pip`。

## 准备工作

在安装Streamlit之前，请确保你的电脑已满足以下条件：

1. **Python:** 支持的版本范围是3.8至3.12。请访问Python的[官方下载页面](https://www.python.org/downloads/)以获取相应版本。

2. **Python环境管理器（推荐）：** 使用环境管理器可以为每个项目创建独立的虚拟环境，以隔离不同项目间Python包的安装。推荐使用虚拟环境的原因是：安装或升级Python包可能会对其他包产生不希望的影响。想要了解关于Python虚拟环境的更多信息，您可以阅读[Python虚拟环境入门](https://realpython.com/python-virtual-environments-a-primer/)。

3. **Python包管理器：** 用于处理所有Python包的安装工作。我们将在本指南中使用`pip`。

4. **仅在MacOS上：Xcode命令行工具：** 根据[这些说明](https://mac.install.guide/commandlinetools/4.html)下载Xcode命令行工具来帮助包管理器安装Streamlit的一些依赖项。

5. **代码编辑器：** 我们喜欢使用[VS Code](https://code.visualstudio.com/download)，这也是我们在所有教程中使用的编辑器。

## 使用`venv`创建环境

按照以下步骤，在终端中导航到你的项目文件夹并创建新的虚拟环境：

```bash
cd myproject
python -m venv .venv
```

此时项目文件夹中会出现一个名为“.venv”的文件夹，它将存放虚拟环境以及所有相关依赖。

## 激活你的环境

根据你的操作系统，在终端中运行以下命令以激活你的虚拟环境：

```bash
# Windows command prompt
.venv\Scripts\activate.bat

# Windows PowerShell
.venv\Scripts\Activate.ps1

# macOS和Linux
source .venv/bin/activate
```

激活虚拟环境后，你的命令行提示符前面会显示环境名“(venv)”。

## 在你的环境中安装Streamlit

接下来，在激活的虚拟环境中，安装Streamlit：

```bash
pip install streamlit
```

通过以下命令测试安装是否成功：

```bash
streamlit hello
```

如果无法正常运行，请使用完整形式的命令：

```bash
python -m streamlit hello
```

你应该会看到Streamlit的欢迎页在你的浏览器新标签页中打开。

![](https://raw.githubusercontent.com/jamiesun/images/master/default/4QsdnG.png)

## 创建并运行一个"Hello World"应用

创建一个名为`app.py`的文件，并添加如下代码：

```python
import streamlit as st

st.write("Hello, world!")
```

想要运行新的Streamlit应用，请确保你的环境已经激活，然后在终端中输入：

```bash
streamlit run app.py
```

如果遇到问题，可尝试使用完整形式的命令：

```bash
python -m streamlit run app.py
```

要停止Streamlit服务器，可在终端中按 `Ctrl+C`。

完成后，如果想要停用虚拟环境并回到普通的命令行环境，输入：

```bash
deactivate
```

## 下一步该做什么？

有了这些基础，你现在可以开始进一步研究Streamlit的功能。建议从阅读[主要概念](https://blog.talkincode.net/2024/02/29/Streamlit-main-concepts/)开始，以了解Streamlit的数据流模型。

想回顾安装步骤，请访问[安装教程](https://docs.streamlit.io/get-started/installation)。如果你更喜欢使用图形界面管理你的Python环境，请查看[如何使用Anaconda Distribution安装Streamlit](https://docs.streamlit.io/get-started/installation/anaconda-distribution)。

### 还有疑问？

如果你有任何疑问，Streamlit 的官方[论坛](https://discuss.streamlit.io/)汇集了大量有用信息和Streamlit专家。

希望这篇教程对你有帮助。享受编程之旅！
