# 超时、重试和代理

控制请求的网络行为对于构建弹性应用程序至关重要。Requests 允许你配置超时以防止无限期挂起，为暂时性故障设置自动重试，以及出于安全或访问目的通过代理路由流量。

## 超时

默认情况下，requests 请求没有超时设置，如果远程服务器无响应，请求可能会无限期挂起。你应该始终指定一个超时时间来防止这种情况发生。

大多数对外部服务器的请求都应设置超时，单位为秒。要设置超时，请使用 `timeout` 参数。你可以提供一个浮点数同时设置连接和读取超时，也可以提供一个元组来分别设置它们。

*   **连接超时**：允许客户端与服务器建立连接的时间。
*   **读取超时**：建立连接后，允许客户端等待服务器响应的时间。

```python 设置超时 icon=logos:python
import requests

# 为连接和读取设置一个统一的超时时间（5 秒）
try:
    response = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.ReadTimeout:
    print('请求在等待服务器响应时超时。')

# 分别设置连接和读取超时
try:
    # 2 秒连接超时，6 秒读取超时
    response = requests.get('https://httpbin.org/delay/10', timeout=(2, 6))
except requests.exceptions.ConnectTimeout:
    print('连接服务器超时。')
except requests.exceptions.ReadTimeout:
    print('服务器在规定时间内未发送任何数据。')
```

如果超时设置为元组，其值将是 `(connect_timeout, read_timeout)`。如果提供的是单个浮点数，它将同时应用于连接和读取超时。

## 重试

默认情况下，Requests 不会重试失败的连接。要实现重试策略，你需要使用 `requests.adapters.HTTPAdapter`。通过将一个配置好的 `HTTPAdapter` 挂载到 `requests.Session` 对象上，你可以为通过该会话发出的请求指定重试行为。

这对于处理临时网络问题或间歇性服务器错误特别有用。

```python 简单重试配置 icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# 创建一个会话对象
s = requests.Session()

# 创建一个具有简单重试配置的适配器。
# 这将对失败的 DNS 查询、套接字连接和
# 连接超时进行最多 3 次重试。
a = HTTPAdapter(max_retries=3)

# 将适配器挂载到会话上，以处理 HTTP 和 HTTPS 请求
s.mount('http://', a)
s.mount('https://', a)

try:
    # 对 503 端点的请求将重试 3 次
    response = s.get('https://httpbin.org/status/503')
    print(f'请求成功，状态码: {response.status_code}')
except requests.exceptions.RetryError as e:
    print(f'多次重试后请求失败: {e}')
```

为了实现更精细的控制，你可以实例化 `urllib3.util.retry.Retry` 并将其传递给 `HTTPAdapter`。这允许你指定触发重试的条件，例如哪些 HTTP 状态码应该触发重试、对哪些方法进行重试以及退避策略。

```python 高级重试策略 icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

s = requests.Session()

retry_strategy = Retry(
    total=3,  # 重试总次数
    status_forcelist=[429, 500, 502, 503, 504],  # 需要重试的 HTTP 状态码
    backoff_factor=1  # 重试的延迟因子（例如，1s, 2s, 4s）
)

adapter = HTTPAdapter(max_retries=retry_strategy)

s.mount('https://', adapter)
s.mount('http://', adapter)

try:
    response = s.get('https://api.example.com/unreliable_endpoint')
except requests.exceptions.RequestException as e:
    print(f'连接端点失败: {e}')
```

## 代理

如果你需要通过代理服务器路由请求，可以使用 `proxies` 参数。该参数接受一个字典，用于将 URL 协议映射到代理服务器的 URL。

```python 使用代理 icon=logos:python
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('https://example.org', proxies=proxies)
```

你也可以使用 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量来配置代理。如果未提供 `proxies` 参数，Requests 会自动使用这些环境变量。

### 代理身份验证

要使用 HTTP 基本代理身份验证，请在代理 URL 中包含用户名和密码：

```python 带身份验证的代理 icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
    'https': 'https://user:password@10.10.1.10:1080/',
}

requests.get('https://example.org', proxies=proxies)
```

### SOCKS 代理

Requests 也支持 SOCKS 代理，但这需要先安装 `PySocks` 库。

```bash 安装 SOCKS 支持 icon=lucide:terminal
pip install pysocks
```

安装后，你就可以指定 SOCKS 代理。对于在客户端执行 DNS 解析的代理，使用 `socks5`；若要让代理服务器解析 DNS，则使用 `socks5h`。

```python 使用 SOCKS 代理 icon=logos:python
proxies = {
    'http': 'socks5://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://example.com', proxies=proxies)
```

### 绕过代理

要为特定主机或域禁用代理，可以设置 `NO_PROXY` 环境变量。该变量应为一个以逗号分隔的主机名、域名或 IP 地址（包括 CIDR 表示法）列表。

```bash 通过环境变量绕过代理 icon=lucide:terminal
export NO_PROXY='localhost,127.0.0.1,example.com,192.168.0.0/24'
```

Requests 会遵循此变量，确保发往指定目标的请求直接发送，绕过所有已配置的代理。

---

掌握超时、重试和代理是构建稳健可靠的 HTTP 客户端的关键。要了解更多关于保护连接安全的信息，请参阅下一节关于[SSL 证书验证](./advanced-usage-ssl-cert-verification.md)的内容。
