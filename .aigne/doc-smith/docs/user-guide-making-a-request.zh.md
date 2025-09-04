# 发送请求

使用 Requests 发送 HTTP 请求非常简单。本指南介绍了如何使用各种 HTTP 方法、传递 URL 参数、自定义标头以及发送不同类型的请求正文。

## 基本的 GET 请求

要发出一个简单的 `GET` 请求，请使用 `requests.get()` 函数。这通常是与 Web 服务或 API 交互的第一步。

```python
import requests

r = requests.get('https://api.github.com/events')
# 响应对象 'r' 现在包含了服务器的响应。
```

## 在 URL 中传递参数

要添加 URL 查询参数，可以将其作为字典传递给 `params` 参数。Requests 会为你正确地构建 URL。

```python
import requests

# 定义参数
payload = {'key1': 'value1', 'key2': 'value2'}

# 发送请求
r = requests.get('https://httpbin.org/get', params=payload)

# 打印构建出的 URL
print(r.url)
```

运行以上代码将输出以下 URL，其中的参数已正确编码：

```
https://httpbin.org/get?key1=value1&key2=value2
```

如果需要为单个键提供多个值，可以使用一个元组列表：

```python

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)
print(r.url)
# 输出: https://httpbin.org/get?key1=value1&key1=value2
```

## 其他 HTTP 方法

Requests 为所有常见的 HTTP 方法提供了简单的函数。它们的工作方式都与 `requests.get()` 类似。

<x-cards data-columns="3">
  <x-card data-title="POST" data-icon="lucide:send">
    向服务器发送数据以创建一个资源。
  </x-card>
  <x-card data-title="PUT" data-icon="lucide:upload-cloud">
    发送数据以完全更新一个现有资源。
  </x-card>
  <x-card data-title="PATCH" data-icon="lucide:pencil">
    对一个资源应用部分修改。
  </x-card>
  <x-card data-title="DELETE" data-icon="lucide:trash-2">
    删除一个指定的资源。
  </x-card>
  <x-card data-title="HEAD" data-icon="lucide:file-question">
    请求一个资源的标头，不包括正文。
  </x-card>
  <x-card data-title="OPTIONS" data-icon="lucide:settings-2">
    描述目标资源的通信选项。
  </x-card>
</x-cards>

你可以这样使用它们：

```python
r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## 在请求正文中发送数据

对于 `POST`、`PUT` 和 `PATCH` 等方法，通常需要在请求正文中发送数据。

### 表单编码数据

要以 `application/x-www-form-urlencoded`（HTML 表单的默认格式）发送数据，请将一个字典传递给 `data` 参数。

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

print(r.json()['form'])
```

响应:
```json
{
  "key1": "value1",
  "key2": "value2"
}
```

### JSON 编码数据

对于现代 API 而言，以 JSON 格式发送数据非常普遍。你可以使用 `json` 参数，而无需手动使用 `json.dumps()` 对字典进行编码。Requests 会自动对数据进行编码，并将 `Content-Type` 标头设置为 `application/json`。

```python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

print(r.json()['json'])
```
响应:
```json
{
  "some": "data"
}
```

### 多部分编码文件上传

要上传文件，可以将一个类文件对象传递给 `files` 参数。文件应以二进制模式打开。

```python
import requests

url = 'https://httpbin.org/post'

# 为示例创建一个虚拟文件
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

# 以二进制模式打开文件并发送请求
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# httpbin.org 将返回上传文件的内容。
print(r.json()['files'])
```

响应:
```json
{
  "file": "This is a test report."
}
```

你还可以通过将一个元组传递给字典值来显式设置文件名、内容类型和标头：

```python
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## 自定义标头

要添加或修改 HTTP 标头，请将一个字典传递给 `headers` 参数。例如，你可能需要设置一个自定义的 `User-Agent`。

```python
import requests

url = 'https://httpbin.org/headers'
headers = {'user-agent': 'my-custom-app/0.0.1'}

r = requests.get(url, headers=headers)

print(r.json()['headers']['User-Agent'])
```

响应:
```
my-custom-app/0.0.1
```

现在你已经知道如何构建和发送请求，下一步是理解服务器的响应。请继续阅读下一节以了解更多信息。

➡️ 下一步: [处理响应](./user-guide-handling-responses.md)
