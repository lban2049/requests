# 自定义适配器和钩子

Requests 通过传输适配器和事件钩子系统提供了强大的低级别定制选项。适配器允许你修改或替换请求发送的核心逻辑，而钩子则可以注册回调函数来检查或更改请求生命周期的各个部分，主要是响应部分。

本节将介绍如何扩展 Requests 的功能，以应对自定义重试策略、非标准身份验证或响应后处理等高级用例。

## 传输适配器

传输适配器是 Requests 处理网络操作的核心。当你调用 `requests.get()` 等方法时，`Session` 对象会根据 URL 前缀（如 `http://` 或 `https://`）确定合适的适配器，并将请求委托给它。默认的适配器是 `HTTPAdapter`，它使用 `urllib3` 库处理所有 HTTP 和 HTTPS 通信。

通过创建自定义适配器，你可以为特定协议或主机实现独特的传输行为。

### 创建自定义适配器

要创建自定义适配器，你需要继承 `requests.adapters.BaseAdapter` 类，并至少实现 `send()` 方法。该方法负责执行请求，且必须返回一个 `requests.Response` 对象。

`send` 方法的基本签名如下：

```python
def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
    """发送 PreparedRequest 对象。返回 Response 对象。"""
    raise NotImplementedError
```

一个实用的方法是继承现有的 `HTTPAdapter` 并重写其方法。这样，你就可以在不重写整个 HTTP/HTTPS 连接逻辑的情况下添加新功能。

#### 示例：自定义重试适配器

`HTTPAdapter` 已通过 `urllib3` 支持重试逻辑。你可以创建一个专门的适配器，根据特定需求（例如仅在某些 HTTP 状态码上重试）来配置此行为。

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

class RetryAdapter(HTTPAdapter):
    def __init__(self, *args, **kwargs):
        # 配置重试策略，在出现 5xx 服务器错误时进行重试
        retries = Retry(
            total=5, # 重试总次数
            backoff_factor=0.2, # 两次尝试之间的延迟因子
            status_forcelist=[500, 502, 503, 504], # 需要重试的状态码
            allowed_methods=frozenset(['GET', 'POST']) # 需要重试的方法
        )
        # 'max_retries' 参数会传递给父类 HTTPAdapter
        super().__init__(max_retries=retries, *args, **kwargs)

# 创建一个会话并为所有 HTTPS 请求挂载自定义适配器
session = requests.Session()
session.mount("https://", RetryAdapter())

try:
    # 如果此请求返回 503 状态码，它将被重试最多 5 次
    response = session.get("https://httpbin.org/status/503")
    print(f"Request succeeded with status: {response.status_code}")
except requests.exceptions.RetryError as e:
    print(f"Request failed after multiple retries: {e}")

```

### 挂载适配器

自定义适配器通过 `session.mount()` 方法注册到 `Session` 对象。你需要将适配器实例与一个 URL 前缀关联，会话将为给定的请求 URL 使用具有最长匹配前缀的适配器。

例如，你可以为不同的服务挂载不同的适配器：

```python
s = requests.Session()

# 对大多数网站使用标准适配器
s.mount('https://', HTTPAdapter())

# 仅对特定 API 使用我们的特殊重试适配器
s.mount('https://api.example.com', RetryAdapter())

s.get('https://google.com') # 使用标准 HTTPAdapter
s.get('https://api.example.com/data') # 使用 RetryAdapter
```

### 适配器请求流程

下图说明了 `Session` 如何选择并使用适配器来发送请求。

```d2
shape: sequence_diagram

User
Session
Adapter: CustomAdapter

User -> Session: "session.get(url, ...)"
Session -> Session: "调用 get_adapter(url) 查找匹配的适配器"
Session -> Adapter: "send(prepared_request, **kwargs)"
Adapter -> Session: "返回 Response 对象"
Session -> User: "返回最终的 Response 对象"
```

## 事件钩子

Requests 还包含一个钩子系统，允许你将回调函数附加到请求/响应周期中的单个事件：`response`。

此钩子在从服务器接收到响应之后、返回给应用程序代码之前触发。钩子对于实现全局日志记录、响应修改或集中式错误处理等横切关注点非常有用。

### `response` 钩子

唯一可用的钩子是 `response`。`response` 钩子是一个可调用对象，它接受 `response` 对象作为第一个参数，并接受传递给请求方法的其他任何关键字参数。

```python
def my_hook(response, **kwargs):
    # 检查响应
    print(f"Received response from {response.url} with status {response.status_code}")

    # 可选择修改并返回响应
    if 'X-Special-Header' not in response.headers:
        response.headers['X-Special-Header'] = 'Added by hook!'
    return response
```
如果钩子函数返回值，该值将替换原始响应。如果返回 `None`，则使用原始响应。

### 注册钩子

你可以在 `Session` 对象上为所有后续请求注册钩子，也可以按单次请求注册。

#### 会话级别钩子
钩子存储在 `session.hooks` 字典中。每个事件键对应的值应该是一个可调用对象列表。

```python
import requests

def log_response_details(response, **kwargs):
    print(f"URL: {response.url}, Elapsed: {response.elapsed}")

session = requests.Session()
session.hooks['response'] = [log_response_details]

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/delay/1')
```

#### 单次请求钩子
或者，你也可以将 `hooks` 字典直接传递给请求方法。

```python
import requests

def check_for_error(response, **kwargs):
    """一个自动为 HTTP 错误引发异常的钩子。"""
    response.raise_for_status()

try:
    # 此请求将使用钩子并引发异常
    requests.get('https://httpbin.org/status/500', hooks={'response': [check_for_error]})
except requests.exceptions.HTTPError as e:
    print(f"Caught expected error: {e}")

# 此请求不使用钩子，因此不会引发异常
response = requests.get('https://httpbin.org/status/500')
print(f"Request without hook completed with status: {response.status_code}")
```

### 钩子执行流程

钩子系统在将响应返回给用户之前对其进行处理，从而允许在关键点进行检查或修改。

```d2
direction: down

A: "通过适配器发送请求"
B: "收到响应"
C: "是否存在 'response' 事件的钩子？" {
  shape: diamond
}
D: "遍历钩子函数"
E: "钩子是否返回值？" {
    shape: diamond
}
F: "用新值替换响应"
G: "向用户返回最终响应"

A -> B -> C

C -> D: 是
C -> G: 否

D -> E: "调用 hook(response, **kwargs)"
E -> F: 是
F -> D
E -> D: 否

D -> G: "所有钩子执行完毕"
```

通过利用自定义适配器和钩子，你可以定制 Requests 的行为以适应几乎任何网络需求。有关所讨论的类和方法的更详细信息，请参阅[API 参考](./api-reference.md)。