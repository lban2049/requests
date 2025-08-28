# 会话对象

Session 对象允许你在多个请求之间保持某些参数。它还会在从该实例发出的所有请求中持久保存 Cookie，并使用 `urllib3` 的连接池。如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

尽管你已在 [发起请求](./user-guide-making-a-request.md) 指南中学习了如何发起单个请求，但对于需要维护状态的、与 API 或网站进行的更复杂的交互，Session 对象至关重要。

## 基本用法

首先，只需创建一个 `Session` 类的实例。它的使用方式与顶层的 `requests` 模块相同。

```python
import requests

s = requests.Session()

response = s.get('https://httpbin.org/get')
print(response.status_code)

response = s.post('https://httpbin.org/post', json={'key': 'value'})
print(response.json())
```

## Cookie 持久化

会话的一个主要用途是在多个请求之间保持 Cookie。Session 对象会自动为你处理此问题。服务器在响应中设置的任何 Cookie 都会被捕获，并在使用同一会话发出的后续请求中发送。这对于与使用基于 Cookie 的身份验证或会话跟踪的服务进行交互至关重要。

以下工作流程演示了会话如何管理 Cookie：

```d2
shape: sequence_diagram

客户端
会话
服务器

客户端 -> 会话: s.get("https://httpbin.org/cookies/set/sessioncookie/12345")
会话 -> 服务器: "GET /cookies/set/sessioncookie/12345"
服务器 -> 会话: "带有 'Set-Cookie' 标头的响应"
note: {
  "Cookie 'sessioncookie=12345' 存储在 session.cookies 中"
  target: 会话
}
会话 -> 客户端: "响应对象"

客户端 -> 会话: s.get("https://httpbin.org/cookies")
会话 -> 服务器: "GET /cookies (发送存储的 'Cookie' 标头)"
服务器 -> 会话: "包含接收到的 Cookie 的响应"
会话 -> 客户端: "包含 Cookie 数据的响应对象"
```

**示例代码**

```python
import requests

with requests.Session() as s:
    # 对此 URL 的第一个请求会在会话中设置一个 Cookie
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

    # 对同一域上不同 URL 的第二个请求将自动发送该 Cookie
    response = s.get('https://httpbin.org/cookies')

    # 响应将显示会话发送的 Cookie
    print(response.json())
```

**响应示例**

```json
{
  "cookies": {
    "sessioncookie": "123456789"
  }
}
```

`session.cookies` 对象是 `RequestsCookieJar` 的一个实例，其用法类似字典，但提供了更高级的功能。

## 在请求之间持久化参数

会话还可用于设置默认数据，这些数据将包含在每个请求中。这可以通过在 `Session` 对象上设置属性来完成。

| 属性     | 描述                                               |
|---------------|-----------------------------------------------------------|
| `headers`     | 随每个请求发送的标头字典。       |
| `auth`        | 身份验证元组或可调用对象。                      |
| `params`      | 要添加到 URL 的查询字符串参数字典。|
| `proxies`     | 要使用的代理字典。                           |
| `verify`      | SSL 验证设置（布尔值或 CA 证书包路径）。  |
| `cert`        | SSL 客户端证书路径。                        |

**示例：持久化标头**

如果你想确保每个请求都发送一组特定的标头，可以更新会话的 `headers` 字典。

```python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'true'})

# 此请求将带有 'x-test-header' 标头
response1 = s.get('https://httpbin.org/headers')
print('Response 1 Headers:', response1.json()['headers']['X-Test-Header'])

# 此请求也将带有相同的标头
response2 = s.get('https://httpbin.org/headers')
print('Response 2 Headers:', response2.json()['headers']['X-Test-Header'])
```

**合并参数**

如果在方法级别（例如，在 `s.get()` 中）提供参数，这些参数将与会话级别的参数合并。对于特定请求，如果存在键冲突，方法级别的参数将覆盖会话参数。

```python
import requests

with requests.Session() as s:
    s.params.update({'param1': 'session_value'})
    s.headers.update({'X-Custom': 'SessionHeader'})

    # 此请求将同时包含会话参数和方法参数。
    # 'X-Custom' 仅在此次请求中被覆盖。
    response = s.get('https://httpbin.org/get', 
                     params={'param2': 'request_value'},
                     headers={'X-Custom': 'RequestHeader'})

    print("URL Arguments:", response.json()['args'])
    print("Custom Header:", response.json()['headers']['X-Custom'])

    # 后续请求将恢复使用会话的默认标头。
    response2 = s.get('https://httpbin.org/get')
    print("Subsequent Custom Header:", response2.json()['headers']['X-Custom'])
```

**输出示例**

```text
URL 参数： {'param1': 'session_value', 'param2': 'request_value'}
自定义标头： RequestHeader
后续自定义标头： SessionHeader
```

## 作为上下文管理器的会话

`Session` 对象可用作上下文管理器，在退出 `with` 块时会自动调用 `session.close()`。这是推荐的做法，因为它能确保连接池中的所有底层连接都被正确关闭。

```python
import requests

with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Inside with block, status: {response.status_code}")

# 会话 's' 现已关闭，其连接也已释放。
```

使用会话可以通过重用连接来有效管理状态并提升性能。对于需要安全访问的交互，请继续阅读下一节，了解如何管理 [身份验证](./user-guide-authentication.md)。
