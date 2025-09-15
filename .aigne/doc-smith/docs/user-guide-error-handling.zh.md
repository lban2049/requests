# 错误处理

在构建与 Web 服务交互的应用程序时，稳健的错误处理至关重要。网络连接可能不可靠，服务器可能宕机，响应也可能不总是符合预期。Requests 旨在通过针对各种错误情况抛出异常来优雅地处理这些状况。本指南将带你了解可能遇到的常见异常以及如何有效处理它们。

Requests 抛出的几乎所有异常都继承自基类异常 `requests.exceptions.RequestException`。这样，如果需要，就可以很方便地使用单个 `try...except` 块来捕获该库中所有潜在的错误。

## HTTP 状态码错误

最常见的任务之一是检查请求是否成功。成功的响应通常由 2xx 范围内的状态码表示。任何 4xx 或 5xx 的状态码分别表示客户端或服务器错误。

你可以使用 `Response.raise_for_status()` 方法，而无需手动检查 `response.status_code`。如果请求返回了不成功的状态码，该方法将抛出一个 `HTTPError`。

```python http_error_example.py icon=logos:python
import requests

try:
    response = requests.get('https://httpbin.org/status/404')
    print(f"请求成功，状态码： {response.status_code}")

    # 如果状态是 4xx 或 5xx，此行将抛出 HTTPError
    response.raise_for_status()

except requests.exceptions.HTTPError as http_err:
    print(f'发生 HTTP 错误： {http_err}')
    # 原始响应对象附加在异常上
    print(f'状态码： {http_err.response.status_code}')
    print(f'原因： {http_err.response.reason}')
except Exception as err:
    print(f'发生意外错误： {err}')
```

运行此代码将尝试获取一个返回 404 Not Found 状态的 URL，这将触发 `HTTPError` 异常。

## 连接和超时错误

网络问题随时可能发生。域名可能不存在，服务器可能宕机，或者连接可能超时。对于这些类型的网络问题，Requests 会抛出 `ConnectionError`。

```python connection_error_example.py icon=logos:python
import requests

try:
    response = requests.get('https://this-is-not-a-real-domain.com')
except requests.exceptions.ConnectionError as conn_err:
    print(f'发生连接错误： {conn_err}')
```

超时是另一个常见的网络问题。你可以使用请求中的 `timeout` 参数来配置超时。如果服务器在指定时间内没有响应，Requests 将抛出 `Timeout` 异常。`Timeout` 异常是 `ConnectTimeout`（初始连接超时）和 `ReadTimeout`（服务器在响应中途停止发送数据）的父类。

```python timeout_example.py icon=logos:python
import requests

try:
    # httpbin.org 的 /delay 端点会等待指定的秒数
    # 我们将超时时间设置得比延迟时间短。
    response = requests.get('https://httpbin.org/delay/5', timeout=2)
except requests.exceptions.Timeout as timeout_err:
    print(f'请求超时： {timeout_err}')
```

## 重定向错误

默认情况下，Requests 会自动跟随重定向。但是，如果服务器配置错误并创建了重定向循环，你的应用程序可能会陷入困境。为了防止这种情况发生，Requests 在默认 30 次重定向后会抛出 `TooManyRedirects` 异常。

```python redirect_error_example.py icon=logos:python
import requests

try:
    # 此端点重定向 10 次。
    # 如果你将限制设置得更低，它会抛出错误。
    # 在此示例中，我们假设已达到默认限制。
    response = requests.get('https://httpbin.org/absolute-redirect/35')
except requests.exceptions.TooManyRedirects as redirect_err:
    print(f'重定向次数过多： {redirect_err}')
```

## 无效 URL 错误

如果你提供的 URL 格式不正确或缺少必要的组成部分（如协议方案 `http://` 或 `https://`），Requests 将抛出异常，通常是 `MissingSchema` 或 `InvalidURL`。

```python url_error_example.py icon=logos:python
import requests

try:
    response = requests.get('httpbin.org/get') # 缺少 'https://'
except requests.exceptions.MissingSchema as schema_err:
    print(f'无效的 URL： {schema_err}')
```

## 异常层次结构

理解异常的层次结构有助于你编写更精确的错误处理逻辑。例如，由于 `ProxyError` 和 `SSLError` 是 `ConnectionError` 的子类，因此捕获 `ConnectionError` 也会捕获这两个异常。

下表列出了最常见的异常及其关系：

| Exception                  | Inherits From            | Description                                                        |
| -------------------------- | ------------------------ | ------------------------------------------------------------------ |
| `RequestException`         | `IOError`                | 任何与 Requests 相关问题的基类异常。                                 |
| `HTTPError`                | `RequestException`       | 因状态码不成功（4xx 或 5xx）而抛出。                                 |
| `ConnectionError`          | `RequestException`       | 封装了像 DNS 解析失败等常见的网络层错误。                           |
| `ProxyError`               | `ConnectionError`        | 表示配置的代理服务器存在问题。                                     |
| `SSLError`                 | `ConnectionError`        | 发生 SSL 握手错误。                                                |
| `Timeout`                  | `RequestException`       | 请求超时。这是更具体超时异常的基类。                               |
| `ConnectTimeout`           | `Timeout`, `ConnectionError` | 尝试建立连接时发生超时。                                           |
| `ReadTimeout`              | `Timeout`                | 服务器在规定时间内未发送任何数据。                                 |
| `URLRequired`              | `RequestException`       | 未提供有效的 URL 来发起请求。                                      |
| `TooManyRedirects`         | `RequestException`       | 请求超出了配置的重定向限制。                                       |
| `InvalidURL`               | `RequestException`       | 提供的 URL 格式不正确。                                            |
| `MissingSchema`            | `InvalidURL`             | URL 缺少协议方案（例如 `http://`）。                               |
| `JSONDecodeError`          | `RequestException`       | 当 `response.json()` 解码响应体失败时抛出。                        |

通过利用此层次结构，你可以决定错误处理的精细程度。例如，你可以捕获特定的 `ConnectTimeout` 来实现重试机制，同时捕获通用的 `RequestException` 来记录所有其他未预见的问题。

---

掌握了这些知识，你就可以构建出更具弹性的应用程序，从而优雅地处理网络故障和意外的服务器响应。要更详细地控制网络行为，请参阅[超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md)部分。