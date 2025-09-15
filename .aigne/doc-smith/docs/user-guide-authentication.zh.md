# 身份验证

许多 Web 服务需要身份验证才能访问其资源。Requests 提供了几种内置方法来处理身份验证，从而可以轻松保护您的 HTTP 请求。本指南涵盖了最常见的身份验证方案，包括基本 (Basic)、摘要 (Digest) 和自定义身份验证机制。

## 基本身份验证

基本身份验证是一种广泛使用且简单直接的方法。要使用它，您可以通过 `auth` 参数提供一个包含用户名和密码的元组。

```python Basic Authentication with a Tuple icon=logos:python
import requests

response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f'Status Code: {response.status_code}')
print(response.json())
```

这是一种方便的简写方式。在内部，Requests 会创建一个 `HTTPBasicAuth` 对象。您也可以自己构造这个对象，如果您需要在多个请求中或在 `Session` 对象中重用相同的身份验证，这将非常有用。

```python Using the HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('user', 'pass')
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=auth)

print(f'Status Code: {response.status_code}')
```

当提供基本身份验证凭据时，Requests 会自动构造 `Authorization` 标头，并使用正确编码的值将其添加到您的请求中。

## 摘要式身份验证

摘要式身份验证通过使用质询-响应机制，避免了以明文形式发送密码，为基本身份验证提供了一种更安全的替代方案。Requests 为您处理了这一流程的复杂性。要使用它，只需将 `HTTPDigestAuth` 的一个实例传递给 `auth` 参数即可。

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'
auth = HTTPDigestAuth('user', 'pass')

response = requests.get(url, auth=auth)

print(f'Status Code: {response.status_code}')
print(response.json())
```

Requests 会首先发送未经身份验证的请求，从服务器接收到带有 `WWW-Authenticate` 标头的 `401 Unauthorized` 响应，然后使用正确的摘要式身份验证标头自动重试该请求。

## 代理身份验证

如果您需要通过 HTTP 代理服务器进行身份验证，可以使用 `HTTPProxyAuth` 类。它的工作方式与 `HTTPBasicAuth` 类似，但设置的是 `Proxy-Authorization` 标头。

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# 注意：这是一个概念性示例。您需要一个正在运行且需要身份验证的代理。
proxies = {
   'http': 'http://proxy.example.com:8080',
   'https': 'http://proxy.example.com:8080',
}

proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# 此处的 'auth' 参数用于代理身份验证，因为我们提供了一个 'proxies' 字典。
# 如果目标服务器也需要身份验证，您将需要更高级的设置。
response = requests.get('https://httpbin.org/get', proxies=proxies, auth=proxy_auth)

print(f'Status Code: {response.status_code}')
```

## 自定义身份验证

Requests 中的身份验证系统被设计为可扩展的。如果您需要实现一个未内置的身份验证方案（如 OAuth1、Hawk 或自定义的基于令牌的系统），您可以创建自己的身份验证处理器。

身份验证处理器只是一个可调用对象，它接收一个 `requests.Request` 对象并返回修改后的对象。创建它的最简单方法是子类化 `requests.auth.AuthBase`。

以下是一个简单的自定义身份验证处理器的示例，它将令牌添加到一个自定义请求标头中。

```python Custom Authentication Handler icon=logos:python
import requests

class ApiTokenAuth(requests.auth.AuthBase):
    """将 API 令牌身份验证附加到给定的 Request 对象。"""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # 将自定义标头添加到请求中
        r.headers['X-API-Token'] = self.token
        return r


# 在请求中使用自定义身份验证处理器
response = requests.get('https://httpbin.org/headers', auth=ApiTokenAuth('my-secret-api-token'))

print(f'Status Code: {response.status_code}')
print(response.json()['headers']['X-Api-Token'])

# 预期输出：
# Status Code: 200
# my-secret-api-token
```

这种模块化的方法允许您将复杂的身份验证逻辑封装到一个可重用的类中，使您的请求代码保持整洁和简单。

---

现在您已经掌握了如何对请求进行身份验证，下一步是学习如何妥善管理网络问题和错误响应。请继续阅读[错误处理](./user-guide-error-handling.md)指南以了解更多信息。