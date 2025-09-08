# 会话对象

`Session` 对象允许您在多个请求之间持久化某些参数。它还会在从 Session 实例发出的所有请求中持久化 Cookie，并使用 `urllib3` 的连接池。如果您向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

## 基本用法

`Session` 对象拥有主 `requests` API 的所有方法。让我们从在多个请求之间持久化一些 Cookie 开始。

```python Session Basic Usage icon=logos:python
import requests
s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

为了正确管理资源，建议使用 `with` 语句，它通过在退出时调用 `Session.close()` 来确保会话被自动关闭，即使发生异常也是如此。

```python Session with Context Manager icon=logos:python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    response = s.get('https://httpbin.org/cookies')
    print(response.json())
```

**响应示例**
```json
{
  "cookies": {
    "sessioncookie": "123456789"
  }
}
```
会话中设置的任何 Cookie 都会随该会话发出的所有后续请求一起发送。这对于在多个 API 调用中保持登录状态特别有用。

## 持久化参数

会话还可以为请求方法提供默认数据。这是通过在 `Session` 对象上设置属性来完成的。您传递给请求方法的任何字典都将与会话级别的值合并。重要的是，方法级别的参数将始终覆盖会话级别的参数。

### 标头

在这里，我们在会话上设置了一个默认标头，然后该标头会与请求级别提供的标头合并。

```python Persisting Headers icon=logos:python
import requests

with requests.Session() as s:
    s.headers.update({'x-test': 'true'})

    # 'x-test' 和 'x-test2' 都会随请求发送
    response = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
    print(response.json()['headers'])
```

**响应示例**
```json
{
  "X-Test": "true", 
  "X-Test2": "true",
  ...
}
```

### 查询参数

会话级别的查询参数也会与特定请求中提供的任何参数合并。

```python Persisting Query Parameters icon=logos:python
import requests

with requests.Session() as s:
    s.params = {'api_key': 'shared_key'}

    # 请求被发送到 https://httpbin.org/get?api_key=shared_key
    response = s.get('https://httpbin.org/get')
    print(response.json()['args'])
```
**响应示例**
```json
{
  "api_key": "shared_key"
}
```

### 其他配置

您还可以在会话级别设置其他请求参数，例如身份验证凭据、代理和 SSL 验证设置。

```python Other Session Configurations icon=logos:python
import requests

with requests.Session() as s:
    # 为所有请求设置默认身份验证
    s.auth = ('username', 'password')

    # 设置默认代理
    s.proxies = {
        'http': 'http://10.10.1.10:3128',
        'https': 'http://10.10.1.10:1080',
    }

    # 设置默认 SSL 证书验证
    s.verify = '/path/to/my/ca.pem'

    # 此请求将使用在会话上定义的
    # auth、proxies 和 verify 设置。
    response = s.get('https://api.example.com/data')
```

## 性能与连接池

当您使用 `Session` 对象向同一主机发出多个请求时，它会重用底层的 TCP 连接。这个过程被称为连接池，它避免了为每个请求建立新连接的开销，这对于需要 TLS 握手的 HTTPS 流量尤其有利。

在底层，`Session` 对象使用 `requests.adapters.HTTPAdapter`，后者通过 `urllib3.PoolManager` 管理连接池。

下图说明了连接处理方式的差异。

```d2
direction: down

subgraph "Individual Requests" {
  shape: rectangle
  label: "独立请求"
  App1: App {shape: rectangle}
  Server1: Server {shape: cylinder}
  
  App1 -> Server1: "请求 1：新 TCP 连接"
  App1 -> Server1: "请求 2：新 TCP 连接"
  App1 -> Server1: "请求 3：新 TCP 连接"
}


subgraph "Session-based Requests" {
  shape: rectangle
  label: "基于会话的请求"
  App2: App {shape: rectangle}
  Session: Session {shape: rectangle}
  Server2: Server {shape: cylinder}

  App2 -> Session: 创建
  Session -> Server2: "请求 1：新 TCP 连接"
  Session -> Server2: "请求 2：重用连接" {style.stroke-dash: 2}
  Session -> Server2: "请求 3：重用连接" {style.stroke-dash: 2}
}
```

## 高级控制

除了基本的参数持久化，`Session` 对象还提供了对网络行为的更精细控制。

### 重定向处理

会话会自动处理重定向。您可以使用 `Session` 对象上的 `max_redirects` 属性来控制此行为。默认情况下，它被设置为 30。

```python Controlling Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

with requests.Session() as s:
    s.max_redirects = 3 # 默认为 30

    try:
        # 这个 URL 会重定向 4 次。
        response = s.get('https://httpbin.org/redirect/4') 
    except TooManyRedirects as e:
        print(f"Redirect limit exceeded: {e}")

# 预期输出：
# 超出重定向限制：超过 3 次重定向。
```

### 传输适配器

Requests 可以通过传输适配器进行扩展，允许您为特定的传输协议定义自定义的交互方法。例如，您可以为 HTTP 请求实现自定义的重试策略。

`Session` 对象允许您为特定前缀挂载这些适配器。

```python Custom Retry Strategy icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

# 创建一个会话
s = requests.Session()

# 定义重试策略
retry_strategy = Retry(
    total=3,
    status_forcelist=[429, 500, 502, 503, 504], # 对这些状态码进行重试
    backoff_factor=0.3
)

# 创建一个带有重试策略的适配器并挂载它
adapter = HTTPAdapter(max_retries=retry_strategy)
s.mount('https://', adapter)
s.mount('http://', adapter)

try:
    # 向一个会失败的端点发出请求
    response = s.get('https://httpbin.org/status/503')
    response.raise_for_status()
except requests.exceptions.RetryError as e:
    print(f"Request failed after multiple retries: {e}")
```
在此示例中，通过会话向 `http://` 或 `https://` URL 发出的任何 `GET` 请求，如果返回指定的服务端错误状态码之一，将会重试最多 3 次。

通过使用 `Session` 对象，您可以编写更清晰的代码，轻松管理像 Cookie 这样的状态，并提高应用程序的性能和弹性。

---

下一节将介绍如何处理不同类型的身份验证，使用会话通常可以简化这项任务。

继续阅读下一节，了解[身份验证](./user-guide-authentication.md)。