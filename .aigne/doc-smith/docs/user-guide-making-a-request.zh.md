# 发起请求

发起 HTTP 请求是 Requests 库的核心功能。其设计简洁直观，让你能够专注于与服务交互，而无需管理连接和请求格式。本指南涵盖了最常见的请求方式，包括不同的 HTTP 方法以及如何发送数据。

## 发起 GET 请求

GET 是最常见的 HTTP 方法之一，用于从指定资源检索数据。要发起 GET 请求，只需使用 `requests.get()` 函数即可。

```python Making a GET Request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.status_code)
```

这段代码向 GitHub Events API 发送一个 GET 请求。请求发出后，我们会得到一个名为 `r` 的 `Response` 对象。我们可以使用此对象查看响应的详细信息，例如状态码。状态码 `200` 表示请求成功。


## 在 URL 中传递参数

通常，你需要在 URL 的查询字符串中发送数据（例如 `?key=value`）。你可以通过 `params` 关键字参数以字典形式提供这些参数，而无需手动构建 URL。Requests 会为你正确地格式化和编码它们。

```python Passing URL Parameters icon=logos:python
import requests

# Define the parameters as a dictionary
payload = {'key1': 'value1', 'key2': 'value2'}

# Make the request with the params
r = requests.get('https://httpbin.org/get', params=payload)

# You can see the URL that was constructed
print(r.url)
```

**输出：**
```text
https://httpbin.org/get?key1=value1&key2=value2
```

如果需要为同一个键提供多个值，你也可以将一个元组列表作为参数传递：

```python Passing a List of Tuples icon=logos:python
import requests

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

**输出：**
```text
https://httpbin.org/get?key1=value1&key1=value2
```

## 其他 HTTP 方法

Requests 为所有常见的 HTTP 方法都提供了简单的函数。它们都和 `get()` 一样易于使用。

```python Common HTTP Methods icon=logos:python
import requests

r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')

print(r.status_code)
```

每个函数都会返回一个包含服务器响应的 `Response` 对象。

## 在请求体中发送数据

对于 POST、PUT 和 PATCH 等方法，你通常需要在请求体中发送数据。Requests 让这个过程变得非常简单。

### 表单编码数据

要以 `application/x-www-form-urlencoded`（HTML 表单的常用格式）发送数据，你可以将一个字典传递给 `data` 参数。发出请求时，你的数据字典将被自动进行表单编码。

```python POSTing Form Data icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# The response body from httpbin.org will show the form data
print(r.json()['form'])
```

### JSON 编码数据

你可以使用 `json` 参数，而无需手动将字典编码为 JSON。Requests 会自动将你的 Python 对象序列化为 JSON 字符串，并添加正确的 `Content-Type: application/json` 标头。

```python POSTing a JSON Body icon=logos:python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# The response from httpbin.org will reflect the JSON payload
print(r.json()['json'])
```

### Multipart 编码文件上传

要上传文件，你可以将一个类文件对象传递给 `files` 参数。Requests 会自动处理 multipart 编码。

```python Uploading a File icon=logos:python
import requests

# You need to open the file in binary mode
files = {'file': open('report.xls', 'rb')}

r = requests.post('https://httpbin.org/post', files=files)

# The response will contain information about the uploaded file
print(r.json()['files'])
```

你还可以通过向 `files` 字典传递一个元组来显式设置文件名、内容类型和自定义标头。

```python Customizing File Uploads icon=logos:python
import requests

files = {
    'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})
}

r = requests.post('https://httpbin.org/post', files=files)
print(r.json()['files'])
```

## 自定义标头

如果你需要添加或修改 HTTP 标头，可以将一个字典传递给 `headers` 参数。例如，你可能需要设置一个自定义的 `User-Agent`。

```python Setting Custom Headers icon=logos:python
import requests

headers = {'user-agent': 'my-cool-app/1.0.0'}
r = requests.get('https://httpbin.org/headers', headers=headers)

print(r.json()['headers']['User-Agent'])
```

---

现在你已经知道如何创建和自定义请求，下一步是了解如何处理服务器的响应。请在下一节 [处理响应](./user-guide-handling-responses.md) 中了解更多信息。