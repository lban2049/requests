# 发起请求

Requests 库简化了发送 HTTP 请求的过程。首先，你需要使用与所需 HTTP 方法相对应的函数。所有这些函数都是对主要函数 `requests.request()` 的封装。

## HTTP 方法

Requests 为每种最常见的 HTTP 方法都提供了一个函数：

*   `requests.get()`：从指定 URL 检索数据。
*   `requests.post()`：向指定资源提交待处理的数据。
*   `requests.put()`：更新资源，如果资源不存在则创建新资源。
*   `requests.patch()`：对资源进行部分修改。
*   `requests.delete()`：删除指定资源。
*   `requests.head()`：请求资源的标头，不包含正文。
*   `requests.options()`：描述目标资源的通信选项。

以下是每种方法的简单示例：

```python
import requests

r = requests.get('https://httpbin.org/get')
print(r)

r = requests.post('https://httpbin.org/post', data={'key': 'value'})
print(r)

r = requests.put('https://httpbin.org/put', data={'key': 'value'})
print(r)

r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
print(r)

r = requests.delete('https://httpbin.org/delete')
print(r)

r = requests.head('https://httpbin.org/get')
print(r)

r = requests.options('https://httpbin.org/get')
print(r)
```

响应：
```
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
```

## 在 URL 中传递参数

通常，你需要在 URL 的查询字符串中发送数据。你可以不手动构建 URL，而是通过 `params` 关键字参数，以字典或元组列表的形式提供这些参数。Requests 会自动为你进行正确的 URL 编码。

例如，要将 `key1=value1` 和 `key2=value2` 传递给 `httpbin.org/get`：

```python
import requests

# 使用字典
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

# Requests 构建的 URL
print(r.url)
```

响应：
```
https://httpbin.org/get?key1=value1&key2=value2
```

如果你需要为单个键提供多个值，也可以传递一个元组列表：

```python
import requests

# 使用元组列表
payload_list = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload_list)

print(r.url)
```

响应：
```
https://httpbin.org/get?key1=value1&key1=value2
```

## 请求体

对于 `POST`、`PUT` 和 `PATCH` 等方法，通常需要在请求体中发送数据。

### 表单编码数据

要发送类似于 HTML 表单提交的表单编码数据，请将一个字典传递给 `data` 参数。你的数据字典在请求发出时会自动进行表单编码。

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# 打印 httpbin 返回的表单数据
print(r.json()['form'])
```

响应：
```json
{
  "key1": "value1",
  "key2": "value2"
}
```

### JSON 编码数据

你也可以不使用表单编码数据，而是将其作为 JSON 序列化的字符串发送。只需使用 `json` 参数，它接受一个 Python 对象（如字典或列表）。Requests 会自动将其编码为 JSON，并将 `Content-Type` 标头设置为 `application/json`。

```python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# 打印 httpbin 返回的 JSON 数据
print(r.json()['json'])
```

响应：
```json
{
  "some": "data"
}
```

### 多部分编码文件上传

要上传多部分编码的文件，请使用 `files` 参数。你可以传递一个字典，其中键为字段名，值为类文件对象。

```python
import requests

url = 'https://httpbin.org/post'
# 你需要在同一目录下创建一个名为 'report.txt' 的文件
# 并写入一些内容才能运行此代码。
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# httpbin 会返回上传文件的内容
print(r.json()['files'])
```

响应：
```json
{
  "file": "This is a test report."
}
```

你还可以通过为文件值提供一个元组，来显式地设置文件名、内容类型和自定义标头：

```python
files = {'file': ('report.csv', 'some,data,to,send\n', 'text/csv', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## 自定义标头

要为请求添加或修改 HTTP 标头，请将一个包含标头的字典传递给 `headers` 参数。

```python
import requests

url = 'https://httpbin.org/get'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)

# httpbin 会返回这些标头
print(r.json()['headers']['User-Agent'])
```

响应：
```
my-app/0.0.1
```

---

现在你已经了解如何构建和发送请求，下一步是理解从服务器返回的内容。有关更多详细信息，请参阅 [处理响应](./user-guide-handling-responses.md) 指南。