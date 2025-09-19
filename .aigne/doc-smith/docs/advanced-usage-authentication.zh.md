# 身份验证

许多 Web 服务需要身份验证，Requests 为此提供了几种即用型方法。本指南涵盖了最常见的身份验证方案，包括基本身份验证、摘要身份验证和自定义身份验证机制。

## 基本身份验证

基本身份验证是一种广泛使用且简单直接的方法，它将用户名和密码随请求一起发送。Requests 使这一过程变得极其简单。

你可以通过向 `auth` 参数传递一个 `HTTPBasicAuth` 实例来提供凭据。

```python Basic Authentication Example icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

为方便起见，Requests 还提供了一种简写方式，即向 `auth` 参数传递一个 `(username, password)` 的二元元组。

```python Shorthand Basic Authentication icon=logos:python
import requests

response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

在后台，Requests 会使用经过 Base64 编码的凭据构建相应的 `Authorization` 标头。

## 摘要身份验证

摘要身份验证通过使用质询-响应机制，避免了以明文形式发送密码，从而提供了比基本身份验证更安全的替代方案。虽然其过程更为复杂，但在 Requests 中使用它同样简单。

只需使用 `HTTPDigestAuth` 类并将其传递给 `auth` 参数即可。Requests 会为你处理整个多步身份验证流程。

```python Digest Authentication Example icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'
response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

## 代理身份验证

如果你需要向中间代理服务器进行身份验证，可以使用 `HTTPProxyAuth` 类。它的工作方式与 `HTTPBasicAuth` 类似，但设置的是 `Proxy-Authorization` 标头。

这通常与 `proxies` 参数结合使用。

```python Proxy Authentication Example icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# 注意：这是一个概念性示例。请替换为你的实际代理详情。
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

response = requests.get('https://www.example.com', proxies=proxies, auth=proxy_auth)

print(f"Status Code: {response.status_code}")
```

## 自定义身份验证

如果你需要实现一个未内置的身份验证方案，Requests 允许你创建自己的方案。任何自定义身份验证处理器都必须是一个可调用对象，它接受一个 `Request` 对象作为其唯一参数，并返回修改后的 `Request` 对象。

创建自定义身份验证处理器最简单的方法是继承 `requests.auth.AuthBase` 并实现 `__call__` 方法。

下面是一个自定义身份验证处理器的示例，它将一个令牌添加到一个自定义标头中，这是 API 身份验证的常见模式。

```python Custom Token Authentication icon=logos:python
import requests

class TokenAuth(requests.auth.AuthBase):
    """将自定义令牌附加到 X-API-Token 标头。"""
    def __init__(self, token):
        # 在此处设置任何与身份验证相关的数据
        self.token = token

    def __call__(self, r):
        # 修改请求对象以应用身份验证
        r.headers['X-API-Token'] = f'{self.token}'
        return r


response = requests.get('https://httpbin.org/headers', auth=TokenAuth('my-secret-api-token'))

print(response.json()['headers']['X-Api-Token'])
# 'my-secret-api-token'
```

这个灵活的系统使你能够与几乎任何身份验证方案（如 OAuth1、OAuth2、Hawk 等）进行集成。

现在你已经了解了身份验证，可能想学习如何通过中介路由你的请求。请继续阅读[代理](./advanced-usage-proxies.md)部分以了解更多信息。