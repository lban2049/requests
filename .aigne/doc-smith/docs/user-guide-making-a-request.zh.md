# 发起请求

使用 Requests 发起 HTTP 请求非常简单。首先，请确保已导入该库：

```python
import requests
```

其核心的所有 HTTP 请求功能都围绕着简单的、基于动词的函数构建，例如 `requests.get()` 和 `requests.post()`。这些方法是对底层 `requests.request()` 函数的直观封装。整个过程遵循简单的请求-响应模式。

```d2
direction: right
shape: sequence_diagram

Client: {
  shape: person
  label: "你的应用程序"
}

Server: {
  shape: cloud
  label: "Web 服务器"
}

Client -> Server: "HTTP 请求 (GET, POST 等)\n- URL: /get\n- 标头: {'user-agent': 'my-app'}\n- 正文: (可选)" {
    style.animated: true
}

Server -> Client: "HTTP 响应\n- 状态码: 200 OK\n- 标头: {'content-type': 'application/json'}\n- 正文: {'key': 'value'}" {
    style.animated: true
}
```

为了快速概览，以下是请求中最常用的参数：

| 参数 | 描述 |
|---|---|
| `url` | 用于新 `Request` 对象的 URL。 |
| `params` | 以请求查询字符串形式发送的字典、元组列表或字节。 |
| `data` | 在请求正文中发送的字典、元组列表、字节或类文件对象（通常用于表单数据）。 |
| `json` | 在请求正文中发送的可 JSON 序列化的 Python 对象。它会自动将 `Content-Type` 标头设置为 `application/json`。 |
| `headers` | 随请求发送的 HTTP 标头字典。 |
| `files` | 用于多部分编码文件上传的字典。 |
| `timeout` | 在放弃前等待服务器发送数据的秒数。可以是一个浮点数，也可以是一个 `(connect, read)` 元组。 |

## GET 请求和 URL 参数

要发起 `GET` 请求以从 URL 检索数据，请使用 `requests.get()` 方法。

```python
# 发起一个简单的 GET 请求
r = requests.get('https://api.github.com/events')
```

通常，你需要在 URL 的查询字符串中传递数据。你可以向 `params` 参数提供一个字典，而无需手动构建 URL。

```python
# 传递 URL 参数
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}
r = requests.get('https://httpbin.org/get', params=payload)

# 你可以检查构建好的 URL
print(r.url)
# 输出: https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## POST、PUT、PATCH 和请求正文

像 `POST`、`PUT` 和 `PATCH` 这样的方法用于向服务器发送数据。这些数据在请求正文中传递。

### 表单编码数据

要发送表单编码数据（就像浏览器提交表单时一样），请向 `data` 参数传递一个字典。数据将被自动编码。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

print(r.json()['form'])
# 输出: {'key1': 'value1', 'key2': 'value2'}
```

### JSON 数据

对于现代 API，发送 JSON 编码的数据是很常见的。你可以使用 `json` 参数，它接受一个 Python 字典。Requests 会为你处理序列化并设置相应的 `Content-Type` 标头。

```python
payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

print(r.json()['json'])
# 输出: {'some': 'data'}
```

### 其他方法

`PUT` 和 `PATCH` 方法在发送正文数据方面的功能与 `POST` 类似。

```python
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
```

## DELETE、HEAD 和 OPTIONS

其他 HTTP 方法也通过简单、一致的 API 提供：

```python
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## 自定义标头

要添加或修改 HTTP 标头，请向 `headers` 参数传递一个字典。这对于设置自定义的 `User-Agent` 字符串、身份验证令牌或其他元数据非常有用。

```python
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

## 多部分文件上传

Requests 可以轻松地使用多部分编码数据上传文件。向 `files` 参数提供一个类文件对象的字典即可。

```python
url = 'https://httpbin.org/post'
# 确保文件 'report.txt' 存在于你的目录中
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

files = {'file': open('report.txt', 'rb')}
r = requests.post(url, files=files)
```

为了实现更多控制，你可以为文件值提供一个元组，以指定自定义文件名、内容类型和附加标头。

```python
# 元组格式为 ('filename', file_object, 'content_type', custom_headers)
with open('report.csv', 'w') as f:
    f.write('col1,col2\nval1,val2')

files = {'file': ('report.csv', open('report.csv', 'rb'), 'text/csv', {'Expires': '0'})}

r = requests.post(url, files=files)
```

## 超时

为防止程序因网络缓慢或无响应而无限期挂起，你应该始终指定超时时间。`timeout` 参数接受一个浮点数值，表示等待的秒数。

```python
# 最多等待 5 秒响应
try:
    r = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('请求超时。')
```

你还可以通过传递一个元组来为连接和从服务器读取数据指定不同的超时时间。

```python
# 3.05 秒用于连接，10 秒用于读取响应
r = requests.get('https://httpbin.org/get', timeout=(3.05, 10))
```

---

现在你已经了解如何构建和发送请求，下一步是处理服务器返回的数据。要了解更多信息，请继续阅读下一节 [处理响应](./user-guide-handling-responses.md)。