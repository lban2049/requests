# 认证

许多 Web 服务需要通过认证来授予对资源的访问权限。Requests 通过 `auth` 参数直接支持多种认证方案，从而简化了这一过程。

## 基本认证

HTTP 基本认证是一种广泛使用且直接的认证方法。若要在 Requests 中使用它，你可以向 `auth` 参数提供一个 `(username, password)` 元组。

```python
import requests

# 使用元组简写形式进行基本认证
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

这个元组是一种方便的简写形式。在内部，Requests 会将其转换为 `requests.auth` 模块中的一个 `HTTPBasicAuth` 对象。你也可以直接创建和使用此对象，使代码更加明确。

```python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass', 
    auth=HTTPBasicAuth('user', 'pass')
)

print(f"Status Code: {response.status_code}")
# Status Code: 200
```
当你使用基本认证时，Requests 会自动为你构建带有正确编码凭据的 `Authorization` 标头，并将其添加到你的请求中。

## 摘要认证

摘要认证采用质询-响应机制，避免了以明文形式发送密码，因此是比基本认证更安全的替代方案。其使用方法与基本认证同样简单。

首先，导入 `HTTPDigestAuth`，然后将其一个实例传递给 `auth` 参数。

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

Requests 会为你处理整个质询-响应流程。该过程首先会发送一个未授权的初始请求，并从服务器接收到 `401` 响应。然后，Requests 会利用服务器 `WWW-Authenticate` 标头中的信息来构建第二个经过认证的请求。

下图展示了摘要认证的流程：

```d2
shape: sequence_diagram
direction: down

Client: "你的应用程序"
Server: "Web 服务"

Client -> Server: "GET /resource (无认证)"
Server -> Client: "401 Unauthorized\nWWW-Authenticate: Digest, nonce=..."

Client: {
  note: "使用凭据和服务器 nonce 计算响应"
}

Client -> Server: "GET /resource\nAuthorization: Digest, response=..."
Server -> Client: "200 OK"

```

## 自定义认证

Requests 具有一个可插拔的认证系统，允许你实现非内置的认证方案。你可以通过创建一个可调用类来自定义认证处理器，该类在 `Request` 对象发送前对其进行修改。

最简单的方法是继承 `requests.auth.AuthBase` 并实现 `__call__` 方法。该方法接收 `PreparedRequest` 对象，应根据需要对其进行修改（例如，添加自定义标头），并且必须返回修改后的对象。

以下是一个基于令牌的认证方案的自定义处理器示例：

```python
import requests
from requests.auth import AuthBase

class TokenAuth(AuthBase):
    """将自定义令牌附加到 Authorization 标头。"""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # 通过添加 Authorization 标头来修改请求 `r`
        r.headers['Authorization'] = f'Token {self.token}'
        return r

# 使用自定义认证处理器
response = requests.get('https://httpbin.org/headers', auth=TokenAuth('12345abcde'))

print(response.json())

# 预期的响应显示了自定义的 Authorization 标头：
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Token 12345abcde", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/x.x.x", 
#     "X-Amzn-Trace-Id": "..."
#   }
# }
```

这种模块化的方法提供了与任何自定义或复杂认证协议集成的灵活性。

---

现在你已经可以对请求进行认证，下一步是学习如何处理非预期的情況。请继续阅读 [错误处理](./user-guide-error-handling.md) 指南以了解更多详情。