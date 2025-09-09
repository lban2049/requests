# 错误处理

当你使用 Requests 时，网络连接可能会失败，服务器可能无法访问，或者响应可能不是你所期望的。Requests 预料到这些问题，并会在它们发生时引发异常。本指南将引导你了解常见的异常以及如何优雅地处理它们。

所有由 Requests 引发的异常都继承自基类 `requests.exceptions.RequestException`。

## HTTP 状态码错误

默认情况下，对于失败的 HTTP 状态码（例如 `404 Not Found` 或 `500 Internal Server Error`），Requests 不会引发异常。要让 Requests 对这些响应引发异常，你可以使用 `Response` 对象的 `raise_for_status()` 方法。

如果状态码指示错误（4xx 为客户端错误，5xx 为服务器错误），`raise_for_status()` 将引发 `HTTPError`。

```python Handling HTTP Errors
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

try:
    response = requests.get(url)
    # 如果响应成功，则不会引发异常
    response.raise_for_status()
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}')
except Exception as err:
    print(f'发生其他错误: {err}')
else:
    print('成功！')
```

## 连接错误

对于网络层面的问题，例如 DNS 故障或连接被拒绝，Requests 将引发 `ConnectionError`。

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

url = 'https://this-is-a-nonexistent-domain.com'

try:
    response = requests.get(url)
except ConnectionError as e:
    print(f"发生连接错误: {e}")
```

## 超时

你可以使用 `timeout` 参数配置 requests，使其在等待指定秒数后停止等待响应。如果服务器没有及时响应，则会引发 `Timeout` 异常。

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

url = 'https://httpbin.org/delay/5' # 此端点会等待 5 秒后响应

try:
    # 设置 3 秒的超时时间
    response = requests.get(url, timeout=3)
except Timeout:
    print('请求超时')
```

`Timeout` 异常是一个基类，它同时捕获 `ConnectTimeout`（用于连接建立期间的超时）和 `ReadTimeout`（用于等待服务器数据时的超时）。如果你需要对这些情况进行不同处理，可以分别捕获它们。

## 重定向错误

Requests 会自动跟踪重定向。但是，如果请求链超过了最大重定向次数，它将引发 `TooManyRedirects` 异常。这有助于防止无限重定向循环。

```python Handling Too Many Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

# 此端点默认重定向 10 次
url = 'https://httpbin.org/redirect/10'

try:
    # 默认情况下，重定向限制为 30 次。我们假设一个限制更低的场景。
    # 在本例中，我们只捕获可能发生的异常。
    response = requests.get(url)
except TooManyRedirects:
    print('请求超出了最大重定向次数。')
```

## 无效 URL

如果你提供格式不正确的 URL，Requests 将会引发异常。最常见的是 `MissingSchema`，当 URL 不包含 `http://` 或 `https://` 时会发生。

```python Handling Invalid URLs icon=logos:python
import requests
from requests.exceptions import MissingSchema

try:
    response = requests.get('httpbin.org/get')
except MissingSchema as e:
    print(f'无效的 URL: {e}')
```

## 内容解码错误

当你尝试使用 `response.json()` 方法解析非有效 JSON 的响应体时，将会引发 `JSONDecodeError`。

```python Handling JSON Decode Errors icon=logos:python
import requests

url = 'https://httpbin.org/html' # 此端点返回 HTML，而非 JSON

try:
    response = requests.get(url)
    response.raise_for_status()
    data = response.json()
except requests.exceptions.JSONDecodeError:
    print("未能从响应中解码 JSON。")
except requests.exceptions.HTTPError as err:
    print(f'发生 HTTP 错误: {err}')
```

## 常见异常摘要

以下是你可能遇到的最常见异常的快速参考表：

| 异常 | 引发原因 |
|---|---|
| `RequestException` | 所有其他异常都继承自该基础异常。 |
| `HTTPError` | 发生 HTTP 错误（4xx 或 5xx 状态码）。由 `response.raise_for_status()` 引发。 |
| `ConnectionError` | 发生网络问题（例如，DNS 故障、连接被拒绝）。 |
| `ProxyError` | 代理服务器出现问题。 |
| `SSLError` | 发生 SSL 握手错误。 |
| `Timeout` | 请求超时。这包括 `ConnectTimeout` 和 `ReadTimeout`。 |
| `TooManyRedirects` | 请求超出了配置的最大重定向次数。 |
| `MissingSchema` | URL 缺少协议方案（例如 `http://` 或 `https://`）。 |
| `InvalidURL` | URL 格式错误。 |
| `JSONDecodeError` | 使用 `response.json()` 将响应内容解码为 JSON 时失败。 |

通过预料这些潜在的错误并使用 `try...except` 块，你可以构建能够优雅地处理网络问题和意外服务器响应的弹性应用程序。

现在你已经了解了如何处理错误，可以开始探索更复杂的场景了。请参阅我们的[高级用法](./advanced-usage.md)指南，了解有关会话对象、SSL 验证等更多内容。