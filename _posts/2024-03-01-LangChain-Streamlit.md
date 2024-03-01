---
layout: post
title:  "LangChain 结合 Streamlit 打造交互式 AI 应用"
date:   2024-03-01 12:16:47 +0800
categories: ai langchain streamlit interactivesoftware
---

![LangChain 结合 Streamlit 打造交互式 AI 应用](https://raw.githubusercontent.com/jamiesun/images/master/default/W88cBu.png)

LangChain 遇上 Streamlit，即便是初学者也能轻松构建引人入胜的智能 web 应用。本指南将带你了解基础知识，突出关键示例，并为你提供开始所需的工具和代码。

## 什么是 LangChain 和 Streamlit？

首先，让我们揭开这两个强大工具的神秘面纱：

- **LangChain** 是一个全面的库，旨在便于创建利用大型语言模型的应用。它是为你的项目添加复杂 AI 功能的首选。在他们的 [GitHub 仓库](https://github.com/langchain/langchain) 中探索更多。

- **Streamlit** 简化了 web 应用开发，将数据脚本转换为可共享的 web 应用，无需花费太多时间。它非常适合希望展示项目的开发者，而不需要深入了解 web 开发的复杂性。在 [Streamlit](https://streamlit.io/) 了解更多。

LangChain 与 Streamlit 的结合使得以最少的麻烦创建动态、AI 驱动的应用成为可能，将 AI 的计算能力与现代 web 应用的交互性结合起来。

## 深入了解 LangChain 🦜️🔗 Streamlit 代理

这次合作产生了几个参考实现，展示了结合 LangChain 与 Streamlit 的潜力。这里有一些亮点，包括查看链接和你开始需要的代码：

1. **简单的流式聊天应用**：
   - 流式聊天应用：利用 `langchain.chat_models.ChatOpenAI` 实现简单的流式体验。[查看应用](https://langchain-streaming-example.streamlit.app/)。

   ![简单的流式聊天应用](https://raw.githubusercontent.com/jamiesun/images/master/default/rOeQGQ.png)

   - 记忆应用：利用 `StreamlitChatMessageHistory` 进行对话记忆管理。[查看应用](https://langchain-st-memory.streamlit.app/)。

2. **MRKL 演示**：展示了 MRKL 演示的复制品，这是 LangChain 在 Streamlit 框架内高级功能的证明。[查看应用](https://langchain-mrkl.streamlit.app)。

![MRKL 演示](https://raw.githubusercontent.com/jamiesun/images/master/default/9XqfMT.png)

3. **启用搜索的聊天机器人**：特色机器人记得聊天历史并能进行搜索，增强了对话 AI 体验。[查看应用](https://langchain-chat-search.streamlit.app/)。

4. **反馈和文档交互**：展示了收集用户反馈和查询自定义文档或数据库以获取信息的应用。[查看反馈应用](https://langsmith-simple-feedback.streamlit.app/)，[查看文档交互应用](https://langchain-document-chat.streamlit.app/)。

这些示例展示了 LangChain 和 Streamlit 的多功能性和能力，从基本交互到复杂的数据处理和 AI 功能。

## 克隆项目

**streamlit_agent** 项目地址: [github repo](https://github.com/langchain-ai/streamlit-agent)

克隆项目到本地

```bash
git clone  https://github.com/langchain-ai/streamlit-agent
```

## 设置你的环境

为了开始你的开发之旅，首先需要设置你的环境。以下是如何操作：

```shell
# 使用 Poetry 安装依赖项
$ poetry install

# 激活你的新环境
$ poetry shell

# 设置预提交钩子
$ pre-commit install
```

这些命令准备了你的环境，安装了所有必要的依赖项，并确保你的代码是干净的，准备好进行开发。

## 运行你的第一个应用

准备好环境后，运行应用就像：

```shell
$ streamlit run streamlit_agent/mrkl_demo.py
```

将 `streamlit_agent/mrkl_demo.py` 替换为你想运行的应用的路径，就可以开始了！

### 采用 Docker 进行部署

对于对容器化感兴趣的人来说，该项目包括了一个 Docker 设置，优化了大小和构建时间：

```shell
# 构建你的 Docker 镜像
DOCKER_BUILDKIT=1 docker build --target=runtime . -t langchain-streamlit-agent:latest

# 运行你的 Docker 容器
docker run -d --name langchain-streamlit-agent -p 8051:8051 langchain-streamlit-agent:latest
```

LangChain 和 Streamlit 为开发者无缝整合 AI 到 web 应用提供了强大的双重工具。有了提供的资源、示例和设置指南，你已经准备好开始探索 AI 驱动的 web 开发之旅。抓住机会创建、学习和贡献给这个令人兴奋的领域吧。

祝编码愉快！
