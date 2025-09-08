# SSL 证书验证

默认情况下，Requests 会为 HTTPS 请求验证 SSL 证书以确保安全通信，就像网页浏览器一样。如果无法验证证书，Requests 将引发 `SSLError`。这是一项重要的安全功能，可以保护您的应用程序免受中间人攻击。

默认情况下，Requests 使用 `certifi` 包中的一组受信任的根证书。本节详细介绍如何自定义此行为，例如使用私有证书颁发机构 (CA) 或提供客户端证书进行身份验证。

## 自定义 CA 证书

您可以通过向 `verify` 参数传递您自己的 CA 包文件路径或证书目录路径来覆盖默认的可信 CA 包。

当与使用自签名或私有颁发证书的内部服务交互时，这特别有用。

```python 使用自定义 CA 包文件 icon=logos:python
import requests

response = requests.get('https://some-internal-site.com', verify='/path/to/your/ca.pem')
```

如果您有一个证书目录，您可以传递该目录的路径：

```python 使用 CA 证书目录 icon=logos:python
import requests

response = requests.get('https://some-internal-site.com', verify='/path/to/certs/')
```

### 使用环境变量

若要进行更持久的配置，您可以设置 `REQUESTS_CA_BUNDLE` 或 `CURL_CA_BUNDLE` 环境变量。Requests 将自动为所有请求使用指定的 CA 包，因此您无需在代码中传递 `verify` 参数。

```bash 设置环境变量 icon=mdi:bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## 客户端证书

某些服务器要求客户端提供证书进行身份验证，这一过程称为双向 TLS (mTLS)。您可以使用 `cert` 参数提供客户端证书。

`cert` 参数可以是一个同时包含私钥和证书的单个文件的路径，也可以是一个分别包含证书文件和密钥文件路径的元组。

```python 证书和密钥在同一个文件中 icon=logos:python
import requests

# 如果您的私钥包含在证书文件中
cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.some-secure-service.com', cert=cert_file_path)
```

```python 证书和密钥在不同文件中 icon=logos:python
import requests

# 如果您的证书和私钥在不同的文件中
cert_file_path = '/path/to/client.crt'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.some-secure-service.com', cert=(cert_file_path, key_file_path))
```

如果指定的证书或密钥文件在给定路径下不存在，Requests 将引发 `OSError`。

## 禁用验证

在某些情况下，例如在本地开发或针对使用临时自签名证书的服务器进行测试时，您可能需要禁用 SSL 验证。您可以通过设置 `verify=False` 来实现这一点。

> **警告：**禁用 SSL 证书验证会使您的应用程序面临严重的安全风险，包括中间人 (MitM) 攻击。它会绕过对服务器身份的验证，这意味着您发送的任何数据都可能被截获。此功能只应在受控的非生产环境中使用。

```python 禁用 SSL 验证 icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# 仅抑制 urllib3 发出的关于不安全请求的单个警告。
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

# 这将禁用证书验证。
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

当 `verify=False` 时，Requests 将接受服务器提供的任何 TLS 证书，并忽略主机名不匹配或证书过期的情况。

## 会话中的 SSL 验证

如果您需要在多个请求中应用相同的 SSL 配置，可以在 `Session` 对象上设置 `verify` 和 `cert` 属性。这种方法避免了为每个请求调用传递相同的参数，并可以通过连接复用提高性能。

```python 使用 SSL 设置配置会话 icon=logos:python
import requests

s = requests.Session()

# 为此会话中的所有请求设置自定义 CA 包
s.verify = '/path/to/ca.pem'

# 为此会话中的所有请求设置客户端证书
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# 这些请求将使用会话的 SSL 配置
response1 = s.get('https://api.example.com/endpoint1')
response2 = s.get('https://api.example.com/endpoint2')
```

直接传递给请求方法的任何参数（例如 `s.get(url, verify=False)`）将覆盖该特定请求的会话设置。

---

有关更高级的网络行为自定义，例如创建自定义连接逻辑或处理特定身份验证方案，请继续阅读下一节。

<x-card data-title="自定义适配器和钩子" data-icon="lucide:git-merge" data-href="/advanced-usage/adapters-and-hooks" data-cta="阅读更多">
  了解如何通过创建自定义传输适配器和使用事件钩子系统来扩展 Requests 的功能。
</x-card>