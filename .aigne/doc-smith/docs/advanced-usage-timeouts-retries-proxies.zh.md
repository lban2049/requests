# 超时、重试和代理

构建稳健的应用程序需要优雅地处理不可靠的网络状况。Requests 提供了强大且易于使用的机制，用于配置超时、重试失败的连接以及通过代理路由流量。本指南将引导你了解这些高级功能，以帮助你控制 HTTP 请求的网络行为。

## 超时

你可以配置 Requests 在指定的秒数后停止等待响应。这是一个至关重要的功能，可以防止你的应用程序在缓慢或无响应的网络连接上无限期挂起。

大多数对外部服务器的请求都应附带超时设置。如果没有超时设置，你的代码可能会挂起数分钟甚至更长时间。

### 基本超时

要设置超时，请使用 `timeout` 参数。如果服务器在指定时间内未发送任何数据，则会引发 `requests.exceptions.Timeout` 异常。

```python 单个请求的超时设置 icon=logos:python
import requests

try:
    response = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('请求超时')
else:
    print('请求未超时')
```

### 连接和读取超时

`timeout` 的值也可以是一个元组，用于为连接和读取设置不同的超时时间。

<x-field data-name="timeout" data-type="float or tuple" data-desc="放弃前等待服务器响应的时间。">
  <x-field data-name="connect" data-type="float" data-desc="与服务器建立连接的超时时间。"></x-field>
  <x-field data-name="read" data-type="float" data-desc="等待服务器发送响应的超时时间。"></x-field>
</x-field>

如果你指定单个浮点数，该值将同时应用于 `connect` 和 `read` 超时。

```python 连接和读取超时 icon=logos:python
import requests

# 等待 3.05 秒连接，等待 27 秒接收数据
try:
    response = requests.get('https://httpbin.org/delay/5', timeout=(3.05, 27))
except requests.exceptions.ConnectTimeout:
    print('发生连接超时')
except requests.exceptions.ReadTimeout:
    print('发生读取超时')
```

## 重试

默认情况下，Requests 不会自动重试失败的请求。要实现重试机制，你需要使用 `Session` 对象，并挂载一个配置了重试策略的 `HTTPAdapter`。

`HTTPAdapter` 上的 `max_retries` 参数仅适用于连接级别的故障，例如 DNS 错误、套接字连接问题和连接超时。它不会重试已成功将数据发送到服务器的请求。

### 配置重试

要配置重试，你需要创建一个 `HTTPAdapter` 实例，指定最大重试次数，然后将其挂载到 `Session` 上以用于特定协议（例如 `http://` 或 `https://`）。

```python 使用 Adapter 配置重试 icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# 创建一个 session
session = requests.Session()

# 创建一个带有重试策略的 adapter
# 这将最多重试 3 次失败的连接
adapter = HTTPAdapter(max_retries=3)

# 将 adapter 挂载到 session 上，用于 HTTP 和 HTTPS
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    # 使用 session 发出请求
    response = session.get('http://a.very.nonexistent.domain.com')
except requests.exceptions.ConnectionError as e:
    print(f'多次重试后连接失败: {e}')
```

为了实现更精细的控制，你可以导入并传递一个已配置的 `urllib3.util.retry.Retry` 对象给 `max_retries`，而不是一个整数。

## 代理

如果你需要通过代理服务器路由请求，可以使用 `proxies` 参数进行配置。

### 基本代理用法

`proxies` 参数是一个字典，用于将 URL 协议映射到代理的 URL。

```python 使用 HTTP 代理 icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

response = requests.get('https://httpbin.org/get', proxies=proxies)
print(response.json())
```

### 带身份验证的代理

要使用基本 HTTP 代理身份验证，请在代理 URL 中包含用户名和密码：

```python 带身份验证的代理 icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS 代理

要使用 SOCKS 代理，你需要安装一个额外的包：

```bash
pip install requests[socks]
```

安装后，你可以在代理 URL 中使用 `socks5://` 或 `socks5h://`。`socks5h` 表示 DNS 解析应在代理服务器端进行。

```python SOCKS 代理 icon=logos:python
proxies = {
    'http': 'socks5h://user:password@host:port',
    'https': 'socks5h://user:password@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

### 环境变量

Requests 会自动从标准的 `HTTP_PROXY`、`HTTPS_PROXY` 和 `NO_PROXY` 环境变量中读取并使用代理配置。你可以在 `Session` 对象上通过设置 `trust_env=False` 来禁用此行为。

`NO_PROXY` 环境变量可以设置为一个由逗号分隔的主机或 IP 地址列表，这些地址应绕过代理。

```python 禁用环境代理 icon=logos:python
s = requests.Session()
s.trust_env = False

# 此请求将不会使用环境变量中的代理
s.get('https://httpbin.org/get')
```

---

通过掌握超时、重试和代理，你可以显著提高应用程序的可靠性和灵活性。要了解更高级的网络控制，请继续阅读下一节关于 [SSL 证书验证](./advanced-usage-ssl-cert-verification.md) 的内容。