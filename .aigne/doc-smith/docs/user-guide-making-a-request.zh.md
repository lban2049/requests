# 发起请求

使用 Requests 发起 HTTP 请求非常简单。所有示例都从导入该库开始：

```python
import requests
```

其核心的所有 HTTP 请求功能都围绕 `requests.request()` 函数构建。像 `get()` 和 `post()` 这样更简单的方法是围绕这个核心函数的便捷封装。为了快速了解，以下是最常用的参数：

| 参数 | 描述 |
|---|---|
| `method` | 请求的 HTTP 方法：`GET`、`POST`、`PUT`、`PATCH`、`DELETE`、`OPTIONS`、`HEAD`。 |
| `url` | 新 `Request` 对象的 URL。 |
| `params` | 在请求的查询字符串中发送的字典、元组列表或字节。 |
| `data` | 在请求正文中发送的字典、元组列表、字节或类文件对象（通常用于表单数据）。 |
| `json` | 在请求正文中发送的可 JSON 序列化的 Python 对象。会自动将 `Content-Type` 标头设置为 `application/json`。 |
| `headers` | 随请求发送的 HTTP 标头字典。 |
| `files` | 用于多部分编码文件上传的字典。 |
| `timeout` | 放弃前等待服务器发送数据的秒数。可以是一个浮点数，也可以是一个 `(connect, read)` 元组。 |

## GET 请求与 URL 参数

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

# 你可以检查构建的 URL
print(r.url)
# 输出: https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## POST、PUT、PATCH 与请求正文

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

对于现代 API，发送 JSON 编码的数据很常见。你可以使用 `json` 参数，它接受一个 Python 字典。Requests 会为你处理序列化并设置相应的 `Content-Type` 标头。

```python
payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

print(r.json()['json'])
# 输出: {'some': 'data'}
```

### 其他方法

在发送正文数据方面，`PUT` 和 `PATCH` 方法的功能与 `POST` 类似。

```python
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
```

## DELETE、HEAD 和 OPTIONS

其他 HTTP 方法也通过一个简单、一致的 API 提供：

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
files = {'file': open('report.txt', 'rb')}

r = requests.post(url, files=files)
```

为了获得更多控制权，你可以为文件值提供一个元组，以指定自定义文件名、内容类型和附加标头。

```python
# 元组格式为 ('filename', file_object, 'content_type', custom_headers)
files = {'file': ('report.csv', open('report.csv', 'rb'), 'text/csv', {'Expires': '0'})}

r = requests.post(url, files=files)
```

## 超时

为了防止你的程序在缓慢或无响应的网络上无限期挂起，你应该始终指定一个超时时间。`timeout` 参数接受一个浮点数值，表示等待的秒数。

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

现在你已经知道如何构建和发送请求，下一步是处理服务器返回的数据。要了解更多信息，请继续阅读下一节 [处理响应](./user-guide-handling-responses.md)。
