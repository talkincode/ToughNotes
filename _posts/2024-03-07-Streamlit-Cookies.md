---
layout: post
title:  "在 Streamlit 中使用 Cookie 管理会话状态"
date:   2024-03-07 15:59:34 +0800
categories: streamlit cookies session-state
keywords: streamlit, cookies, session-state
---

在构建基于Web的应用程序时，会话状态管理是一项至关重要的功能，它允许我们跟踪用户的交互状态，从而提供连贯且个性化的用户体验。Streamlit作为一种用于创建数据应用程序的Python库，其核心设计没有直接提供会话状态或cookie管理功能，这是由于Streamlit的主要设计目标是简化数据应用程序的创建和部署过程。然而，在实际的应用开发中，管理用户会话往往是不可或缺的，因此了解在Streamlit环境下如何巧妙利用cookie进行状态管理，显得尤为重要。

Cookie是Web开发中用于存储用户信息的一小段数据，它由服务器发送至用户的Web浏览器，并保存在用户的本地终端中。在用户后续对服务器的访问中，浏览器会将之前存储的cookie回传给服务器，以此方式实现服务器对用户状态的追踪。

在 Streamlit 中，虽然没有内建的cookie管理机制，我们可以通过一些技术手段实现这一功能。具体来讲，可以借助某些Python第三方库或JavaScript脚本来进行cookie的读取和设置。

一个典型的实现方式是使用Python的`http.cookies`库和Streamlit的`st.markdown()`函数，后者可以用来加载自定义的HTML代码。在这个场景中，我们可以通过`st.markdown()`载入包含JavaScript脚本的HTML元素，利用JavaScript的`document.cookie`属性来管理cookie。

例如，以下是一个基本的示例，展示了如何在Streamlit应用中设置和获取cookie：

```python
import streamlit as st
from http.cookies import SimpleCookie

# 设置Cookie
def set_cookie(key, value, expires=None):
    cookie = SimpleCookie()
    cookie[key] = value
    if expires is not None:
        cookie[key]["expires"] = expires

    script = f"document.cookie = '{cookie.output(header='', sep='; ')}';"
    st.markdown(f'<script>{script}</script>', unsafe_allow_html=True)

# 获取Cookie
def get_cookie(key):
    cookies = SimpleCookie(st.session_state.get("cookies", ""))
    return cookies.get(key).value if key in cookies else None

# 在Streamlit中调用函数设置和获取cookie
if 'cookies' not in st.session_state:
    st.session_state['cookies'] = ""

cookie_value = get_cookie('my_cookie')
if cookie_value is not None:
    st.write(f'The value of the cookie is: {cookie_value}')
else:
    set_cookie('my_cookie', 'my_value', expires='Thu, 01 Jan 1970 00:00:01 GMT')
```

在这个例子中，我们通过定义`set_cookie`和`get_cookie`函数来分别设置和获取cookie。`set_cookie`函数内部构建了一个`SimpleCookie`对象，然后利用JavaScript向客户端写入cookie。在获取cookie时，我们从`st.session_state`（Streamlit用来在会话中保存状态的机制）中提取并解析cookie信息。

## 使用第三方 Streamlit 扩展来实现 cookie 管理

除了上文提及的使用JavaScript脚本和`http.cookies`库管理Cookie之外，Streamlit的生态系统中亦出现了第三方扩展来简化这一过程。以下我们将讨论`streamlit_cookie`扩展的使用方法来为Streamlit应用程序实现Cookie管理。

`streamlit_cookie`是一个为Streamlit定制的Python库，它提供了一个`EncryptedCookieManager`类，以简化Cookie的读取、设置和加密存储流程。其主要特点是能够为用户提供加密的cookie管理，从而提高应用安全性。

### 初始化Cookie管理器

在开始使用`streamlit_cookie`扩展之前，首先需要实例化一个`EncryptedCookieManager`对象。你可以为其传递一个cookie的前缀，并设置一个环境变量`COOKIES_PASSWORD`作为加密密钥。

```python
cookies = EncryptedCookieManager(
    prefix="streamlit-cookie/",
    password=os.environ.get("COOKIES_PASSWORD", "My secret password"),
)
```

应当注意，在应用部署至生产环境时，需要保证一个安全的加密密钥，并妥善管理环境变量，以防泄露。

### 读取和设置Cookie

随后，你可以很容易地通过下标操作来设置和获取cookie的值。如果需要立即保存更改，可以调用`cookies.save()`方法强制更新而不需等待页面重新运行。

```python
# 设置一个新的cookie值
cookies['a-cookie'] = 'new_value'
# 强制立即保存cookie
cookies.save()
# 获取cookie值
cookie_value = cookies['a-cookie']
```

### 提供用户交互

为了在用户界面上提供与cookie相关的操作，可以结合Streamlit的原生组件以及HTML和JavaScript代码。

```python
value = st.text_input("New value for a cookie")

if st.button("Change the cookie"):
    cookies['a-cookie'] = value
    cookies.save()  # 可选择立即保存

st.write(cookies['a-cookie'])
```

通过上述代码，用户可以在应用程序的文本框输入新值，并通过按钮点击将其存储为cookie。如果要删除一个cookie，可以简单地使用`del`关键字。

```python
if st.button("Delete the cookie"):
    del cookies['a-cookie']
```

### 安全性和最佳实践

使用`streamlit_cookie`扩展时，应当注意以下安全最佳实践：

- 使用强加密密钥，并在生产环境中通过环境变量管理该密钥。
- 限制cookie的使用范围，例如设置合适的路径和过期时间，以减少安全风险。
- 对敏感数据进行额外加密，即使在使用`EncryptedCookieManager`时也应考虑这一点，因为客户端潜在的威胁仍然存在。

结论上，`streamlit_cookie`为Streamlit应用程序提供了一个简便有效的cookie管理方案，它通过封装复杂的机制为开发者提供了易于使用的API，大大简化了会话状态管理流程。
