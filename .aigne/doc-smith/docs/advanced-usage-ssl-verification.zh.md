# SSL 证书验证

Requests 默认会验证 HTTPS 请求的 SSL 证书，其功能与网络浏览器类似。这是一项关键的安全功能，有助于防范中间人攻击。默认情况下，Requests 使用来自 `certifi` 包的一组受信任的证书颁发机构 (CA)，该包提供了 Mozilla 精心整理的证书集合。

本节介绍如何管理 SSL 验证，包括（谨慎地）禁用验证、使用自定义 CA 证书包，以及为双向 TLS 身份验证提供客户端证书。

## 默认行为

当你向 `https://` URL 发出请求时，Requests 会自动执行证书验证。你无需进行任何特殊操作。

```python 默认验证
import requests

try:
    response = requests.get('https://api.github.com')
    print('请求成功！')
except requests.exceptions.SSLError as e:
    print(f'SSL 错误: {e}')
```

如果服务器的证书有效且受 `certifi` 证书包中某个 CA 的信任，请求将会成功。否则，将引发 `requests.exceptions.SSLError` 异常。

## 禁用 SSL 验证

在某些情况下，例如在处理使用自签名证书的开发服务器时，你可能需要禁用验证。为此，可以将 `verify` 参数设置为 `False`。

<x-card data-title="安全警告" data-icon="lucide:alert-triangle">
**不建议**在生产环境中禁用证书验证。这会将你的应用程序暴露在安全漏洞（包括中间人攻击）之下，因为它允许与身份无法确认的服务器进行通信。请谨慎操作，仅在完全理解相关风险时才继续。
</x-card>

```python 禁用验证 icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# 仅抑制来自 urllib3 的关于不安全请求的警告
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

try:
    response = requests.get('https://self-signed.badssl.com/', verify=False)
    print('请求成功，但未进行 SSL 验证。')
    print(f'状态码: {response.status_code}')
except requests.exceptions.RequestException as e:
    print(f'发生错误: {e}')
```

当你设置 `verify=False` 时，Requests 仍会执行 TLS 握手，但不会验证服务器的证书，从而忽略任何 SSL 错误。

## 自定义 CA 证书包

对于使用自有证书颁发机构的企业或私有网络，你可以通过向 `verify` 参数传递证书文件的路径，来让 Requests 信任特定的 CA 证书包。

证书文件应为 PEM 格式，并且可以包含多个 CA 证书。

```python 使用自定义 CA 证书包 icon=logos:python
import requests

ca_bundle_path = '/path/to/your/custom-ca.pem'

try:
    response = requests.get('https://internal.mycompany.com', verify=ca_bundle_path)
    print('使用自定义 CA 成功验证。')
except requests.exceptions.SSLError as e:
    print(f'使用自定义 CA 验证失败: {e}')
```

如果你有一个包含多个 CA 证书的目录而不是单个文件，也可以提供该目录的路径。

## 客户端证书

有些服务要求客户端提供自己的证书进行身份验证，这一过程称为双向 TLS (mTLS)。你可以使用 `cert` 参数来提供客户端证书。

`cert` 参数可以是以下两种形式之一：
1.  一个字符串，包含同时存有客户端证书和私钥的单个文件的路径。
2.  一个元组，分别包含证书文件和密钥文件的路径。

### 证书和密钥在单个文件中

```python 客户端证书 (单个文件) icon=logos:python
import requests

cert_file_path = '/path/to/client.pem'

response = requests.get(
    'https://api.secure.service/resource',
    cert=cert_file_path
)
```

### 证书和密钥在单独的文件中

```python 客户端证书 (单独的文件) icon=logos:python
import requests

cert_path = '/path/to/client.crt'
key_path = '/path/to/client.key'

response = requests.get(
    'https://api.secure.service/resource',
    cert=(cert_path, key_path)
)
```

请注意，私钥不能被加密。

## 为会话配置验证

如果你需要对多个请求应用相同的验证设置，配置 `Session` 对象会更高效。对于任何非简单的应用程序，这都是推荐的方法。

```python 使用自定义验证的会话 icon=logos:python
import requests

# 配置一个带有自定义 CA 和客户端证书的会话
s = requests.Session()
s.verify = '/path/to/custom-ca.pem'
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# 使用此会话发出的所有请求都将使用这些设置
response1 = s.get('https://api.secure.service/resource1')
response2 = s.get('https://api.secure.service/resource2')
```

通过在 `Session` 对象上设置 `verify` 和 `cert`，你可以避免为每次调用重复配置，并能受益于连接池。有关更多详细信息，请参阅关于[会话对象](./advanced-usage-session-objects.md)的文档。

接下来，学习如何通过配置[超时](./advanced-usage-timeouts.md)来防止请求无限期挂起。