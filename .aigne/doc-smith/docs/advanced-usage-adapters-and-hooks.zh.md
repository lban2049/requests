# 自定义适配器和钩子

Requests 的设计是可扩展的，允许您在高级场景下改变其核心行为。实现这一点的两种主要机制是传输适配器，它控制请求如何通过网络发送；以及钩子，它让您能够拦截和修改请求-响应周期的各个部分。

本指南将探讨如何创建和使用自定义适配器与钩子，以根据您的特定需求定制 Requests。

## 传输适配器

传输适配器是一个类，它接收一个 `PreparedRequest` 对象并处理将其发送到服务器的逻辑。它负责管理连接池、重试逻辑和特定于协议的行为。默认情况下，Requests 对所有 `http://` 和 `https://` 请求都使用同一个 `HTTPAdapter`。

通过创建自定义适配器，您可以实现独特的传输行为，例如添加自定义身份验证标头、以特定格式记录请求，甚至使用不同的传输协议。

### 创建自定义适配器

创建自定义适配器最直接的方法是继承 `requests.adapters.HTTPAdapter` 并重写其方法。`send()` 方法是适配器的核心，因为它负责整个请求发送过程。

下面是一个自定义适配器的示例，它会为发送的每个请求添加一个 `X-Request-ID` 标头。

```python Add Custom Header Adapter icon=logos:python
import requests
import uuid
from requests.adapters import HTTPAdapter

class RequestIdAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        # Generate a unique ID for the request
        request_id = str(uuid.uuid4())
        request.headers['X-Request-ID'] = request_id

        print(f"Sending request with ID: {request_id}")
        
        # Call the parent class's send method to execute the request
        return super().send(request, stream, timeout, verify, cert, proxies)

```

### 挂载自定义适配器

定义好适配器后，您必须指示 `Session` 对象为特定的 URL 前缀使用该适配器。这可以通过 `session.mount()` 方法来完成。

```python Mount and Use Custom Adapter icon=logos:python
# Create a session object
s = requests.Session()

# Create an instance of our custom adapter
adapter = RequestIdAdapter()

# Mount the adapter to handle all HTTP and HTTPS requests
s.mount('http://', adapter)
s.mount('https://', adapter)

# Make a request using the session
try:
    response = s.get('https://httpbin.org/headers')
    response.raise_for_status()
    print("\nResponse Headers:")
    # The response will contain the X-Request-ID header
    print(response.json()['headers'])
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")

```

当执行此代码时，httpbin.org 的输出将包含 `X-Request-ID` 标头，这证明您的自定义适配器已用于该请求。

## 事件钩子

Requests 还提供了一个钩子系统，允许您注册在请求生命周期的特定时间点执行的回调函数。这对于实现事件处理、自定义日志记录或动态修改响应非常有用。

主要可用的钩子是 `response`，它在从服务器收到响应后、返回给您的代码前触发。

### 使用 `response` 钩子

钩子是一个函数，它接收 `response` 对象作为其第一个参数。传递给原始请求方法（例如 `timeout`）的任何附加参数也会作为关键字参数传递给钩子。

钩子函数可以检查或修改响应。如果它返回值，该值将替换原始响应。如果它返回 `None`，则使用原始响应。

#### 示例：记录响应

下面是一个简单的钩子，用于记录每个响应的状态码和 URL。

```python Response Logging Hook icon=logos:python
import requests

def log_response(response, *args, **kwargs):
    print(f"Request to {response.url} completed with status {response.status_code}")
    # This hook does not modify the response, so it returns None implicitly.

# Attach the hook to a session
session = requests.Session()
session.hooks['response'] = log_response

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/status/404')

```

#### 示例：集中式错误检查

此示例展示了一个自动调用 `response.raise_for_status()` 的钩子，从而为所有通过该会话发出的请求实现集中的 HTTP 错误处理。

```python Automatic Error Checking Hook icon=logos:python
import requests

def check_for_error(response, *args, **kwargs):
    try:
        response.raise_for_status()
        print(f"Request to {response.url} was successful.")
    except requests.exceptions.HTTPError as e:
        # You could add more robust error handling here, like logging to a file
        print(f"HTTP Error for {response.url}: {e}")
    # No need to return anything, we are just performing an action.

session = requests.Session()
session.hooks['response'] = check_for_error

print("Making a request that will succeed...")
session.get('https://httpbin.org/status/200')

print("\nMaking a request that will fail...")
session.get('https://httpbin.org/status/500')

```

### 适配器和钩子的交互流程

下图说明了在使用同时注册了自定义适配器和响应钩子的 `Session` 发出请求时的数据流。

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
  label: "自定义适配器"
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
HookFunction -> Session: "7. 钩子执行，可能返回修改后的响应"
Session -> UserCode: "8. 返回最终响应"

```

通过结合使用自定义适配器和钩子，您可以构建出能够完美满足应用程序需求的、复杂且具有弹性的 HTTP 客户端。

---

如需了解所讨论的类和方法的更多详细信息，请浏览 [API 参考](./api-reference.md)。