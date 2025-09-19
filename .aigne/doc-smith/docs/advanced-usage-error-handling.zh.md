# 错误处理

在处理网络请求时，预测并处理潜在问题至关重要，这些问题包括网络故障和服务器错误。Requests 库提供了一套全面的异常，帮助你编写健壮可靠的代码。该库特有的所有异常都是 `requests.exceptions.RequestException` 的子类。

## 处理 HTTP 状态码错误

一个常见的场景是处理带有错误状态码（4xx 表示客户端错误，5xx 表示服务器错误）的响应。虽然 Requests 不会为这些情况自动引发异常，但你可以使用 `Response.raise_for_status()` 方法来引发异常。

如果响应状态码在 400 到 599 之间，此方法将引发一个 `HTTPError`。

```python 处理 HTTP 错误 icon=logos:python
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

try:
    response = requests.get(url)

    # 如果响应成功，则不会引发异常
    response.raise_for_status()
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}')  # Python 3.6+
    print(f'状态码: {http_err.response.status_code}')
except Exception as err:
    print(f'发生其他错误: {err}')  # Python 3.6+
else:
    print('成功！')
```

## 处理连接和网络错误

对于网络层面的问题，如 DNS 解析失败、连接被拒绝或其他连接问题，Requests 将引发 `ConnectionError`。

```python 处理连接错误 icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://this-is-not-a-real-domain.org')
except ConnectionError as e:
    print(f'发生连接错误: {e}')
```

## 处理超时

如果一个请求耗时过长，将会引发 `Timeout` 异常。这个异常可以方便地捕获连接超时和读取超时两种情况。

- `ConnectTimeout`: 如果与远程服务器建立连接超时，则会发生此异常。
- `ReadTimeout`: 如果服务器未在指定时间内发送数据，则会发生此异常。

```python 处理超时 icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # 使用一个很短的超时来触发异常
    response = requests.get('https://httpbin.org/delay/5', timeout=1)
except Timeout:
    print('请求超时')
```

## 处理无效 URL

如果提供的 URL 格式不正确（例如，缺少 `http://` 这样的协议），Requests 将引发 `MissingSchema` 或 `InvalidURL` 等异常。由于这些异常都继承自 `RequestException`，你可以捕获这个基类异常来处理所有这类情况。

```python 处理 URL 错误 icon=logos:python
import requests
from requests.exceptions import RequestException

try:
    response = requests.get('invalid-url-without-schema')
except RequestException as e:
    # 这将捕获 MissingSchema、InvalidURL 和其他与请求相关的错误
    print(f'请求发生错误: {e}')
```

## 异常层次结构

了解异常的层次结构有助于编写精确的 `try...except` 块。下图展示了 Requests 中主要异常的继承结构。

```d2 异常层次结构图
direction: down

IOError

RequestException: {
  label: "requests.exceptions.RequestException"
}
IOError -> RequestException

ConnectionError -> RequestException
ProxyError -> ConnectionError
SSLError -> ConnectionError

Timeout -> RequestException
ReadTimeout -> Timeout
ConnectTimeout: {
  label: "ConnectTimeout"
}
ConnectTimeout -> ConnectionError
ConnectTimeout -> Timeout

HTTPError -> RequestException
TooManyRedirects -> RequestException
URLRequired -> RequestException

InvalidURL: {
  label: "InvalidURL (ValueError)"
}
InvalidURL -> RequestException
InvalidProxyURL -> InvalidURL

MissingSchema: {
  label: "MissingSchema (ValueError)"
}
MissingSchema -> RequestException

InvalidSchema: {
  label: "InvalidSchema (ValueError)"
}
InvalidSchema -> RequestException

ContentDecodingError -> RequestException
ChunkedEncodingError -> RequestException
StreamConsumedError -> RequestException

InvalidJSONError -> RequestException
JSONDecodeError -> InvalidJSONError

```

### 常见异常

以下是你可能遇到的最常见异常及其含义的列表。

| Exception | Description |
|---|---|
| `RequestException` | 基类异常。请求期间任何不明确的异常都属于此类。 |
| `HTTPError` | 由 `raise_for_status()` 针对不成功的状态码（4xx 或 5xx）引发。 |
| `ConnectionError` | 用于网络问题（如 DNS 解析失败、连接被拒绝）的通用异常。 |
| `ProxyError` | 配置的代理出现错误。 |
| `SSLError` | 发生 SSL 相关错误。 |
| `Timeout` | 请求超时。此异常捕获 `ConnectTimeout` 和 `ReadTimeout`。 |
| `ConnectTimeout` | 尝试连接到远程服务器时请求超时。 |
| `ReadTimeout` | 服务器在指定时间内未发送任何数据。 |
| `URLRequired` | 未提供有效的 URL 来发起请求。 |
| `TooManyRedirects` | 请求超出了配置的最大重定向次数。 |
| `MissingSchema` | URL 缺少协议（例如 `http://` 或 `https://`）。 |
| `InvalidURL` | 提供的 URL 无效。 |
| `JSONDecodeError` | 当 `response.json()` 无法解码响应内容时引发。 |

有关所有异常的完整列表，请参阅[异常 API 参考](./api-reference-exceptions.md)。