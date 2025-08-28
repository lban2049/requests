# 错误处理

在处理网络请求时，可能会出现问题。服务器可能不可用，连接可能中断，或者您可能会遇到无效的 URL。Requests 旨在通过引发异常来处理这些情况。这使您能够构建可以从容管理故障的弹性应用程序。

Requests 引发的所有异常都继承自基类异常 `requests.exceptions.RequestException`。

## 状态码不成功：`HTTPError`

最常见的检查之一是查看请求是否成功。如果服务器返回错误状态码（在 4xx 或 5xx 范围内），Requests 不会自动引发异常。但是，您可以使用 `response.raise_for_status()` 方法来引发异常。

如果请求不成功，将会引发 `HTTPError`。

```python
import requests

for status_code in [404, 503]:
    try:
        url = f'https://httpbin.org/status/{status_code}'
        response = requests.get(url)

        # 如果 HTTP 请求返回不成功的状态码，此行代码将引发 HTTPError。
        response.raise_for_status()
    except requests.exceptions.HTTPError as err:
        print(f'HTTP error for status {status_code}: {err}')
    except Exception as err:
        print(f'An unexpected error occurred: {err}')
```

此代码将针对 404（客户端错误）和 503（服务器错误）状态码生成输出，演示 `raise_for_status()` 如何帮助捕获这些问题。

## 连接问题：`ConnectionError`

网络层面的错误，例如 DNS 解析失败、连接被拒或其他连接问题，将引发 `ConnectionError`。

```python
import requests

try:
    response = requests.get('https://this-domain-does-not-exist.com')
except requests.exceptions.ConnectionError as err:
    print(f'Connection error occurred: {err}')
```

当您的应用程序无法访问目标服务器时，此异常是一个很好的通用捕获方式。

## 超时

您可以配置 requests 在等待指定秒数后停止接收响应。如果达到超时时间，则会引发 `Timeout` 异常。`Timeout` 异常是更具体的超时异常的父类：

- `ConnectTimeout`：在尝试建立连接时发生超时会引发此异常。
- `ReadTimeout`：如果服务器在指定时间内未发送任何数据，则会引发此异常。

```python
import requests

try:
    # 此请求将在尝试连接时超时。
    response = requests.get('https://github.com', timeout=0.001)
except requests.exceptions.ConnectTimeout as err:
    print(f'Connection timed out: {err}')
except requests.exceptions.ReadTimeout as err:
    print(f'Read timed out: {err}')
except requests.exceptions.Timeout as err:
    print(f'A timeout occurred: {err}')
```

## 超出重定向次数：`TooManyRedirects`

默认情况下，Requests 最多会跟踪 30 次重定向。如果超出此限制，它将引发 `TooManyRedirects` 异常。这可以防止您的应用程序陷入无限重定向循环。

```python
import requests

try:
    # httpbin.org/redirect/N 会进行 N 次重定向。默认限制为 30 次。
    response = requests.get('https://httpbin.org/redirect/35')
except requests.exceptions.TooManyRedirects as err:
    print(f'Too many redirects: {err}')
```

## 无效的 URL

如果您提供的 URL 格式不正确，Requests 将会引发异常，这通常在网络请求发出之前发生。最常见的异常是 `MissingSchema`，当您忘记添加 `http://` 或 `https://` 时会发生此异常。

```python
import requests

try:
    response = requests.get('google.com')
except requests.exceptions.MissingSchema as err:
    print(f'Invalid URL: {err}')
```

## 异常层级结构

了解异常的层级结构可以帮助您更有效地捕获它们。例如，捕获 `ConnectionError` 也会捕获 `ProxyError` 和 `SSLError`。

以下是主要异常类型的示意图：

```d2
direction: down

"IOError" -> "RequestException"

"RequestException" -> "HTTPError"
"RequestException" -> "ConnectionError"
"RequestException" -> "Timeout"
"RequestException" -> "URLRequired"
"RequestException" -> "TooManyRedirects"
"RequestException" -> "InvalidURL"

"ConnectionError" -> "ProxyError"
"ConnectionError" -> "SSLError"

"Timeout" -> "ReadTimeout"

"ConnectTimeout"
"ConnectionError" -> "ConnectTimeout"
"Timeout" -> "ConnectTimeout"

"InvalidURL" -> "MissingSchema"
```

## 后续步骤

现在您已经能够从容地处理错误，可以开始探索更复杂的场景了。请继续阅读[高级用法](./advanced-usage.md)指南，了解有关代理、SSL 验证、自定义适配器等更多内容。