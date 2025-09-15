# SSL 证书验证

当你向 `https` URL 发出请求时，Requests 在确保通信安全方面扮演着至关重要的角色。它通过验证服务器的 SSL/TLS 证书来实现这一点，这有助于防止中间人攻击。本指南将介绍 Requests 如何处理 SSL 验证，以及如何针对不同场景自定义其行为。

## 默认验证行为

默认情况下，Requests 会为所有 HTTPS 请求验证 SSL 证书。为此，它需要一组受信任的证书颁发机构 (CA)。Requests 使用 `certifi` 包提供的 CA 证书包，这是一个由受信任 CA 的根证书组成的精选集合。

当你发出一个简单的 HTTPS 请求时，此验证会自动进行：

```python 发送一个经过验证的 HTTPS 请求 icon=logos:python
import requests

try:
    response = requests.get('https://api.github.com')
    print('Request was successful!')
except requests.exceptions.SSLError as e:
    print(f'An SSL error occurred: {e}')
```

如果服务器的证书有效且由受信任的 CA 签署，请求将成功。否则，Requests 将引发 `SSLError`。

## 禁用证书验证

在某些情况下，例如当你使用本地开发服务器或使用自签名证书的内部服务时，可能需要绕过 SSL 验证。你可以通过将 `verify` 参数设置为 `False` 来实现这一点。

<x-card data-title="安全警告" data-icon="lucide:shield-alert">
禁用 SSL 验证会使你的应用程序容易受到中间人 (MitM) 攻击。这意味着你的连接不安全，任何交换的数据都可能被拦截和篡改。仅在受信任的网络上进行测试时使用 `verify=False`。
</x-card>

```python 禁用验证 icon=logos:python
import requests
from requests.packages.urllib3.exceptions import InsecureRequestWarning

# 可选：禁用不安全请求警告
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

try:
    response = requests.get('https://self-signed.badssl.com/', verify=False)
    print('Request completed without SSL verification.')
    print(f'Status Code: {response.status_code}')
except Exception as e:
    print(f'An error occurred: {e}')
```

当你设置 `verify=False` 时，Requests 会为每个不安全的请求发出警告。在确认风险后，通常的做法是使用 `requests.packages.urllib3.disable_warnings()` 来禁用这些警告。

## 使用自定义 CA 证书包

对于内部或私有系统，一种比禁用验证更安全的方法是告知 Requests 信任特定的 CA 证书包。你可以通过将 CA 证书包文件（`.pem` 格式）的路径传递给 `verify` 参数来实现。

如果你要连接的服务器使用的是由你组织内部 CA 颁发的证书，这将非常有用。

```python 指定本地 CA 证书包 icon=logos:python
import requests

ca_bundle_path = '/path/to/your/corporate-ca.pem'

try:
    response = requests.get('https://internal.mycompany.com', verify=ca_bundle_path)
    print('Successfully verified the server certificate using a custom CA.')
except requests.exceptions.SSLError as e:
    print(f'SSL verification failed: {e}')

```

如果你的 CA 证书包的结构是包含多个独立证书文件的目录，你也可以提供该目录的路径。

对于持久化配置，Requests 也支持 `REQUESTS_CA_BUNDLE` 和 `CURL_CA_BUNDLE` 环境变量。

```bash 通过环境变量配置 icon=mdi:bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## 客户端证书

一些服务器要求客户端提供自己的证书进行身份验证，这个过程称为双向 TLS (mTLS)。你可以使用 `cert` 参数提供客户端证书。

你可以在单个文件中提供证书和私钥：

```python 客户端证书和密钥在同一个文件中 icon=logos:python
import requests

cert_file_path = '/path/to/your/client.pem'

response = requests.get(
    'https://api.service.com/secure-data',
    cert=cert_file_path
)
```

或者，如果你的证书和私钥位于不同的文件中，你可以传递一个包含路径的元组：

```python 客户端证书和密钥在不同的文件中 icon=logos:python
import requests

cert_path = '/path/to/your/client.crt'
key_path = '/path/to/your/client.key'

response = requests.get(
    'https://api.service.com/secure-data',
    cert=(cert_path, key_path)
)
```

如果私钥是加密的，系统将在运行时提示你输入密码。

通过正确管理 SSL 验证，你可以确保应用程序安全通信，同时保留连接各种 HTTPS 服务的灵活性。

---

现在你已经了解了如何管理 SSL 证书，你可能想学习如何在多个请求之间持久化这些设置。请查看 [Session 对象](./user-guide-session-objects.md) 指南以了解更多信息。