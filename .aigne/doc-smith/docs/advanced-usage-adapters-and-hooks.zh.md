# 自定义适配器和钩子

Requests 被设计为可扩展的，允许你修改或替换核心组件以满足特定需求。为此，主要有两种机制：传输适配器（Transport Adapters）处理发送请求的逻辑，而事件钩子（Event Hooks）则允许你拦截和处理响应。

本指南将演示如何创建和使用这两种机制来扩展 HTTP 请求的功能。

## 传输适配器

传输适配器（Transport Adapter）为处理特定 URL 协议（如 `http://` 或 `https://`）的请求提供了一个底层接口。当你发起请求时，`Session` 对象会根据请求的 URL 前缀选择合适的适配器，并将实际的网络通信委托给它。

通过创建自定义适配器，你可以实现独特的传输行为，例如自定义身份验证、专门的日志记录或非标准的重试逻辑。

### 创建自定义适配器

创建自定义适配器最简单的方法是继承 `requests.adapters.HTTPAdapter` 类并重写其方法。最常被重写的方法是 `send()`，它负责发送 `PreparedRequest`。

下面是一个简单适配器的示例，它会记录每个请求所花费的时间及其状态码。

```python
import requests
import time
from requests.adapters import HTTPAdapter

class TimingAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        start = time.time()
        print(f'Starting request to {request.url}')
        
        # 调用父类的 send 方法来执行实际的请求
        response = super().send(request, stream, timeout, verify, cert, proxies)
        
        end = time.time()
        total_time = round(end - start, 2)
        print(f'Request to {request.url} finished in {total_time}s with status {response.status_code}')
        
        return response
```

### 挂载适配器

创建自定义适配器后，你需要指示 `Session` 对象在处理某些请求时使用它。这可以通过 `mount()` 方法完成，该方法会将一个 URL 前缀与你的适配器关联起来。

```python
# 创建一个 session 对象
session = requests.Session()

# 创建自定义适配器的实例
timing_adapter = TimingAdapter()

# 挂载适配器以处理所有 HTTPS 请求
session.mount('https://', timing_adapter)

# 使用此 session 对 https:// URL 发起的任何请求都将使用我们的适配器
try:
    session.get('https://httpbin.org/get')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')

# 预期输出：
# Starting request to https://httpbin.org/get
# Request to https://httpbin.org/get finished in Xs with status 200
```

选择适配器时，Requests 会使用最具体的前缀。例如，对于发往某个主机的请求，如果同时存在挂载在 `'https://api.example.com'` 和 `'https://'` 上的适配器，那么前者将被选用。

### 使用适配器的请求生命周期

下图说明了传输适配器在请求生命周期中的位置。

```d2
direction: down

"请求已发起": {
  shape: oval
}

"Session 对象": {
  shape: rectangle
  "1. get_adapter(url)": {
    shape: rectangle
  }
}

"传输适配器": {
  shape: package
  "2. send(request)": {
    shape: rectangle
  }
  "4. build_response(raw_resp)": {
    shape: rectangle
  }
}

"网络通信": {
  shape: cylinder
  label: "HTTP/HTTPS"
}

"响应处理": {
  shape: rectangle
  "5. dispatch_hook('response', ...)"
}

"最终响应": {
  shape: oval
}

"请求已发起" -> "Session 对象"
"Session 对象" -> "传输适配器": "选择合适的适配器"
"传输适配器" -> "网络通信": "3. 发送请求"
"网络通信" -> "传输适配器": "接收原始响应"
"传输适配器" -> "响应处理": "返回 requests.Response 对象"
"响应处理" -> "最终响应": "返回给用户"
```

## 事件钩子

Requests 还提供了一个钩子系统，供开发者在请求过程的特定环节附加回调。最主要的可用钩子是 `response`，它在收到响应之后、返回给调用者之前被触发。

钩子对于全局性地检查或修改响应对象非常有用，无需封装每个请求调用。

### 使用 `response` 钩子

钩子就是一个函数，它接受 `response` 对象作为第一个参数，同时还接受传递给原始请求方法的任何其他关键字参数。

以下是一个打印响应头的钩子示例：

```python
import requests

def print_server_header(response, **kwargs):
    """此钩子打印 Server 头的值。"""
    if 'Server' in response.headers:
        print(f"Response was served by: {response.headers['Server']}")
    return response

# 将钩子附加到单个请求
requests.get('https://httpbin.org/get', hooks={'response': print_server_header})

# 预期输出：
# Response was served by: gunicorn/19.9.0
```

你也可以将钩子附加到 `Session` 对象上，使其对通过该 session 发出的每个请求都运行。

```python
session = requests.Session()
# 注意：session 钩子应该是一个可调用对象的列表
session.hooks['response'] = [print_server_header]

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/ip')
```

### 修改响应

钩子也可以修改响应对象。如果钩子函数返回值，该返回值将替换原始响应对象，用于任何后续处理以及最终返回给用户。

此示例展示了一个为响应附加自定义属性的钩子。

```python
import requests

def add_custom_attribute(response, **kwargs):
    response.hook_was_here = True
    return response

response = requests.get('https://httpbin.org/get', hooks={'response': add_custom_attribute})

if hasattr(response, 'hook_was_here') and response.hook_was_here:
    print("Custom attribute added by hook successfully.")

# 预期输出：
# Custom attribute added by hook successfully.
```