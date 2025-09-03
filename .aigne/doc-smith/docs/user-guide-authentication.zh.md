# 身份验证

许多 Web 服务需要身份验证才能访问其资源。Requests 提供了多种内置的身份验证机制和一种直接的使用方式，从而简化了这一过程。身份验证信息通常通过请求的 `auth` 参数传递。

## 基本身份验证

基本身份验证是一种广泛使用且简单的身份验证方案。使用 Requests 时，你可以通过将一个包含 `(username, password)` 的二元组传递给 `auth` 参数来提供凭据。

```python
import requests
from requests.auth import HTTPBasicAuth

# 使用元组作为简写形式
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(response.status_code)
# 200
```

这种简写形式便于快速使用。在底层，Requests 会将此元组转换为一个 `HTTPBasicAuth` 对象。你也可以直接创建并传递一个 `HTTPBasicAuth` 的实例，如果希望重用身份验证对象，这样做会非常有用。

```python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('user', 'pass')
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=auth)

print(response.status_code)
# 200
```

## 摘要式身份验证

摘要式身份验证是另一种常见的 HTTP 身份验证形式，与基本身份验证相比，它能以更安全的方式传输凭据。其使用方法同样简单。

要使用摘要式身份验证，你需要导入 `HTTPDigestAuth` 并将其一个实例传递给 `auth` 参数。

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(response.status_code)
# 200
```

Requests 会自动处理摘要式身份验证所需的多步质询-响应流程。

## 代理身份验证

如果你通过需要身份验证的代理来路由请求，Requests 也能满足你的需求。你可以使用 `HTTPProxyAuth` 辅助工具来提供代理凭据。

```python
import requests
from requests.auth import HTTPProxyAuth

proxies = {
   'http': 'http://proxy.example.com:8080',
   'https': 'https://proxy.example.com:8080',
}

# 假设代理需要身份验证
auth = HTTPProxyAuth('proxy_user', 'proxy_pass')

response = requests.get('https://httpbin.org/get', proxies=proxies, auth=auth)

print(response.status_code)
```

这会在你的请求中发送相应的 `Proxy-Authorization` 标头。

## 其他身份验证方案

Requests 的设计考虑了可扩展性。如果你需要实现更复杂的身份验证方案（如 OAuth），可以创建自己的自定义身份验证处理程序。任何接受 `Request` 对象并返回修改后的 `Request` 对象的可调用对象均可使用。这使得 Requests 几乎可以与任何身份验证机制集成。

---

现在你已经了解了如何为请求进行身份验证，接下来了解如何管理潜在问题就变得非常重要。请继续阅读下一节，学习有关[错误处理](./user-guide-error-handling.md)的内容。