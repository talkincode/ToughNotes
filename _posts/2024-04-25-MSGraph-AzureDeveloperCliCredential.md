---
layout: post
title: "深入解密MSGraph认证机制： AzureDeveloperCliCredential 详解"
date: 2024-04-25 11:25:23 +0800
categories: azure MSGraph authentication
---

本文旨在深入解析 `AzureDeveloperCliCredential` 认证机制，这是一种专为开发者在本地开发环境中设计的认证方式，让我们一探究竟。

## 什么是 AzureDeveloperCliCredential？

`AzureDeveloperCliCredential` 是 Azure 标识库（Azure Identity library）中的一个类，它提供了一种便捷的认证方法，允许开发者在本地开发环境中使用 Azure Developer CLI 工具进行认证。这种认证机制主要面向的是使用 Microsoft Entra 进行身份管理和访问控制的应用程序开发场景。

借助于 `AzureDeveloperCliCredential`，开发者无需在代码中硬编码任何敏感信息，如客户端 ID、秘钥或访问令牌等，大大降低了管理凭证时的安全风险。

## 如何工作？

`AzureDeveloperCliCredential` 的工作流程相对直接，主要包括以下几个步骤：

1. 开发者首先需要在本地环境中安装和配置 Azure Developer CLI（`azd`）工具，并通过它进行登录。
2. 在成功登录后，`azd`能够获取并管理访问令牌。
3. 当 Azure SDK 需要访问 Azure 服务时，`AzureDeveloperCliCredential` 会与 `azd` 交互，请求一个访问令牌。
4. `azd` 返回一个有效的访问令牌给 `AzureDeveloperCliCredential`，该令牌随后被用于对 Azure 服务的 API 调用。

这一过程免去了开发者在应用程序代码中直接处理和存储凭证的需要，为开发者提供了更加安全便捷的身份认证方式。

## 关键特点

- **安全性**：避免在应用程序代码中硬编码敏感信息，减少潜在的安全风险。
- **便捷性**：开发者仅需通过 `azd` 登陆一次，便可在本地开发环境中自如访问 Azure 资源。
- **灵活性**：支持多租户认证，开发者可以指定一个或多个租户 ID，增加了使用的灵活性。

## 示例：创建和使用 AzureDeveloperCliCredential

以下是如何创建一个`AzureDeveloperCliCredential`对象并使用它获取 Azure 服务访问令牌的示例代码片段：

```python
from azure.identity import AzureDeveloperCliCredential

# 创建AzureDeveloperCliCredential实例
credential = AzureDeveloperCliCredential()

# 使用该凭证请求访问令牌
token = credential.get_token("https://management.azure.com/.default")
print("获取的访问令牌：", token.token)
```

在这个简单的例子中，我们首先导入并创建了一个`AzureDeveloperCliCredential`的实例。随后，我们调用了`get_token`方法来获取针对 Azure 管理 API 的访问令牌。这简化了访问 Azure 资源的认证过程，使开发者能够专注于业务逻辑的实现。

> 注意你可能会得到如下错误提示 `AzureDeveloperCliCredential.get_token failed: Azure Developer CLI could not be found. Please visit https://aka.ms/azure-dev for installation instructions and then,once installed, authenticate to your Azure account using 'azd auth login'.`, 这是提醒你需要安装 azd 命令行工具并通过 `azd auth login` 登录

## azd 工具安装

- windows: `winget install microsoft.azd`
- macos: `brew tap azure/azd && brew install azd`
- linux: `curl -fsSL https://aka.ms/install-azd.sh | bash`

## 总结

`AzureDeveloperCliCredential` 为 Azure 资源的开发和管理提供了一种简便且安全的认证方式，特别适合在本地开发环境中使用。通过将身份认证过程外包给 Azure Developer CLI，它有效减少了代码中管理敏感信息的需要，提高了开发效率同时保障了安全性。无论是新手开发者还是经验丰富的 Azure 专家，`AzureDeveloperCliCredential` 都是一个值得了解和利用的强大工具。
