# 超时、重试和代理

构建稳健的应用程序需要处理多变的网络状况和特定的环境约束。Requests 允许你配置高级网络行为，例如设置超时以防止无限期挂起、自动重试失败的请求以及通过代理路由流量。

## 超时

默认情况下，requests 请求没有超时设置，这意味着如果服务器无响应，它们可能会无限期挂起。为防止这种情况，你应该始终为你的请求指定 `timeout`。

大多数对外部服务器的请求都应附带超时设置。你可以将超时设置为单个浮点数值，该值将同时应用于请求的 `connect` 和 `read` 阶段。

```python Timeout Example icon=logos:python
# 为整个请求设置 5 秒的超时
response = requests.get('https://api.github.com/events', timeout=5)
```

### 连接超时与读取超时

为了进行更精细的控制，你可以为连接和读取指定不同的超时时间。`connect` 超时是指等待与服务器建立连接的秒数。`read` 超时是指建立连接后，等待服务器发送响应的秒数。

要单独设置它们，可以向 `timeout` 参数传递一个元组。

```python Connect and Read Timeouts icon=logos:python
# 等待 3.5 秒连接，等待 10 秒接收数据
response = requests.get('https://api.github.com/events', timeout=(3.5, 10))
```

如果你想让请求无限期等待（不建议在大多数生产场景中使用），可以将 `None` 作为超时值传递。

## 重试

Requests 不会自动重试失败的请求。要实现重试机制，你需要使用 `Session` 对象，并挂载一个配置了重试策略的自定义 `HTTPAdapter`。

`HTTPAdapter` 的 `max_retries` 参数可以是一个整数，也可以是 `urllib3.util.retry.Retry` 的实例，以实现更高级的控制。

以下是如何配置会话，在发生连接相关错误时最多重试请求 3 次：

```python Basic Retries with HTTPAdapter icon=logos:python
import requests
from requests.adapters import HTTPAdapter

session = requests.Session()

# 配置适配器以重试 3 次
adapter = HTTPAdapter(max_retries=3)

# 为 HTTP 和 HTTPS 挂载适配器
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    response = session.get('http://a.server.that.does.not.exist')
except requests.exceptions.ConnectionError as e:
    print(f"重试后请求失败: {e}")
```

对于更高级的场景，例如在特定的 HTTP 状态码（如 `503 Service Unavailable`）上重试，你可以从 `urllib3` 传递一个 `Retry` 对象。

```python Advanced Retry Strategy icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()

retry_strategy = Retry(
    total=5,
    status_forcelist=[429, 500, 502, 503, 504], # 需要重试的状态码
    backoff_factor=1  # 重试之间的延迟因子
)

adapter = HTTPAdapter(max_retries=retry_strategy)

session.mount('https://', adapter)
session.mount('http://', adapter)

try:
    response = session.get('https://httpbin.org/status/503')
    print(f"请求成功，状态码: {response.status_code}")
except requests.exceptions.RetryError as e:
    print(f"多次重试后请求失败: {e}")

```

## 代理

如果你需要通过代理服务器路由请求，可以使用 `proxies` 参数。这在企业环境或访问有地理限制的内容时很常见。

`proxies` 参数接受一个字典，将 URL 协议（例如 `http`、`https`）映射到代理的 URL。

```python Using an HTTP Proxy icon=logos:python
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'https://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### 认证

如果你的代理需要认证，可以将其包含在代理 URL 中：

```python Proxy with Authentication icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://example.org', proxies=proxies)
```

### SOCKS 代理

Requests 也支持 SOCKS 代理。要使用它们，首先需要安装 `PySocks` 库：

```bash
pip install PySocks
```

安装后，你可以在 `proxies` 字典中使用 `socks5` 或 `socks5h` 协议来指定 SOCKS 代理。

```python SOCKS Proxy icon=logos:python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```

### 环境变量

Requests 会自动检测并使用在你的环境变量（`HTTP_PROXY` 和 `HTTPS_PROXY`）中配置的代理。你可以通过将 `Session` 对象的 `trust_env` 属性设置为 `False` 来禁用此行为。

你还可以使用 `NO_PROXY` 环境变量来指定应绕过代理的主机。


通过这些配置，你可以构建更具弹性和适应性的 HTTP 客户端。如需更高级的自定义，你可能需要探索 SSL 证书处理。

接下来，学习如何管理 [SSL 证书验证](./advanced-usage-ssl-cert-verification.md)。