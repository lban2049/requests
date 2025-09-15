# 认证

许多 Web 服务需要认证才能访问其数据。Requests 提供了多种即用型认证方法和一个用于创建自定义认证方案的灵活系统。认证通过 `auth` 参数传递给请求方法。

本指南涵盖了最常见的内置认证类型。要深入了解更复杂的场景，你可能还需要参考[高级用法](./advanced-usage.md)部分。

## 基本认证

基本认证是一种广泛使用的、直接的方法，它依赖于在请求中发送用户名和密码。虽然它很简单，但重要的是仅在 HTTPS 上使用它，以确保凭据被加密。

提供基本认证最便捷的方式是将一个 `(username, password)` 元组传递给 `auth` 参数。

```python Basic Auth with a Tuple icon=logos:python
import requests

# 使用 httpbin 的 basic-auth 端点，该端点需要 'user' 和 'pass' 作为凭据。
response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# 成功的认证将返回 200 OK。
if response.status_code == 200:
    print(f'Response JSON: {response.json()}')
else:
    print(f'Authentication failed. Reason: {response.reason}')

```

实际上，这个元组是创建 `HTTPBasicAuth` 对象的快捷方式。你也可以直接使用该类，以获得更明确的方法，其行为完全相同。

```python Basic Auth with HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
```

## 摘要认证

摘要认证是基本认证的一种更安全的挑战-响应替代方案，因为它不会以明文形式发送密码。Requests 为你无缝处理了此机制的复杂性。

要使用摘要认证，请导入 `HTTPDigestAuth` 并将其一个实例传递给 `auth` 参数。

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

# httpbin 的 digest-auth 端点
url = 'https://httpbin.org/digest-auth/qop/user/pass'
response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
if response.ok:
    print(f'Response JSON: {response.json()}')
```

## 代理认证

如果你需要通过中间代理服务器进行认证，可以使用 `HTTPProxyAuth`。其工作方式类似于 `HTTPBasicAuth`，但设置的是 `Proxy-Authorization` 标头，而不是 `Authorization` 标头。

此认证方法应与 `proxies` 参数结合使用，该参数在[超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md)部分有更详细的介绍。

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# 这是一个概念性示例。你需要一个正在运行且需要认证的代理。
proxies = {
   "http": "http://proxy.example.com:8080",
   "https": "https://proxy.example.com:8080",
}

# 此 auth 对象为代理服务器提供凭据。
proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# 'auth' 参数用于代理，而不是目标服务器。
# 注意：如果没有真实配置的代理，此操作将会失败。
try:
    response = requests.get("https://httpbin.org/get", proxies=proxies, auth=proxy_auth)
    print(response.status_code)
except requests.exceptions.ProxyError as e:
    print(f'Failed to connect to proxy: {e}')
```

## 其他认证方案

Requests 具有一个模块化的认证系统。虽然它自带了常见的方案，但其他认证类型（如 OAuth1 和 OAuth2）由第三方库提供。

创建自己的认证机制非常简单。你可以创建一个继承自 `requests.auth.AuthBase` 的类，或者直接创建一个可调用对象，该对象接受一个 `Request` 对象并在修改后返回它。这对于实现自定义方案（例如基于令牌的认证）非常有用。

以下是一个用于简单基于令牌 API 的自定义认证类的示例。

```python Custom Authentication icon=logos:python
import requests
from requests.auth import AuthBase

class TokenAuth(AuthBase):
    """将自定义令牌附加到 Authorization 标头。"""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # 通过添加 Authorization 标头来修改请求 `r`。
        r.headers['Authorization'] = f'Token {self.token}'
        return r

# 使用自定义认证处理器
response = requests.get('https://httpbin.org/headers', auth=TokenAuth('my-secret-token-123'))

print(response.json())
```

---

现在你已经保护了你的请求，但可能会遇到问题。让我们在[错误处理](./user-guide-error-handling.md)部分学习如何处理这些问题。