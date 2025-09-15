# 自定义适配器和钩子

虽然 Requests 为大多数 HTTP 通信需求提供了强大而直接的接口，但它也为高级场景提供了高度可扩展的架构。通过实现自定义传输适配器和利用事件钩子系统，你可以对请求生命周期进行精细控制。

本指南将引导你了解如何扩展 Requests 的核心功能，以处理自定义传输逻辑、动态修改请求/响应对象，以及将自定义处理集成到你的 HTTP 工作流中。有关完整的技术参考，请参阅 [API 参考](./api-reference.md)。

## 传输适配器

传输适配器是 Requests 处理发送请求的核心机制。当你发出请求时，`Session` 对象会根据给定的 URL 方案（例如 `http://` 或 `https://`）确定合适的适配器，并用它来管理连接和分派请求。

所有适配器的基类是 `BaseAdapter`。任何自定义适配器都必须继承自它，并实现 `send()` 和 `close()` 方法。然而，对于大多数用例，你会希望子类化内置的 `HTTPAdapter`。

### HTTPAdapter

`HTTPAdapter` 基于 `urllib3` 库构建，为 HTTP 和 HTTPS 连接提供了一个通用接口。它管理连接池、重试和代理配置。

你可以创建一个 `HTTPAdapter` 实例并将其挂载到 `Session` 对象上，以针对特定协议或域配置其行为。一个常见的用例是设置自定义重试策略。

<x-field data-name="pool_connections" data-type="number" data-default="10" data-desc="要缓存的 urllib3 连接池数量。"></x-field>
<x-field data-name="pool_maxsize" data-type="number" data-default="10" data-desc="连接池中要保存的最大连接数。"></x-field>
<x-field data-name="max_retries" data-type="number or urllib3.util.retry.Retry" data-default="0" data-desc="每个连接应尝试的最大重试次数。你可以传递一个整数或一个配置好的 `Retry` 对象以进行更精细的控制。"></x-field>
<x-field data-name="pool_block" data-type="boolean" data-default="false" data-desc="当没有空闲连接时，连接池是否应阻塞等待连接。"></x-field>

**示例：配置连接重试**

默认情况下，Requests 不会重试失败的连接。你可以通过挂载一个预先配置的 `HTTPAdapter` 来轻松改变此行为。

```python Configuring and Mounting an HTTPAdapter icon=logos:python
import requests

s = requests.Session()

# 为所有发往 http:// 和 https:// 的请求配置一个带 3 次重试的适配器
a = requests.adapters.HTTPAdapter(max_retries=3)
s.mount('http://', a)
s.mount('https://', a)

# 使用带自定义适配器的会话发出请求
try:
    response = s.get('http://a.bad.domain/will/fail')
except requests.exceptions.ConnectionError as e:
    print(f"Request failed after retries: {e}")

```
此示例创建一个 `Session` 并挂载一个配置为最多重试三次失败连接的 `HTTPAdapter`。这仅适用于 DNS 失败、套接字连接错误和连接超时等特定错误，不适用于已将数据发送到服务器的请求。

### 创建自定义适配器

对于真正的自定义行为，例如实现非 HTTP 传输协议或修改低级连接逻辑，你可以子类化 `HTTPAdapter` 并重写其方法。你可能需要重写的一些关键方法包括：

| 方法 | 描述 |
|---|---|
| `send()` | 发送 `PreparedRequest` 并返回 `Response` 对象的主要方法。 |
| `init_poolmanager()` | 初始化 `urllib3.PoolManager`。 |
| `proxy_manager_for()` | 为给定的代理返回一个 `urllib3.ProxyManager`。 |
| `cert_verify()` | 处理 SSL 证书验证逻辑。 |
| `build_response()` | 从 `urllib3` 响应构建一个 `requests.Response`。 |


## 事件钩子

Requests 提供了一个钩子系统，允许你将可调用函数附加到请求过程的特定部分。这些钩子对于记录日志、修改请求或响应或触发事件非常有用。

可用的主要钩子是 `response`，它在从服务器接收到响应之后、返回给调用代码之前被调用。

钩子函数接收与事件相关的数据（例如 `Response` 对象）作为其第一个参数。它可以使用这些数据执行操作，甚至可以返回它的修改版本，然后 Requests 将使用该版本。

### 使用 `response` 钩子

你可以在 `Session` 对象上注册钩子，以将其应用于该会话发出的所有请求，也可以按每个请求进行注册。

**示例：记录响应头**

这是一个简单钩子的示例，它会打印每个响应的状态码和头信息。

```python Attaching a Response Hook icon=logos:python
import requests

def log_response_details(response, *args, **kwargs):
    """一个用于记录响应状态和头信息的钩子函数。"""
    print(f"Status Code: {response.status_code}")
    print("--- Headers ---")
    for key, value in response.headers.items():
        print(f"{key}: {value}")
    print("---------------")

# 创建一个会话并附加钩子
session = requests.Session()
session.hooks['response'] = [log_response_details]

# 使用此会话发出的所有请求都将触发该钩子
print("Making request to httpbin.org...")
session.get('https://httpbin.org/get')

print("\nMaking another request...")
session.get('https://httpbin.org/headers')
```

当你运行此代码时，`log_response_details` 函数将为每个请求执行，并将其详细信息打印到控制台。

钩子通过 `dispatch_hook` 函数进行分派。如果钩子函数返回值，该值将替换传递给它的数据。这允许你在 `Response` 对象从 `request()` 调用返回之前对其进行修改。

通过结合使用自定义适配器和钩子，你可以扩展 Requests 以适应几乎任何网络需求，从简单的重试逻辑到复杂的自定义传输协议。