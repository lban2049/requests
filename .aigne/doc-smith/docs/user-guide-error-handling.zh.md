# 错误处理

在构建依赖外部服务的应用时，预测并处理潜在问题至关重要。可能会出现网络问题、服务器故障，响应也可能不符合预期。Requests 提供了一系列异常，以帮助你妥善处理这些情况。

Requests 抛出的所有异常都继承自基类 `requests.exceptions.RequestException`。

## HTTP 状态码错误

对于不成功的 HTTP 响应（即状态码在 4xx 或 5xx 范围内），你可以使用 `Response.raise_for_status()` 方法。这是一种便捷的方法，用于检查请求是否成功，并在请求失败时抛出 `HTTPError`。

```python
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

try:
    response = requests.get(url)
    # 如果响应成功，则不会抛出异常
    response.raise_for_status()
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}')  # Python 3.6+
except Exception as err:
    print(f'发生其他错误: {err}')  # Python 3.6+
else:
    print('成功！')
```

运行此代码时，`raise_for_status()` 调用将抛出一个 `HTTPError`，其消息会指明客户端错误：

```
发生 HTTP 错误: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

## 连接错误

如果发生网络问题（例如 DNS 解析失败、连接被拒绝），Requests 将抛出 `ConnectionError`。

例如，尝试连接到一个无效或无法访问的域名会触发此异常。

```python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://example.invalid-domain')
except ConnectionError as e:
    print(f"发生连接错误: {e}")
```

## 超时

你可以配置 requests，使其在等待响应超过指定秒数后停止。如果服务器未在规定时间内响应，则会抛出 `Timeout` 异常。

`requests.exceptions.Timeout` 异常是 `ConnectTimeout` 和 `ReadTimeout` 这两个更具体异常的父类。这使你可以用一个 `except` 块捕获这两种类型的超时。

```python
import requests
from requests.exceptions import Timeout

try:
    # 尝试使用极短的超时时间连接到一个响应缓慢的端点
    response = requests.get('https://httpbin.org/delay/5', timeout=1)
except Timeout:
    print('请求超时')
```

有关超时配置的更多详细信息，请参阅 [超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md) 部分。

## 无效 URL

如果你提供的 URL 格式不正确，Requests 将抛出一个异常来指明问题所在。最常见的是 `MissingSchema`，当你忘记添加 `http://` 或 `https://` 时会发生此异常。

```python
import requests
from requests.exceptions import MissingSchema

try:
    response = requests.get('google.com')
except MissingSchema as e:
    print(f"无效的 URL: {e}")
```

这将输出一条有帮助性的提示信息，建议正确的格式：
`无效的 URL: Invalid URL 'google.com': No scheme supplied. Perhaps you meant https://google.com?`

## 重定向错误

默认情况下，Requests 会处理重定向。但是，如果一个请求超出了默认的 30 次重定向限制，它将抛出 `TooManyRedirects` 异常，以防止其陷入重定向循环。

```python
import requests
from requests.exceptions import TooManyRedirects

try:
    # httpbin.org/redirect/N 会重定向 N 次
    response = requests.get('https://httpbin.org/redirect/35')
except TooManyRedirects:
    print('重定向次数过多')
```

## 异常层级结构

理解异常的层级结构有助于你编写更高效的错误处理逻辑。例如，由于 `SSLError` 继承自 `ConnectionError`，一个 `except ConnectionError:` 块也能捕获到 SSL 错误。

以下是常见异常的简化示意图：

```d2
direction: down

RequestException: {
  shape: class
}

HTTPError: { shape: class }
ConnectionError: { shape: class }
Timeout: { shape: class }
TooManyRedirects: { shape: class }
MissingSchema: { shape: class }

RequestException -> HTTPError
RequestException -> ConnectionError
RequestException -> Timeout
RequestException -> TooManyRedirects
RequestException -> MissingSchema

ConnectTimeout: { shape: class }
ReadTimeout: { shape: class }
ProxyError: { shape: class }
SSLError: { shape: class }

ConnectionError -> ConnectTimeout
ConnectionError -> ProxyError
ConnectionError -> SSLError
Timeout -> ConnectTimeout
Timeout -> ReadTimeout
```

### 常见异常总结

以下是你将遇到的最常见异常的快速参考：

| 异常 | 描述 |
|---|---|
| `RequestException` | 基础异常类。Requests 抛出的所有其他异常都继承自该类。 |
| `HTTPError` | 通过 `response.raise_for_status()` 针对不成功的响应（4xx 或 5xx 状态码）抛出。 |
| `ConnectionError` | 针对网络相关问题（如 DNS 解析失败或连接被拒绝）抛出。 |
| `Timeout` | 当请求超时时抛出。可同时捕获 `ConnectTimeout` 和 `ReadTimeout`。 |
| `TooManyRedirects` | 当请求超过配置的最大重定向次数时抛出。 |
| `MissingSchema` | 当提供的 URL 没有协议方案（如 `http://` 或 `https://`）时抛出。 |
| `JSONDecodeError` | 当 `response.json()` 解码响应内容失败时抛出。 |

通过处理这些异常，你可以使你的应用程序在面对网络故障和意外的服务器行为时更具弹性。对于更复杂的场景，你可能需要了解 [高级用法](./advanced-usage.md)。