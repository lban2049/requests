# 错误处理

构建依赖网络请求的应用程序时，必须预见到可能会出现问题。网络可能不可靠，服务器可能宕机，URL 格式也可能错误。Requests 库提供了一套全面的异常，可以帮助你妥善处理这些情况。

Requests 抛出的所有异常都继承自基类 `requests.exceptions.RequestException`。这样，你就可以使用一个 `try...except` 块来捕获该库引发的任何错误。

```python Handling Requests Exceptions icon=logos:python
import requests

try:
    # 可能失败的操作
    response = requests.get('https://a-very-unreliable-server.com', timeout=1)
    response.raise_for_status()
except requests.exceptions.RequestException as e:  
    # 这将捕获 Requests 库抛出的任何异常。
    print(f"An error occurred: {e}")
```

下面我们来探讨最常见的错误类型以及如何专门处理它们。

## 处理 HTTP 状态码错误

默认情况下，Requests 不会将不成功的 HTTP 状态码（如 `404 Not Found` 或 `500 Internal Server Error`）视为会导致程序崩溃的错误。然而，通常需要将这些响应视为失败。最简单的方法是使用 `Response.raise_for_status()` 方法。

如果响应状态码在 400 到 599 之间，`raise_for_status()` 将引发一个 `HTTPError`。

```python Checking for HTTP Errors icon=logos:python
import requests
from requests.exceptions import HTTPError

urls = ['https://httpbin.org/get', 'https://httpbin.org/status/404', 'https://httpbin.org/status/500']

for url in urls:
    try:
        response = requests.get(url)
        # 如果响应成功，则不会引发异常
        response.raise_for_status()
    except HTTPError as http_err:
        print(f'HTTP error occurred: {http_err}')
    except Exception as err:
        print(f'Other error occurred: {err}')
    else:
        print('Success!')
```

这段代码会成功处理第一个 URL，但会为 404 和 500 状态码捕获 `HTTPError` 异常，从而防止应用程序使用错误的响应继续执行。

## 连接错误

当请求因网络层级问题（如 DNS 解析失败、连接被拒绝或其他导致客户端无法访问服务器的问题）而失败时，会发生 `ConnectionError`。

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://this-domain-does-not-exist.org')
except ConnectionError as e:
    print(f"A connection error occurred: {e}")
```

这是一个涵盖多种网络问题的宽泛异常。更具体的异常（如 `ProxyError` 和 `SSLError`）也继承自 `ConnectionError`。

## 超时

你可以配置请求在等待响应指定秒数后停止。如果服务器未及时响应，则会引发 `Timeout` 异常。

`Timeout` 异常是一个通用的异常，涵盖了两种更具体的超时事件：
*   `ConnectTimeout`：在尝试与远程服务器建立连接时超时发生。
*   `ReadTimeout`：服务器已接受连接，但在指定时间内未能发回任何数据时发生。

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # httpbin.org/delay/5 需要 5 秒才能响应。
    response = requests.get('https://httpbin.org/delay/5', timeout=2)
except Timeout:
    print('The request timed out.')
```

## 重定向次数过多

当服务器以重定向状态码 (3xx) 响应时，Requests 会自动跟随。为防止无限重定向循环，Requests 默认在 30 次重定向后停止，并引发 `TooManyRedirects` 异常。

```python Handling Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

try:
    # 此 URL 将重定向 11 次。
    response = requests.get('https://httpbin.org/redirect/11', allow_redirects=True)
    print(f"Final URL: {response.url}")

    # 这将失败，因为此端点的默认限制是 10 次。
    response_fail = requests.get('https://httpbin.org/absolute-redirect/11')
except TooManyRedirects:
    print('The request exceeded the redirect limit.')
```

## 异常层次结构

理解异常的层次结构有助于编写更精确的错误处理逻辑。例如，捕获 `ConnectionError` 也会捕获 `ProxyError` 和 `SSLError`。

下表列出了最常见的异常及其关系：

| Exception | Description | Parent Class(es) |
| :--- | :--- | :--- |
| `RequestException` | 所有其他异常都继承自该基础异常。 | `IOError` |
| `HTTPError` | 通过 `raise_for_status()` 对不成功的状态码（4xx 或 5xx）引发。 | `RequestException` |
| `ConnectionError` | 与网络连接相关的错误的基类。 | `RequestException` |
| `ProxyError` | 配置的代理出现错误。 | `ConnectionError` |
| `SSLError` | 发生 SSL 相关错误。 | `ConnectionError` |
| `Timeout` | 请求超时。捕获 `ConnectTimeout` 和 `ReadTimeout`。 | `RequestException` |
| `ConnectTimeout` | 尝试连接服务器时请求超时。 | `ConnectionError`, `Timeout` |
| `ReadTimeout` | 服务器在规定时间内未发送任何数据。 | `Timeout` |
| `URLRequired` | 未提供有效的 URL 来发出请求。 | `RequestException` |
| `TooManyRedirects` | 请求超出了配置的最大重定向次数。 | `RequestException` |
| `MissingSchema` | URL 缺少协议（例如 `http://` 或 `https://`）。 | `RequestException`, `ValueError` |
| `InvalidURL` | 提供的 URL 格式错误。 | `RequestException`, `ValueError` |
| `JSONDecodeError` | 当 `response.json()` 解析响应内容失败时引发。 | `RequestException` |

通过利用这些异常，你可以构建出健壮且有弹性的应用程序，从而有效处理网络故障和意外的服务器响应。

对于更复杂的场景，你可能需要探索高级功能。请继续阅读[高级用法](./advanced-usage.md)部分，了解有关自定义超时、代理和 SSL 验证的更多信息。