# SSL 证书验证

Requests 默认会为 HTTPS 请求验证 SSL 证书以确保安全通信，这与 Web 浏览器的运作方式类似。如果证书无法验证，Requests 将会抛出 `SSLError`。此行为是一项关键的安全功能，可防止中间人攻击。

默认情况下，Requests 使用由 `certifi` 包提供的 CA 捆绑包。本节详细介绍如何针对不同场景（例如使用私有 CA 或提供客户端证书）自定义此行为。

## 自定义 CA 证书

你可以通过向 `verify` 参数传递你自己的 CA 捆绑包文件路径或证书目录路径来覆盖默认的受信任 CA 捆绑包。

这在与使用自签名证书的内部服务器或服务交互时特别有用。

```python
import requests

# 使用自定义 CA 捆绑包文件
response = requests.get('https://some-internal-site.com', verify='/path/to/your/ca.pem')

# 使用 CA 证书目录
response = requests.get('https://some-internal-site.com', verify='/path/to/certs/')
```

如果 `verify` 设置为目录路径，Requests 将从该目录加载证书。

### 使用环境变量

或者，你可以通过设置 `REQUESTS_CA_BUNDLE` 或 `CURL_CA_BUNDLE` 环境变量来为所有请求配置自定义 CA 捆绑包：

```bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

设置此环境变量后，Requests 将其用作默认的 CA 捆绑包，因此你无需在代码中指定 `verify` 参数。

## 客户端证书

一些服务器要求客户端提供证书进行身份验证，这个过程称为双向 TLS (mTLS)。你可以使用 `cert` 参数提供客户端证书。

`cert` 参数可以是一个包含私钥和证书的单一文件路径，也可以是一个包含证书文件和密钥文件路径的元组。

```python
import requests

# 如果你的私钥包含在证书文件中
cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.some-secure-service.com', cert=cert_file_path)

# 如果你的证书和私钥位于不同的文件中
cert_file_path = '/path/to/client.crt'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.some-secure-service.com', cert=(cert_file_path, key_file_path))
```

如果指定的证书或密钥文件不存在，Requests 将会抛出 `OSError`。

## 禁用验证

在某些情况下，例如本地开发或针对使用临时自签名证书的服务器进行测试时，你可能需要禁用 SSL 验证。可以通过设置 `verify=False` 来实现。

> **警告：** 禁用 SSL 证书验证会使你的应用程序容易受到中间人 (MitM) 攻击。它会绕过对服务器身份的验证，这意味着你发送的任何数据都可能被截获。这只应在受控的非生产环境中使用。

```python
import requests

# 这将禁用证书验证，并可能导致安全警告。
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

当 `verify=False` 时，Requests 会接受服务器提供的任何 TLS 证书，并忽略主机名不匹配和证书过期的情况。

## 会话中的 SSL 验证

如果你需要对多个请求应用相同的 SSL 配置，可以在 `Session` 对象上设置 `verify` 和 `cert` 属性。这样可以避免为每个请求调用传递相同的参数。

```python
import requests

s = requests.Session()

# 为此会话中的所有请求设置自定义 CA 捆绑包
s.verify = '/path/to/ca.pem'

# 为此会话中的所有请求设置客户端证书
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# 这些请求将使用会话的 SSL 配置
response1 = s.get('https://api.example.com/endpoint1')
response2 = s.get('https://api.example.com/endpoint2')
```

直接传递给请求方法的任何参数（例如 `s.get(url, verify=False)`）都将覆盖该特定请求的会话设置。

---

要了解更多关于网络行为的高级自定义，例如创建自定义连接逻辑或处理特定的身份验证方案，请继续阅读下一节。

<x-card data-title="自定义适配器和钩子" data-icon="lucide:git-merge" data-href="/advanced-usage/adapters-and-hooks" data-cta="阅读更多">
  学习如何通过创建自定义传输适配器和使用事件钩子系统来扩展 Requests 的功能。
</x-card>