# 用户指南

欢迎阅读 Requests 用户指南。本指南将通过实用的代码优先示例，带你逐步了解该库的核心功能。我们将涵盖从发起首次请求到处理响应、管理会话、实现身份验证以及处理错误的全部内容。

本指南专为已安装该库并希望学习其核心功能的开发者设计。如果你尚未安装 Requests，请先参阅[入门指南](./getting-started.md)。

<x-cards data-columns="2">
  <x-card data-title="发起请求" data-href="/user-guide/making-a-request" data-icon="lucide:send">
    学习发送 GET、POST 和 PUT 等各种 HTTP 请求。我们将介绍如何传递 URL 参数、自定义标头以及不同类型的请求正文。
  </x-card>
  <x-card data-title="处理响应" data-href="/user-guide/handling-responses" data-icon="lucide:arrow-down-circle">
    探索如何处理服务器的响应。学习以不同格式（文本、JSON、二进制）访问响应内容、检查状态码以及读取标头。
  </x-card>
  <x-card data-title="会话对象" data-href="/user-guide/session-objects" data-icon="lucide:database">
    使用会话对象在多个请求之间持久化参数、Cookie 和标头，并利用连接池来提升性能。
  </x-card>
  <x-card data-title="身份验证" data-href="/user-guide/authentication" data-icon="lucide:key-round">
    通过实现包括基本和摘要式身份验证在内的常用身份验证方案来保护你的请求。
  </x-card>
</x-cards>

<x-cards data-columns="1">
 <x-card data-title="错误处理" data-href="/user-guide/error-handling" data-icon="lucide:shield-alert">
    学习处理潜在的请求错误（如连接问题、超时和不成功的 HTTP 状态），编写健壮的代码。
  </x-card>
</x-cards>

## 完整示例

以下是一个简单示例，演示了一个常见的工作流程：发起 GET 请求、检查请求是否成功以及解析 JSON 响应。该模式涵盖了许多日常用例。

```python
import requests

try:
    # 使用 URL 参数向 httpbin.org API 发起 GET 请求
    response = requests.get('https://httpbin.org/get', params={'key': 'value'})

    # 检查请求是否成功（状态码 200-399）
    # 如果不成功，将引发 HTTPError
    response.raise_for_status()

    # 以 JSON 格式打印响应内容
    json_response = response.json()
    print("请求成功！")
    print("请求 URL:", json_response['url'])
    print("源 IP:", json_response['origin'])
    print("User-Agent 标头:", json_response['headers']['User-Agent'])

except requests.exceptions.HTTPError as http_err:
    print(f"发生 HTTP 错误: {http_err}")  # 例如 404 Not Found
except requests.exceptions.ConnectionError as conn_err:
    print(f"发生连接错误: {conn_err}") # 例如 DNS 解析失败、连接被拒绝
except requests.exceptions.Timeout as timeout_err:
    print(f"发生超时错误: {timeout_err}")
except requests.exceptions.RequestException as err:
    print(f"发生意外错误: {err}")

```
该示例使用 `requests.get` 发送请求，`response.raise_for_status()` 检查 HTTP 错误，并使用 `response.json()` 解码 JSON 正文。它还包含了针对常见网络问题的基本错误处理。

## 后续步骤

本指南为高效使用 Requests 提供了基础。要开始学习，请深入阅读[发起请求](./user-guide-making-a-request.md)部分，了解通过网络发送数据的基本知识。对于更复杂的场景，请参阅[高级用法](./advanced-usage.md)指南。