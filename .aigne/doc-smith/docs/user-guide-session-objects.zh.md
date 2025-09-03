# 会话对象

Session 对象允许你在多个请求之间保持某些参数。它也会在从 Session 实例发出的所有请求中保持 Cookie，并会使用 `urllib3` 的连接池。因此，如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

Session 对象拥有主 Requests API 的所有方法。

让我们在多个请求之间保持一些 Cookie：

```python
import requests

s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

Session 也可以用来为请求方法提供默认数据。这可以通过为 Session 对象的属性提供数据来完成：

```python
import requests

s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# both 'x-test' and 'x-test2' are sent
r = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})

print(r.text)
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Basic dXNlcjpwYXNz", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.32.3", 
#     "X-Amzn-Trace-Id": "Root=1-66a7b732-2d85835b446108130386811a", 
#     "X-Test": "true", 
#     "X-Test2": "true"
#   }
# }
```

你传递给请求方法的任何字典都将与已设置的会话级别的值合并。方法级别的参数会覆盖会话参数。

### 会话工作流图

下图说明了 Session 对象如何在多个请求之间维护状态（如 Cookie 和标头）。

```d2
direction: down

"你的应用": {
  shape: rectangle
}

"requests.Session()": {
  shape: package
  "Cookie": { shape: stored_data }
  "标头": { shape: document }
  "认证": { shape: document }
}

"远程服务器": {
  shape: cylinder
}

"你的应用" -> "requests.Session()": "1. 创建会话"
"requests.Session()" -> "远程服务器": "2. 发出请求 1（例如，登录）"
"远程服务器" -> "requests.Session()": "3. 接收响应 + Cookie"
"requests.Session()" -> "远程服务器": "4. 发出请求 2（发送存储的 Cookie）"
"远程服务器" -> "requests.Session()": "5. 接收经过身份验证的响应"
"requests.Session()" -> "你的应用": "返回最终响应"

```

但是请注意，即使使用会话，方法级别的参数也*不会*在多个请求之间保持。下面的例子只会在第一个请求中发送 Cookie，而不会在第二个请求中发送：

```python
import requests

s = requests.Session()

r = s.get('https://httpbin.org/cookies', cookies={'from-my': 'browser'})
print(r.text)
# '{\n  "cookies": {\n    "from-my": "browser"\n  }\n}'

r = s.get('https://httpbin.org/cookies')
print(r.text)
# '{\n  "cookies": {}\n}'
```

如果你想从 Session 中移除某个属性，可以将其值设置为 `None`。例如，要移除会话级别的标头：

```python
s.headers = None
```

### 上下文管理器

Session 也可以用作上下文管理器，这样可以确保即使发生异常，会话也会被关闭。这是使用 Session 的推荐方式。

```python
with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

关闭会话会清理所有适配器，进而关闭所有连接池中的连接。

Session 中包含的所有值都可以直接访问。更多信息请参见 [Session API 文档](https://requests.readthedocs.io/en/latest/api/#requests.Session)。

现在你已经了解了如何使用会话管理状态，让我们来探讨如何实现不同类型的[身份验证](./user-guide-authentication.md)。
