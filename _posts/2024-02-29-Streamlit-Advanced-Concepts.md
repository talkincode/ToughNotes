---
layout: post
title:  "探索Streamlit的高级概念与技巧"
date:   2024-02-29 21:10:37 +0800
categories: streamlit webapp development
---

![探索Streamlit的高级概念与技巧](https://raw.githubusercontent.com/jamiesun/images/master/default/q4FYXx.png)

在我们了解了Streamlit如何运行和处理数据后，今天我们来讨论如何提高效率。我们将重点介绍缓存和会话状态（Session State），这些功能可以让你在应用程序之间保存数据和状态，避免不必要的重复计算，从而创建动态页面和处理渐进式的进程。

## 缓存（Caching）

缓存的主要作用是即使在从网络加载数据、操作大型数据集或执行昂贵的计算时，也保持你的应用高效运行。

缓存的基本原理是存储昂贵的函数调用结果，在再次遇到相同输入值时直接返回缓存结果，避免重复执行函数。

在Streamlit中，要使用缓存功能，需要对相应函数应用缓存装饰器（caching decorator）。你有两个选项：

- `st.cache_data` 是缓存返回数据的计算结果的推荐方式。如果你要缓存的函数返回可序列化的数据对象（如str、int、float、DataFrame、dict、list），则使用`st.cache_data`。**它会在每次函数调用时创建数据的新副本**，确保数据不会产生变异和并发问题。在大多数情况下，如果你不确定如何缓存，那就从`st.cache_data`开始吧！
- `st.cache_resource` 用于缓存全局资源，如机器学习模型或数据库连接。如果你的函数返回的是不可序列化的对象，你又不希望多次加载它，就使用`st.cache_resource`。**它返回的是缓存的对象本身**，这个对象会在所有重运行和会话中共享，无需复制或重复生成。如果你改变了一个用`st.cache_resource`缓存的对象，那么这种变化将会在所有重运行和会话中存在。

比如，以下是使用缓存的示例代码：

```python
@st.cache_data
def long_running_function(param1, param2):
    return ...
```

以上示例中，`long_running_function`被`@st.cache_data`装饰。因此，Streamlit将记录以下信息：

- 函数名称（`"long_running_function"`）。
- 输入值的详情（`param1`, `param2`）。
- 函数内部的代码。

在执行`long_running_function`内部的代码之前，Streamlit会检查其缓存中是否已保存了之前的结果。如果找到了一个为给定函数和输入值缓存的结果，它会返回该缓存结果，而不重新运行函数的代码。否则，Streamlit会执行该函数，将结果保存在缓存中，并继续执行脚本。在开发过程中，当函数的代码发生变化时，缓存会自动更新，确保最新的更改反映在缓存中。

想要深入了解Streamlit缓存装饰器、其配置参数和限制，请参见[官方缓存文档](https://docs.streamlit.io/library/advanced-features/caching)。

## 会话状态（Session State）

会话状态提供了类似字典的接口，你可以在脚本重运行之间保存信息。使用`st.session_state`和键或属性标记来存储和回调值。例如，`st.session_state["my_key"]`或`st.session_state.my_key`。不过请记住，小部件本身就会自行处理状态，所以你并不总是需要使用会话状态！

### 什么是会话？

一个会话是指在浏览器中查看应用的一个单独实例。如果你在两个不同的浏览器标签页中查看应用，每个标签页将拥有自己的会话。因此，每个查看者都有与他们特定视图相关的会话状态。用户与应用交云服务器协，其中包括刷新浏览器页面或重新加载应用URL时，他们的会话状态将重置，重新开始一个新的会话。

### 使用会话状态的示例

这是一个简单的应用程序，它可以计数页面被运行的次数。每次点击按钮，脚本就会重新运行。

```python
import streamlit as st

if "counter" not in st.session_state:
    st.session_state.counter = 0

st.session_state.counter += 1

st.header(f"This page has run {st.session_state.counter} times.")
st.button("Run it again")
```

- **首次运行：** 对于每个用户，第一次运行应用时，会话状态是空的。因此，会创建一个键值对（`"counter": 0`）。随着脚本的继续执行，计数器立即增加（`"counter": 1`），并显示结果："This page has run 1 times." 当页面完全呈现后，脚本运行结束，Streamlit服务器等待用户作出反应。当用户点击按钮时，就开始了重运行。

- **第二次运行：** 由于"counter"已经是会话状态中的一个键，它不会重新初始化。脚本继续执行，计数器增加（`"counter": 2`），并显示结果："This page has run 2 times."

在上面的示例中，会话状态用于你希望从一次重运行到下一次逐渐建构的渐进式过程。会话状态也可用于防止重新计算，类似于缓存。然而，区别很重要：

- 缓存将存储的值与特定的函数和输入关联起来。缓存的值对所有用户和所有会话都是可访问的。
- 会话状态将存储的值与键（字符串）关联起来。会话状态中的值只在保存它的那个会话中可用。

如果你的应用程序中有随机数生成，你可能会使用会话状态。这里有一个示例：每个会话开始时，都会随机生成数据。将这些随机信息保存在会话状态中，每个用户打开应用时都会得到不同的随机数据，但在他们与应用交云服务器协时，数据不会不断改变。如果你选择不同的颜色，你会看到数据不会因每次重运行而重新随机生成。（如果你在一个新标签页中打开应用以开始一个新会话，你会看到不同的数据！）

```python
import streamlit as st
import pandas as pd
import numpy as np

# 数据初始化，只在首次加载页面时执行
if "df" not in st.session_state:
    st.session_state.df = pd.DataFrame(np.random.randn(20, 2), columns=["x", "y"])

st.header("Choose a datapoint color")
color = st.color_picker("Color", "#FF0000") # 颜色选择器
st.divider() # 分割线
st.scatter_chart(st.session_state.df, x="x", y="y", color=color) # 绘制散点图
```

如果你正在获取所有用户都相同的数据，你可能会缓存一个检索该数据的函数。另一方面，如果您获取的是特定于用户的数据，例如查询他们的个人信息，您可能想将其保存在会话状态中。这样，查询的数据只在那一个会话中可用。

正如在[主要概念](https://docs.streamlit.io/get-started/fundamentals/main-concepts#widgets)中提到的，会话状态也与小部件相关。小部件像魔法一样，静静地处理自己的状态。但作为一个高级功能，你可以通过为它们分配键来在代码中操控小部件的值。分配给小部件的任何键都会成为会话状态中与该小部件值相关的键。这可以用来操控小部件的值。在你理解Streamlit的基础知识后，如果你感兴趣，可以查看[小部件行为指南](https://docs.streamlit.io/library/advanced-features/widget-behavior)，深入了解详情。

## 连接管理

上面提到了，你可以使用`@st.cache_resource`来缓存连接。这是最通用的解决方案，允许你使用几乎任何Python库的连接。不过，Streamlit也为一些最流行的连接提供了便捷的处理方法，比如SQL！`st.connection`为你处理了缓存，所以你可以用更少的代码行数。从数据库获取数据可以很简单：

```python
import streamlit as st

conn = st.connection("my_database") # 创建数据库连接
df = conn.query("SELECT * FROM my_table") # 执行查询
st.dataframe(df) # 显示数据
```

当然，你可能想知道你的用户名和密码要放在哪里。Streamlit拥有一个便捷的[秘密管理机制](https://docs.streamlit.io/library/advanced-features/secrets-management)。现在，我们只需要看看`st.connection`如何很好地与秘密配合使用。在你的本地项目目录中，你可以保存一个`.streamlit/secrets.toml`文件。你将你的秘密信息保存在这个toml文件中，然后`st.connection`就会使用它们！例如，如果你有一个应用程序文件`streamlit_app.py`，你的项目目录可能看起来像这样：

```bash
your-LOCAL-repository/
├── .streamlit/
│   └── secrets.toml # 一定要添加到.gitignore中！
└── streamlit_app.py
```

对于上面的SQL示例，你的`secrets.toml`文件可能如下所示：

```toml
[connections.my_database]
type = "sql"
dialect = "mysql"
username = "xxx"
password = "xxx"
host = "example.com" # IP或URL
port = 3306 # 端口号
database = "mydb" # 数据库名称
```

由于你不想将你的`secrets.toml`文件提交到你的代码库，你需要了解一下当你准备发布你的应用程序时，你的主机是如何处理秘密信息的。每个主机平台可能有不同的方式让你传递你的秘密信息。如果你使用Streamlit社区云，例如，每个部署的应用程序都有一个设置菜单，你可以在其中加载你的秘密信息。在你编写了一个应用程序并准备部署之后，你可以阅读有关如何在社区云上[发布你的应用程序](https://docs.streamlit.io/streamlit-community-cloud/deploy-your-app)的所有信息。

如果你想要更多帮助或者有更复杂的问题，请访问我们的[论坛](https://discuss.streamlit.io/)。那里有大量的信息和Streamlit专家能为你提供帮助。

希望这篇文章对你有所帮助，让我们在下一篇文章再见！
