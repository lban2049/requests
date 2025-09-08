# 错误处理

在构建依赖外部服务的应用程序时，预测和处理潜在问题至关重要。可能会出现网络问题，服务器可能会发生故障，响应也可能不符合预期。Requests 提供了一系列异常，以帮助您妥善处理这些情况。

Requests 抛出的所有异常都继承自基类 `requests.exceptions.RequestException`。

## HTTP 状态码错误

对于不成功的 HTTP 响应（即 4xx 或 5xx 范围内的状态码），您可以使用 `Response.raise_for_status()` 方法。这是一种检查请求是否成功并在不成功时抛出 `HTTPError` 的便捷方法。

```python Handling HTTP Errors icon=logos:python
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

try:
    response = requests.get(url)
    # 如果响应成功，则不会抛出异常
    response.raise_for_status()
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}')
except Exception as err:
    print(f'发生其他错误: {err}')
else:
    print('成功！')
```

运行此代码时，`raise_for_status()` 调用将抛出一个 `HTTPError`，并附带一条指示客户端错误的消息：

```text Expected Output
发生 HTTP 错误: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

## 连接错误

如果发生网络问题（例如，DNS 解析失败、连接被拒绝），Requests 将抛出 `ConnectionError`。

例如，尝试连接到无效或无法访问的域名将触发此异常。

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://example.invalid-domain')
except ConnectionError as e:
    print(f"发生连接错误: {e}")
```

## 超时

您可以配置 requests 在给定的秒数后停止等待响应。如果服务器未及时响应，则会抛出 `Timeout` 异常。

`requests.exceptions.Timeout` 异常是两个更具体的异常 `ConnectTimeout` 和 `ReadTimeout` 的父类。这使您可以使用单个 `except` 块捕获这两种类型的超时。

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # 尝试使用非常短的超时时间连接到一个慢速端点
    response = requests.get('https://httpbin.org/delay/5', timeout=1)
except Timeout:
    print('请求超时')
```

有关超时的更详细配置，请参阅[超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md)部分。

## 无效 URL

如果您提供的 URL 格式不正确，Requests 将抛出一个指示问题的异常。最常见的是 `MissingSchema`，如果您忘记包含 `http://` 或 `https://`，就会发生此异常。

```python Handling Invalid URLs icon=logos:python
import requests
from requests.exceptions import MissingSchema

try:
    response = requests.get('google.com')
except MissingSchema as e:
    print(f"无效 URL: {e}")
```

这将输出一条有用的消息，建议正确的格式：
`Invalid URL: Invalid URL 'google.com': No scheme supplied. Perhaps you meant https://google.com?`

## 重定向错误

默认情况下，Requests 会处理重定向。但是，如果请求超过默认的 30 次重定向限制，它将抛出 `TooManyRedirects` 异常，以防止其陷入重定向循环。

```python Handling Too Many Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

try:
    # httpbin.org/redirect/N 会重定向 N 次
    response = requests.get('https://httpbin.org/redirect/35')
except TooManyRedirects:
    print('重定向次数过多')
```

## JSON 解码错误

当您使用 `response.json()` 解析响应时，如果响应体不包含有效的 JSON，您可能会遇到 `JSONDecodeError`。如果服务器返回错误页面（如 HTML）而不是预期的 JSON，就可能发生这种情况。

```python Handling JSON Errors icon=logos:python
import requests
from requests.exceptions import JSONDecodeError

url = 'https://httpbin.org/html' # 此端点返回 HTML，而非 JSON

try:
    response = requests.get(url)
    response.raise_for_status()
    data = response.json()
except JSONDecodeError:
    print("未能从响应中解码 JSON。")
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}')
```

## 异常层次结构

了解异常层次结构可以帮助您编写更有效的错误处理逻辑。例如，由于 `SSLError` 继承自 `ConnectionError`，因此 `except ConnectionError:` 块也会捕获 SSL 错误。

以下是最常见异常的简化图：

```d2
direction: down

RequestException: {
  label: "RequestException"
  shape: rectangle
}

HTTPError: { 
  label: "HTTPError"
  shape: rectangle 
}
ConnectionError: { 
  label: "ConnectionError"
  shape: rectangle 
}
Timeout: { 
  label: "Timeout"
  shape: rectangle 
}
TooManyRedirects: { 
  label: "TooManyRedirects"
  shape: rectangle 
}
MissingSchema: { 
  label: "MissingSchema"
  shape: rectangle 
}

RequestException -> HTTPError
RequestException -> ConnectionError
RequestException -> Timeout
RequestException -> TooManyRedirects
RequestException -> MissingSchema

ConnectTimeout: { 
  label: "ConnectTimeout"
  shape: rectangle 
}
ReadTimeout: { 
  label: "ReadTimeout"
  shape: rectangle 
}
ProxyError: { 
  label: "ProxyError"
  shape: rectangle 
}
SSLError: { 
  label: "SSLError"
  shape: rectangle 
}

ConnectionError -> ConnectTimeout
ConnectionError -> ProxyError
ConnectionError -> SSLError
Timeout -> ConnectTimeout
Timeout -> ReadTimeout
```

### 常见异常摘要

以下是您将遇到的最常见异常的快速参考：

| 异常 | 描述 |
|---|---|
| `RequestException` | 基础异常类。Requests 抛出的所有其他异常都继承自该类。 |
| `HTTPError` | 通过 `response.raise_for_status()` 对不成功的响应（4xx 或 5xx 状态码）抛出。 |
| `ConnectionError` | 因 DNS 解析失败或连接被拒绝等网络相关问题而抛出。 |
| `Timeout` | 当请求超时时抛出。可捕获 `ConnectTimeout` 和 `ReadTimeout`。 |
| `TooManyRedirects` | 当请求超过配置的最大重定向次数时抛出。 |
| `MissingSchema` | 当提供的 URL 没有协议方案（例如 `http://` 或 `https://`）时抛出。 |
| `JSONDecodeError` | 当 `response.json()` 解码响应内容失败时抛出。 |

通过处理这些异常，您可以使您的应用程序对网络故障和意外的服务器行为更具弹性。对于更复杂的场景，您可能需要探索[高级用法](./advanced-usage.md)。