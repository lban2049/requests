# 自定义适配器和钩子

Requests 的设计具有高度可扩展性，允许您在高级场景中改变其核心行为。实现这一点的两种主要机制是传输适配器（Transport Adapters），它控制请求如何通过网络发送；以及钩子（Hooks），它允许您拦截和修改请求-响应周期的部分内容。

本指南探讨了如何创建和使用您自己的自定义适配器和钩子，以根据您的特定需求定制 Requests。

## 传输适配器

传输适配器（Transport Adapter）是一个类，它接收一个 `PreparedRequest` 并处理将其发送到目标服务器的逻辑。它管理连接池、重试逻辑和特定协议的行为。Requests 自带一个默认的 `HTTPAdapter`，用于所有 `http://` 和 `https://` 请求。

通过创建自定义适配器，您可以实现独特的传输行为，例如添加自定义身份验证头、以特定格式记录请求，甚至完全使用不同的传输协议。

### 创建自定义适配器

创建自定义适配器最简单的方法是继承 `requests.adapters.HTTPAdapter` 并重写其方法之一。最重要的方法是 `send()`，它负责整个请求发送过程。

以下是一个自定义适配器的示例，它会为发送的每个请求添加一个 `X-Custom-Header`：

```python
import requests
from requests.adapters import HTTPAdapter

class CustomAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        # 为请求添加一个自定义头
        request.headers['X-Custom-Header'] = 'MyCustomValue'

        # 调用父类的 send 方法来执行请求
        print("Sending request with custom header...")
        return super().send(request, stream, timeout, verify, cert, proxies)

```

### 挂载自定义适配器

定义好适配器后，您需要指示一个 `Session` 对象为特定的 URL 前缀使用它。这可以通过 `session.mount()` 方法完成。

```python
# 创建一个会话对象
s = requests.Session()

# 创建我们自定义适配器的实例
adapter = CustomAdapter()

# 挂载适配器以处理所有 HTTP 和 HTTPS 请求
s.mount('http://', adapter)
s.mount('https://', adapter)

# 使用会话发出请求
try:
    response = s.get('https://httpbin.org/headers')
    response.raise_for_status()
    print("\nResponse Headers:")
    print(response.json()['headers'])
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")

```

当您运行此代码时，您将在 httpbin.org 的响应中看到 `X-Custom-Header`，这证实了您的自定义适配器已被使用。

### 适配器和钩子如何交互

下图说明了在使用带有自定义适配器和已注册响应钩子的 `Session` 发出请求时的流程。

```d2
direction: down

UserCode: {
  label: "用户代码"
  shape: rectangle
}

Session: {
  label: "requests.Session"
  shape: class
}

Adapter: {
  label: "CustomAdapter"
  shape: class
}

Network: {
  label: "网络 / 服务器"
  shape: cylinder
}

HookFunction: {
  label: "响应钩子函数"
  shape: rectangle
}

UserCode -> Session: "1. session.get(url)"
Session -> Adapter: "2. 选择并调用 adapter.send(request)"
Adapter -> Network: "3. 发送 HTTP 请求"
Network -> Adapter: "4. 接收 HTTP 响应"
Adapter -> Session: "5. 返回 requests.Response 对象"
Session -> HookFunction: "6. 调度 'response' 钩子"
HookFunction -> Session: "7. 钩子执行并返回"
Session -> UserCode: "8. 返回最终的 Response"

```

## 事件钩子

Requests 还提供了一个钩子系统，允许您注册在请求生命周期的特定点执行的回调函数。这对于实现事件处理、自定义日志记录或动态修改响应非常有用。

主要可用的钩子是 `response`，它在从服务器接收到响应之后、返回给调用代码之前触发。

### 使用 `response` 钩子

钩子是一个函数，它接收 `response` 对象作为其第一个参数。传递给原始请求方法的任何其他参数（例如 `timeout`）也会作为关键字参数传递给钩子。

钩子函数可以检查响应，如果它返回一个值，该值将替换原始响应。如果它返回 `None`，则使用原始响应。

#### 示例：记录响应

这是一个简单的钩子，用于记录每个响应的状态码和 URL。

```python
import requests

def log_response(response, *args, **kwargs):
    print(f"Request to {response.url} completed with status {response.status_code}")
    # 这个钩子不修改响应，因此它隐式返回 None。

# 将钩子附加到会话上
session = requests.Session()
session.hooks['response'] = log_response

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/status/404')

```

#### 示例：自动错误检查

此示例展示了一个通过调用 `response.raise_for_status()` 自动检查 HTTP 错误的钩子，从而简化了主应用程序逻辑中的错误处理。

```python
import requests

def check_for_error(response, *args, **kwargs):
    try:
        response.raise_for_status()
        print(f"Request to {response.url} was successful.")
    except requests.exceptions.HTTPError as e:
        print(f"HTTP Error for {response.url}: {e}")
    # 无需返回任何内容，我们只是在执行一个操作。

session = requests.Session()
session.hooks['response'] = check_for_error

print("Making a request that will succeed...")
session.get('https://httpbin.org/status/200')

print("\nMaking a request that will fail...")
session.get('https://httpbin.org/status/500')

```

通过结合使用自定义适配器和钩子，您可以构建出完全符合您应用程序需求的复杂且有弹性的 HTTP 客户端。

---

有关所讨论的类和方法的更多详细信息，您可以浏览 [API 参考](./api-reference.md)。