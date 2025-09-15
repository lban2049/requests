# 自定义适配器和钩子

Requests 设计为高度可定制。对于超出标准 HTTP 交互的进阶场景，你可以使用传输适配器和事件钩子系统来扩展其功能。这使你能够集成自定义传输逻辑、修改请求处理，并以复杂的方式处理响应。

## 传输适配器

Requests 的核心是使用传输适配器来处理通过不同协议实际发送的请求。该库包含一个默认的 `HTTPAdapter`，用于所有 `http://` 和 `https://` 请求。你可以修改其行为，甚至为自定义传输机制创建自己的适配器。

### 自定义 HTTP 适配器

内置的 `HTTPAdapter` 可以配置以更改其默认行为，例如连接池和重试逻辑。要使用自定义适配器，你必须将其挂载到特定 URL 前缀的 `Session` 对象上。

`HTTPAdapter` 可以使用几个参数进行初始化以控制其行为：

<x-field data-name="pool_connections" data-type="number" data-default="10" data-desc="要缓存的 urllib3 连接池数量。"></x-field>
<x-field data-name="pool_maxsize" data-type="number" data-default="10" data-desc="连接池中要保存的最大连接数。"></x-field>
<x-field data-name="max_retries" data-type="number or urllib3.util.retry.Retry" data-default="0" data-desc="每个连接在失败时应尝试的最大重试次数。默认情况下，重试是禁用的。"></x-field>
<x-field data-name="pool_block" data-type="boolean" data-default="False" data-desc="当连接池已满时，是否应阻塞连接。"></x-field>

例如，你可以配置一个会话，使其在请求失败时自动重试最多 3 次：

```python 使用 HTTPAdapter 配置重试 icon=logos:python
import requests
from requests.adapters import HTTPAdapter

s = requests.Session()

# Configure an adapter with 3 retries
a = HTTPAdapter(max_retries=3)

# Mount the adapter to handle all HTTP requests
s.mount('http://', a)

# Any request made with this session will now retry on failure
try:
    response = s.get('http://a.bad.url/endpoint')
    print(response.status_code)
except requests.exceptions.ConnectionError as e:
    print(f"Request failed after retries: {e}")

```

### 创建自定义适配器

对于真正自定义的行为，你可以通过子类化 `requests.adapters.BaseAdapter` 来创建自己的传输适配器。自定义适配器必须实现 `send()` 方法。

然而，更常见的方法是子类化 `HTTPAdapter` 并覆盖特定方法以添加功能，而无需重新实现整个 HTTP/HTTPS 逻辑。

以下是一个自定义适配器的示例，它会向每个传出的请求添加一个特定的标头：

```python 自定义标头适配器 icon=logos:python
from requests.adapters import HTTPAdapter

class CustomHeaderAdapter(HTTPAdapter):
    def __init__(self, custom_header, *args, **kwargs):
        self.custom_header = custom_header
        super().__init__(*args, **kwargs)

    def add_headers(self, request, **kwargs):
        # This method is called before the request is sent
        request.headers['X-Custom-Header'] = self.custom_header

# Usage
import requests

session = requests.Session()
adapter = CustomHeaderAdapter('MyCustomValue')
session.mount('https://', adapter)

response = session.get('https://httpbin.org/headers')
print(response.json())

```

运行此代码时，httpbin.org 的响应将显示 `X-Custom-Header` 已成功添加到请求中。

## 事件钩子

Requests 还提供了一个钩子系统，允许你将回调函数附加到请求过程的某些部分。这对于事件处理、日志记录或在响应返回到应用程序代码之前修改响应非常有用。

目前唯一可用的钩子是 `response`，它在从服务器接收到响应之后、从初始请求方法返回之前触发。

### 使用 `response` 钩子

钩子是一个函数，它接受响应对象作为其第一个参数，以及传递给请求方法（`get`、`post` 等）的任何其他关键字参数。

你可以将钩子附加到 `Session` 对象或单个请求上。

```python 响应钩子示例 icon=logos:python
import requests

def log_response_status(response, *args, **kwargs):
    print(f"Request to {response.url} completed with status: {response.status_code}")

# Attach hook to a session
session = requests.Session()
session.hooks['response'] = [log_response_status]

print("Making request with a session hook...")
session.get('https://httpbin.org/get')

print("\nMaking request with a per-request hook...")
requests.get('https://httpbin.org/get', hooks={'response': log_response_status})
```

### 修改响应

钩子还可以返回一个修改后的响应对象。如果钩子函数返回的值不是 `None`，该值将替换原始响应。这允许在运行时进行强大的响应操作。

```python 使用钩子修改响应 icon=logos:python
import requests

def add_custom_attribute(response, *args, **kwargs):
    """一个向响应对象添加自定义属性的钩子。"""
    response.custom_message = "Response processed by hook!"
    # 由于我们是就地修改响应，因此无需返回它。
    # 然而，如果需要，你也可以返回一个完全不同的对象。
    return None

session = requests.Session()
session.hooks['response'] = [add_custom_attribute]

response = session.get('https://httpbin.org/get')

# Access the custom attribute added by the hook
if hasattr(response, 'custom_message'):
    print(response.custom_message)

```

通过利用自定义适配器和钩子，你可以定制 Requests 以适应几乎任何工作流程，从简单的重试逻辑到复杂的、特定于协议的集成。

---

要获取所有类和方法的完整参考，请前往 [API 参考](./api-reference.md)。