# 自定义适配器和钩子

Requests 被设计为高度可扩展，允许你修改其核心行为以适应特定的使用场景。实现这一点的两个主要机制是传输适配器和事件钩子。传输适配器让你能够定义 Requests 如何进行 HTTP 和 HTTPS 调用，而钩子则提供了一种在请求-响应周期的特定节点触发自定义操作的方法。

## 传输适配器

每当 `Session` 处理一个请求时，它会根据 URL 的协议（例如 `http://` 或 `https://`）查找已注册的传输适配器。该适配器负责整个传输逻辑，包括连接管理、发送请求和返回响应。

默认情况下，Requests 对 `http://` 和 `https://` 都使用 `HTTPAdapter`。你可以创建自己的适配器来实现自定义的传输行为。

### 创建自定义适配器

自定义适配器应继承自 `requests.adapters.BaseAdapter`，或者更常见地，继承自 `requests.adapters.HTTPAdapter`（如果你只想修改现有的 HTTP/HTTPS 逻辑）。最重要的需要重写的方法是 `send()`。

下面是一个自定义适配器的简单示例，它为每个请求和响应添加了基本的日志记录功能。

```python Transport Adapter Example icon=logos:python
import requests
from requests.adapters import HTTPAdapter

class LoggingAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        print(f'-> Sending request to {request.method} {request.url}')
        
        # 调用父类的 send 方法来执行实际的请求
        response = super().send(request, stream, timeout, verify, cert, proxies)
        
        print(f'<- Received response {response.status_code} {response.reason}')
        return response
```

### 使用自定义适配器

要使用自定义适配器，你必须将其挂载（mount）到一个 `Session` 对象上，并指定一个 URL 前缀。之后，该会话将对所有 URL 以该前缀开头的请求使用你的适配器。

```python Mounting a Custom Adapter icon=logos:python
# 创建一个 session 和我们自定义适配器的实例
session = requests.Session()
adapter = LoggingAdapter()

# 挂载适配器以处理所有 HTTPS 流量
session.mount('https://', adapter)

# 所有对 https://... 的请求现在都将通过我们的 LoggingAdapter
try:
    session.get('https://httpbin.org/get')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')

# 输出:
# -> Sending request to GET https://httpbin.org/get
# <- Received response 200 OK
```

通过对 `HTTPAdapter` 进行子类化，你还可以重写其他方法，从而对连接池（`init_poolmanager`）、代理管理（`proxy_manager_for`）和响应构建（`build_response`）等方面进行细粒度控制。

## 事件钩子

Requests 提供了一个钩子系统，允许你将可调用操作附加到请求生命周期的不同阶段。当你注册一个钩子时，Requests 会用特定数据调用你的函数，从而允许你检查或修改这些数据。

最主要的可用钩子是 `response`，它在从服务器接收到响应之后、返回给调用者之前被触发。

### 钩子如何工作

钩子是一个函数，它接收其作用的对象作为第一个参数。对于 `response` 钩子，这个对象就是 `Response` 对象。你的函数可以根据响应执行操作，甚至可以修改响应本身。如果钩子函数返回值，该返回值将替换原始对象。

下面是一个钩子的示例，它会自动检查 HTTP 错误：

```python Hook Example icon=logos:python
import requests

def raise_for_status_hook(response, *args, **kwargs):
    """一个在每个响应上调用 raise_for_status() 的钩子函数。"""
    print(f'Hook is checking response for URL: {response.url}')
    response.raise_for_status()
    # 如果我们不修改响应，则不需要返回值

# 创建一个 session 并附加钩子
session = requests.Session()
session.hooks['response'] = [raise_for_status_hook]

# 这个请求将会成功，钩子也会运行
print('--- Making a successful request ---')
response = session.get('https://httpbin.org/get')
print(f'Request successful with status code: {response.status_code}')

print('\n--- Making a failing request ---')
try:
    session.get('https://httpbin.org/status/404')
except requests.exceptions.HTTPError as e:
    print(f'Caught expected error via hook: {e}')

# 输出:
# --- Making a successful request ---
# Hook is checking response for URL: https://httpbin.org/get
# Request successful with status code: 200
#
# --- Making a failing request ---
# Hook is checking response for URL: https://httpbin.org/status/404
# Caught expected error via hook: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

你也可以通过向请求方法传递一个 `hooks` 字典，在单个请求的粒度上附加钩子：

```python Per-Request Hook icon=logos:python
requests.get('https://httpbin.org/status/500', hooks={'response': raise_for_status_hook})
```

通过利用自定义适配器和钩子，你可以扩展 Requests 来处理复杂的身份验证方案、自定义的日志记录需求和独特的网络传输，使其成为处理任何 HTTP 相关任务的强大工具。

要详细了解可用于扩展的类和方法，请查阅完整的 [API 参考](./api-reference.md)。