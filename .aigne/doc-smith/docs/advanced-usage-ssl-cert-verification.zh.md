# SSL 证书验证

当您向 HTTPS URL 发出请求时，`requests` 在确保您的通信安全方面扮演着至关重要的角色。其中的一个关键部分是验证服务器的 SSL/TLS 证书。此过程确认您正在与您认为的目标服务器进行通信，保护您免受中间人攻击。

本指南涵盖了 `requests` 如何处理 SSL 验证、如何使用您自己的证书自定义此行为，以及如何使用客户端证书进行双向认证。

## 默认验证行为

默认情况下，`requests` 会为所有 HTTPS 请求验证 SSL 证书。为此，它使用由 `certifi` 包提供的一组受信任的证书颁发机构 (CA)。这与主流网络浏览器信任的 CA 集合相同。

当您发出请求时，`verify` 参数被隐式设置为 `True`。

```python SSL 验证默认开启 icon=logos:python
import requests

try:
    response = requests.get('https://example.com')
    print('Request was successful!')
except requests.exceptions.SSLError as e:
    print(f'An SSL error occurred: {e}')
```

如果服务器的证书有效且由受信任的 CA 签署，请求将继续进行。否则，`requests` 将引发 `SSLError`。

## 禁用 SSL 验证

在某些情况下，例如在本地开发或处理使用自签名证书的服务器时，您可能需要禁用验证。您可以通过将 `verify` 参数设置为 `False` 来实现此目的。

<x-card data-title="安全警告" data-icon="lucide:shield-alert">
禁用 SSL 证书验证会使您的应用程序容易受到中间人 (MitM) 攻击。任何交换的数据，包括敏感凭据，都可能被拦截和篡改。仅在受控的测试环境中禁用验证，切勿在生产环境中使用。
</x-card>

```python 禁用 SSL 验证 icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# 仅抑制 urllib3 发出的关于不安全请求的单个警告
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

response = requests.get('https://self-signed.badssl.com/', verify=False)
print(response.status_code)
```

当使用 `verify=False` 时，`requests` 会发出警告。建议仅在您完全了解安全隐患的情况下才抑制此警告。

## 自定义 CA 捆绑包

对于使用私有或自定义证书的服务器，一种比完全禁用验证更安全的方法是告诉 `requests` 信任特定的 CA 捆绑包。您可以通过将 CA 捆绑包文件 (`.pem`) 的路径传递给 `verify` 参数来实现此目的。

<x-field data-name="verify" data-type="boolean | string" data-default="True" data-desc="控制 SSL/TLS 证书验证。如果为 `True`，则使用默认的 CA 捆绑包。如果为 `False`，则禁用验证。如果为字符串，则必须是 CA 捆绑包文件或 CA 证书目录的路径。"></x-field>

```python 使用自定义 CA 捆绑包文件 icon=logos:python
import requests

try:
    response = requests.get('https://example.com', verify='/path/to/your/ca.pem')
    print('Request successful with custom CA!')
except requests.exceptions.SSLError as e:
    print(f'SSL verification failed: {e}')
```

如果 `verify` 路径指向一个目录，`requests` 将使用该目录中的 CA 证书。

此外，`requests` 会遵循 `REQUESTS_CA_BUNDLE` 和 `CURL_CA_BUNDLE` 环境变量。如果设置了这些变量，对于所有 `verify` 为 `True` 的请求，`requests` 将默认使用指定的 CA 捆绑包。

## 客户端证书

一些服务器要求客户端提供自己的证书进行身份验证，这个过程称为双向 TLS (mTLS)。您可以使用 `cert` 参数提供客户端证书。

<x-field data-name="cert" data-type="string | tuple" data-desc="客户端 SSL 证书的路径。可以是一个单独的文件（包含私钥和证书），也可以是一个元组 ('/path/to/cert.pem', '/path/to/key.pem')。"></x-field>

### 证书和密钥在同一个文件中

如果您的证书和私钥位于同一个 `.pem` 文件中，您可以将路径作为单个字符串传递。

```python 单文件客户端证书 icon=logos:python
import requests

response = requests.get('https://api.example.com/data', cert='/path/to/client.pem')
```

### 证书和密钥在不同文件中

如果您的证书和私钥位于不同的文件中，请传递一个元组，其中包含证书文件的路径和密钥文件的路径。

```python 分离文件客户端证书 icon=logos:python
import requests

cert_path = '/path/to/client.crt'
key_path = '/path/to/client.key'

response = requests.get('https://api.example.com/data', cert=(cert_path, key_path))
```

通过正确配置 SSL 验证和客户端证书，您可以确保应用程序的通信安全且经过适当的身份验证。

---

现在你已经掌握了 SSL 验证，可能希望探索其他高级网络功能。更多信息，请参阅 [超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md) 指南。