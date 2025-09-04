# Session 对象

Session 对象是一个强大的工具，它允许你在多个请求之间持久化参数。它还会为发往同一主机的请求重用底层的 TCP 连接，这可以显著提升性能。从本质上讲，它为你管理 Cookie、请求头和连接池。

虽然你可以使用 `requests.get()`、`requests.post()` 等方法发起单个请求，但在需要向同一 API 或网站发起多个请求时，强烈建议使用 `Session` 对象。

## 基本用法

`Session` 对象拥有与顶层 `requests` 模块相同的方法。让我们先创建一个会话并发起一个简单的 GET 请求。

```python
import requests

s = requests.Session()

response = s.get('https://httpbin.org/get')
print(response.status_code)
```

为了妥善管理资源，最好使用 `with` 语句，它能确保会话在使用完毕后自动关闭。

```python
import requests

with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(response.json())
```

## Cookie 持久化

Session 的一个关键特性是它能够持久化 Cookie。当你发起请求时，服务器设置的任何 Cookie 都会被存储在 Session 的 Cookie 罐中，并在后续发往同一域名的请求中自动发送。Web 浏览器就是通过这种方式来维持登录状态的。

下面的示例演示了这一行为：

```python
import requests

with requests.Session() as s:
    # First, let's visit a URL that sets a cookie
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

    # Now, let's make another request to a different URL that can read cookies
    response = s.get('https://httpbin.org/cookies')

    # The response will show the cookie we received from the first request
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
如你所见，在第一个请求中设置的 `sessioncookie` 已被自动包含在第二个请求中。

## 持久化参数

Session 也可用于持久化其他请求数据，例如请求头、查询参数和认证详情。你分配给会话属性的任何字典都将与特定于请求的参数合并。

### 默认请求头

如果你需要在每个请求中都发送相同的请求头，可以在会话的 `headers` 属性上进行设置。

```python
import requests

with requests.Session() as s:
    s.headers.update({'x-test-header': 'true'})

    # This request will have the 'x-test-header'
    response_1 = s.get('https://httpbin.org/headers')
    print('Response 1:', response_1.json()['headers']['X-Test-Header'])

    # This request will also have it, along with a new one
    response_2 = s.get('https://httpbin.org/headers', headers={'x-another-header': 'true'})
    print('Response 2:', response_2.json()['headers']['X-Test-Header'])
    print('Response 2:', response_2.json()['headers']['X-Another-Header'])
```

此示例表明，`x-test-header` 会随两个请求一起发送，并且请求级别的请求头会与会话级别的请求头合并。

### 默认查询参数

同样，你也可以在 `params` 属性上设置默认的查询字符串参数。

```python
import requests

with requests.Session() as s:
    s.params = {'api_key': 'shared_key'}

    # This will be sent to https://httpbin.org/get?api_key=shared_key
    response = s.get('https://httpbin.org/get')
    print(response.json()['args'])
```

**响应示例**
```json
{
  "api_key": "shared_key"
}
```

## 性能与连接池

当你使用 `Session` 对象向同一主机发起多个请求时，它会重用底层的 TCP 连接，这可以带来显著的性能提升。这个过程被称为连接池。

下面是一个比较单个请求与基于会话的请求的概念图：

```d2
direction: down

"应用": {
  shape: rectangle
}

"服务器": {
  shape: cylinder
}

"单个请求": {
  shape: package

  "请求 1": {
    label: "请求 1"
    shape: rectangle
  }
  "TCP 1": {
    label: "新建 TCP 连接"
  }

  "请求 2": {
    label: "请求 2"
    shape: rectangle
  }
  "TCP 2": {
    label: "新建 TCP 连接"
  }

  "应用" -> "请求 1": "发送"
  "请求 1" -> "TCP 1": "打开"
  "TCP 1" -> "服务器": "连接"
  "服务器" -> "TCP 1": "响应"
  "TCP 1" -> "请求 1": "传递"
  "请求 1" -> "应用": "返回"

  "应用" -> "请求 2": "发送"
  "请求 2" -> "TCP 2": "打开"
  "TCP 2" -> "服务器": "连接"
  "服务器" -> "TCP 2": "响应"
  "TCP 2" -> "请求 2": "传递"
  "请求 2" -> "应用": "返回"
}

"基于会话的请求": {
  shape: package

  "会话": {
    label: "Session 对象"
    shape: rectangle
  }

  "连接池": {
    shape: queue
  }

  "请求 3": {
    label: "请求 1"
    shape: rectangle
  }

  "请求 4": {
    label: "请求 2"
    shape: rectangle
  }

  "应用" -> "会话": "创建"
  "会话" -> "请求 3": "发送"
  "请求 3" -> "连接池": "打开新的 TCP 连接"
  "连接池" -> "服务器": "连接"
  "服务器" -> "连接池": "响应"
  "连接池" -> "请求 3": "传递"
  "请求 3" -> "会话": "返回"
  
  "会话" -> "请求 4": "发送"
  "请求 4" -> "连接池": "重用 TCP 连接"
  "连接池" -> "服务器": "连接"
  "服务器" -> "连接池": "响应"
  "连接池" -> "请求 4": "传递"
  "请求 4" -> "会话": "返回"
}

```

通过避免为每个请求建立新连接的开销，会话可以显著减少延迟，尤其是在处理需要 TLS 握手的 HTTPS 时。

---

通过使用 `Session` 对象，你可以编写更简洁的代码，轻松管理像 Cookie 这样的状态，并提升应用程序的性能。下一节将介绍如何处理不同类型的身份验证，而使用会话通常可以简化这项任务。

继续阅读下一节，了解[身份验证](./user-guide-authentication.md)。
