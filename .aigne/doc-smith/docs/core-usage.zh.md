# 核心用法

**Requests** 是一个简单而优雅的 HTTP 库，旨在让发送 HTTP 请求变得极其容易。本节介绍了使用其简单函数式 API 发出请求和处理响应的基本模式。这是学习如何在 Python 中与 Web 服务进行交互的理想起点。

若想了解高级概述，可以从 [概述](./overview.md) 开始。

### 基本的请求-响应模式

Requests 库的核心在于向 URL 发出请求并接收一个 `Response` 对象。该对象包含了服务器返回的所有信息。

一个简单的 `GET` 请求（用于从 URL 检索数据）如下所示：

```python Making a simple GET request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')

# 检查状态码，看请求是否成功
if r.status_code == 200:
    print('Success!')
else:
    print('An error occurred.')

# 你可以访问响应头
print(r.headers['content-type'])

# 以及作为文本的响应体
# print(r.text)

# 或者，如果响应是 JSON 格式，你可以自动解码
print(r.json()[0]) # 打印来自 GitHub API 的第一个事件
```

这种简单的模式——为某个 HTTP 方法调用一个函数并返回一个响应对象——是你使用 Requests 所做一切的基础。

### 探索核心功能

为了帮助你掌握基本要素，我们将核心用法分解为几个详细的指南。每个指南都侧重于发出请求和处理响应的特定方面。

<x-cards data-columns="2">
  <x-card data-title="发起请求" data-icon="lucide:send" data-href="/core-usage/making-a-request">
    学习如何执行 GET、POST、PUT、DELETE 等多种 HTTP 方法。本指南涵盖了基本的请求-响应周期。
  </x-card>
  <x-card data-title="传递 URL 参数" data-icon="lucide:link-2" data-href="/core-usage/passing-url-parameters">
    了解如何在 URL 的查询字符串中发送数据，这是使用 GET 请求来筛选或指定数据的 API 的常见要求。
  </x-card>
  <x-card data-title="处理响应内容" data-icon="lucide:file-text" data-href="/core-usage/handling-response-content">
    深入了解访问响应体的不同方式，无论是原始字节、解码后的文本还是结构化的 JSON 数据。
  </x-card>
  <x-card data-title="POST 数据" data-icon="lucide:upload-cloud" data-href="/core-usage/posting-data">
    探索在请求体中发送数据的各种方法，包括表单编码数据、多部分文件上传和 JSON 载荷。
  </x-card>
</x-cards>

### Response 对象

如你所见，每次请求调用都会返回一个 `Response` 对象。这个对象包含了许多有用的属性和方法，可以让你检查服务器的回复。关键属性包括：

*   `status_code`：HTTP 状态码（例如，`200` 代表成功，`404` 代表未找到）。
*   `headers`：响应头的类字典对象。
*   `content`：以字节为单位的响应体。
*   `text`：解码为字符串的响应体。
*   `json()`：一个将 JSON 响应体反序列化为 Python 对象的方法。

理解如何使用这个对象对于构建有效的应用程序至关重要，上述指南将详细引导你完成这一过程。

### 后续步骤

熟悉了这些核心概念后，你就可以处理更复杂的交互了。[高级用法](./advanced-usage.md) 部分涵盖了会话管理、身份验证和错误处理等主题，帮助你构建健壮可靠的 HTTP 应用程序。