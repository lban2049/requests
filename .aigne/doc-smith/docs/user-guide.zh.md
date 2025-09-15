# 用户指南

欢迎阅读 Requests 用户指南！本指南旨在通过实用的、代码优先的示例，帮助您探索该库的核心功能。我们将涵盖从发出您的第一个请求到处理身份验证和会话管理等复杂场景的所有内容。

如果您尚未安装该库，请从我们的[入门指南](./getting-started.md)开始。有关所有类和方法的详尽列表，请参阅[API 参考](./api-reference.md)。

本指南分为以下几个部分：

<x-cards>
  <x-card data-title="发出请求" data-href="/user-guide/making-a-request" data-icon="lucide:send">
    学习如何使用 GET、POST、PUT 等各种 HTTP 方法，以及如何传递 URL 参数、请求头和请求体。
  </x-card>
  <x-card data-title="处理响应" data-href="/user-guide/handling-responses" data-icon="lucide:arrow-down-left">
    了解如何访问响应内容（文本、JSON、二进制）、检查状态码和读取响应头。
  </x-card>
  <x-card data-title="会话对象" data-href="/user-guide/session-objects" data-icon="lucide:database">
    利用会话对象在多个请求之间持久化参数、Cookie 和请求头，以提高性能。
  </x-card>
  <x-card data-title="身份验证" data-href="/user-guide/authentication" data-icon="lucide:key-round">
    实施各种身份验证方案，包括基本认证和摘要认证，以保护您的请求。
  </x-card>
  <x-card data-title="错误处理" data-href="/user-guide/error-handling" data-icon="lucide:shield-alert">
    学习预测和处理潜在的请求错误，例如连接问题、超时和错误的 HTTP 状态。
  </x-card>
</x-cards>

## 一个简单的示例

这里有一个快速示例，演示了发出请求和处理响应的基本工作流程，涉及本指南前几章的概念。

```python Basic Workflow Example icon=logos:python
import requests

# 向一个公共 API 发出 GET 请求
response = requests.get('https://httpbin.org/get')

# 您可以检查状态码以查看请求是否成功
if response.status_code == 200:
    print('Success!')
    # 响应内容可以解码为 JSON
    data = response.json()
    print('Response JSON:')
    print(data)
else:
    print(f'Request failed with status code: {response.status_code}')

# Response 对象有许多有用的属性
print(f"\nResponse Headers: {response.headers['Content-Type']}")
```

## 后续步骤

本指南提供了一条掌握 Requests 的结构化路径。每个部分都建立在前一部分的基础上，让您全面了解该库的功能。

准备好后，请深入阅读第一章 [发出请求](./user-guide-making-a-request.md)。如果您正在寻找更具体问题的解决方案，您可能需要探索我们的[高级用法](./advanced-usage.md)指南。