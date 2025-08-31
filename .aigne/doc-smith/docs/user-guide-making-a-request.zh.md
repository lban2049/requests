# 发起请求

使用 Requests 库发起 HTTP 请求的设计旨在简单直观。首先，请确保已导入该库：

```python
import requests
```

整个 API 围绕七个主要函数构建，每个函数对应一个 HTTP 动词。这些函数是底层 `requests.request()` 方法的直接封装，提供了一种清晰易读的方式来与 Web 服务进行交互。

其基本交互遵循一个明确的请求-响应周期。

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

以下是构建请求时最常用的一些参数摘要：

| Parameter | Description |
|---|---|
| `url` | 新的 `Request` 对象的 URL。 |
| `params` | 以请求查询字符串形式发送的字典、元组列表或字节。 |
| `data` | 在请求正文中发送的字典、元组列表、字节或类文件对象（通常用于表单数据）。 |
| `json` | 在请求正文中发送的可 JSON 序列化的 Python 对象。这会自动将 `Content-Type` 标头设置为 `application/json`。 |
| `headers` | 随请求一同发送的 HTTP 标头字典。 |
| `files` | 用于多部分编码文件上传的字典。 |
| `timeout` | 在放弃前等待服务器发送数据的秒数。可以是一个浮点数，也可以是一个 `(connect, read)` 元组。 |

## GET 请求与 URL 参数

要发起 `GET` 请求以从 URL 检索数据，请使用 `requests.get()` 方法。这是最常见的请求类型之一。

```python
# 发起一个简单的 GET 请求
r = requests.get('https://api.github.com/events')
```

通常，你需要在 URL 的查询字符串中传递数据。你可以向 `params` 参数提供一个字典，Requests 会自动为你构建 URL，而无需手动拼接。

```python
# 定义一个参数字典
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

# 使用参数发起请求
r = requests.get('https://httpbin.org/get', params=payload)

# 你可以查看构建好的 URL
print(r.url)
# 预期输出: https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## POST、PUT、PATCH 与请求体

`POST`、`PUT` 和 `PATCH` 等方法用于向服务器发送数据。这些数据通过请求体进行传输。

### 表单编码数据

要发送表单编码数据（即浏览器在用户提交表单时所做操作），请向 `data` 参数传递一个字典。你的数据字典在请求发出时会自动进行表单编码。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# 你可以查看服务器接收到的表单数据
print(r.json()['form'])
# 预期输出: {'key1': 'value1', 'key2': 'value2'}
```

### JSON 数据

许多现代 API 都要求发送 JSON 编码的数据。你可以使用 `json` 参数，它接受 Python 字典或其他可 JSON 序列化的对象。Requests 会处理序列化过程，并自动设置正确的 `Content-Type` 标头 (`application/json`)。

```python
payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# 查看服务器接收到的 JSON 数据
print(r.json()['json'])
# 预期输出: {'some': 'data'}
```

### 其他方法

在发送请求体数据方面，`PUT` 和 `PATCH` 方法的功能与 `POST` 完全相同。

```python
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
```

## DELETE、HEAD 和 OPTIONS

其他 HTTP 方法也可以通过类似简单且一致的 API 进行调用，尽管它们通常不涉及发送请求体。

```python
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## 自定义标头

要为请求添加或修改 HTTP 标头，请向 `headers` 参数传递一个字典。这对于设置自定义 `User-Agent` 字符串、身份验证令牌或其他请求元数据非常有用。

```python
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

## 多部分文件上传

Requests 简化了使用多部分编码数据上传文件的过程。向 `files` 参数提供一个由类文件对象（以二进制模式打开）组成的字典即可。

```python
url = 'https://httpbin.org/post'

# 为示例创建一个虚拟文件
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

# 以二进制读取模式打开文件，并将其传递给 files 参数
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# 服务器将在 'files' 字段中接收到该文件
print(r.json()['files'])
# 预期输出: {'file': 'This is a test report.'}
```

为了更精细地控制上传文件，你可以为字典值提供一个元组。这样就可以为该文件指定自定义文件名、内容类型和附加标头。

```python
# 元组的格式为 ('filename', file_object, 'content_type', custom_headers)
with open('report.csv', 'w') as f:
    f.write('col1,col2\nval1,val2')

with open('report.csv', 'rb') as f:
    files = {'file': ('custom_report_name.csv', f, 'text/csv', {'Expires': '0'})}
    r = requests.post(url, files=files)

# 查看服务器接收到的标头和文件名
print(r.json()['files'])
```

## 超时

为防止程序因服务器缓慢或无响应而无限期等待，你应该始终指定一个超时时间。`timeout` 参数接受一个浮点数，表示等待服务器发送响应的秒数。

```python
# 最多等待 5 秒响应
try:
    r = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('请求超时。')
```

你还可以通过传递一个元组来为连接服务器和读取响应分别指定不同的超时时间。

```python
# 等待 3.05 秒建立连接，然后等待 10 秒接收响应
r = requests.get('https://httpbin.org/get', timeout=(3.05, 10))
```

---

现在你已经熟悉如何构建和发送请求，下一步是处理服务器返回的数据。要了解更多信息，请继续阅读下一节 [处理响应](./user-guide-handling-responses.md)。