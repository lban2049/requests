# 超时、重试和代理

控制请求的网络行为对于构建健壮的应用程序至关重要。Requests 允许你配置超时以防止无限期挂起，为暂时性故障设置自动重试，以及出于安全或访问目的通过代理路由流量。

## 超时

默认情况下，requests 请求没有超时设置，如果远程服务器无响应，请求可能会无限期挂起。你应该始终指定一个超时时间以防止这种情况发生。

大多数对外部服务器的请求都应设置超时。超时时间以秒为单位。

要设置超时，请使用 `timeout` 参数。你可以为连接和读取超时提供一个单一的浮点数值，或使用一个元组分别为它们进行设置。

- **连接超时**：允许客户端与服务器建立连接的时间。
- **读取超时**：建立连接后，允许客户端等待服务器响应的时间。

```python
import requests

# 为连接和读取设置单个超时时间
try:
    response = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.ReadTimeout:
    print('请求在等待服务器响应时超时。')

# 分别为连接和读取设置超时时间
try:
    response = requests.get('https://httpbin.org/delay/5', timeout=(2, 6)) # 2秒连接，6秒读取
except requests.exceptions.ConnectTimeout:
    print('与服务器的连接超时。')
except requests.exceptions.ReadTimeout:
    print('服务器在规定时间内未发送任何数据。')
```

如果超时时间设置为元组，其值将是 `(connect_timeout, read_timeout)`。如果提供的是单个浮点数，则它将同时应用于两者。

## 重试

默认情况下，Requests 不会重试失败的连接。要实现重试策略，你需要使用 `HTTPAdapter`。通过将配置好的 `HTTPAdapter` 挂载到 `Session` 对象上，你可以为通过该会话发出的请求指定重试次数。

这对于处理临时网络问题或间歇性服务器错误特别有用。

```python
import requests
from requests.adapters import HTTPAdapter

# 创建一个会话对象
s = requests.Session()

# 创建一个具有简单重试配置的适配器
# 这将对失败的 DNS 查询、套接字连接和连接超时最多重试 3 次。
a = HTTPAdapter(max_retries=3)

# 为特定协议将会话挂载到适配器上
s.mount('http://', a)
s.mount('https://', a)

try:
    response = s.get('https://httpbin.org/status/503')
    print(f'请求成功，状态码：{response.status_code}')
except requests.exceptions.RetryError as e:
    print(f'请求在多次重试后失败：{e}')
```

为了更精细地控制重试行为，你可以实例化 `urllib3.util.retry.Retry` 并将其传递给 `HTTPAdapter`。这允许你指定触发重试的条件，例如哪些 HTTP 状态码应触发重试、对哪些方法进行重试以及退避策略。

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

s = requests.Session()

retry_strategy = Retry(
    total=3,  # 重试总次数
    status_forcelist=[429, 500, 502, 503, 504],  # 需要重试的 HTTP 状态码
    backoff_factor=1  # 重试的延迟因子
)

adapter = HTTPAdapter(max_retries=retry_strategy)

s.mount('https://', adapter)
s.mount('http://', adapter)

try:
    response = s.get('https://api.example.com/unreliable_endpoint')
except requests.exceptions.RequestException as e:
    print(f'连接到端点失败：{e}')

```

## 代理

如果你需要通过代理服务器路由请求，可以使用 `proxies` 参数。该参数接受一个字典，用于将 URL 协议映射到代理的 URL。

```python
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('https://example.org', proxies=proxies)
```

你也可以使用环境变量 `HTTP_PROXY` 和 `HTTPS_PROXY` 来配置代理。如果未提供 `proxies` 参数，Requests 将自动使用这些环境变量。

### 代理身份验证

要使用基本 HTTP 代理身份验证，请在代理 URL 中包含用户名和密码：

```python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS 代理

Requests 也支持 SOCKS 代理。要使用它们，你需要安装 `PySocks` 库：

```bash
pip install pysocks
```

安装后，你可以在 `proxies` 字典中指定 SOCKS 代理。对于在客户端执行 DNS 解析的代理，请使用 `socks5`；要让代理进行 DNS 解析，请使用 `socks5h`。

```python
proxies = {
    'http': 'socks5://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://example.com', proxies=proxies)
```

### 绕过代理

要对特定主机或域名禁用代理，你可以使用 `NO_PROXY` 环境变量。它应该是一个由逗号分隔的主机名列表。

```bash
export NO_PROXY='localhost,127.0.0.1,example.com'
```

Requests 会遵循此变量，确保向指定域名的请求被直接发送，从而绕过任何已配置的代理。

---

掌握超时、重试和代理是构建健壮可靠的 HTTP 客户端的关键。有关保护连接的更多信息，请参阅下一节关于[SSL 证书验证](./advanced-usage-ssl-cert-verification.md)的内容。