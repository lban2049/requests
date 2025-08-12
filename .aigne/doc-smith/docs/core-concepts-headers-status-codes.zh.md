# 请求头与状态码

HTTP 通信严重依赖请求头和状态码来传达有关请求和响应的关键信息。请求头充当元数据，提供有关消息体、发送方或预期接收方的详细信息。状态码是数字指示符，表示服务器尝试满足客户端请求的结果。Requests 简化了两者的处理，特别是通过提供一种灵活的方式，使用不区分大小写的字典结构来管理 HTTP 请求头。

要了解请求头和状态码如何融入整个请求-响应周期，您可以参考 [请求与响应](./core-concepts-requests-responses.md) 部分。

## HTTP 请求头：不区分大小写的处理

Requests 使用一个名为 `CaseInsensitiveDict` 的专用字典式对象来管理 HTTP 请求头。这种结构确保了请求头查找不区分大小写，与 HTTP 规范的最佳实践保持一致，同时在存储或迭代请求头名称时保留其原始大小写。这意味着您可以使用 `headers['content-type']` 或 `headers['Content-Type']` 访问像 `Content-Type` 这样的请求头，并获得相同的结果。

以下是 `CaseInsensitiveDict` 如何简化请求头操作的示例：

```python
from requests.structures import CaseInsensitiveDict

# Create an instance of CaseInsensitiveDict
headers = CaseInsensitiveDict()

# Set headers
headers['Content-Type'] = 'application/json'
headers['Accept-Encoding'] = 'gzip, deflate'

# Access headers case-insensitively
print(headers['content-type']) # Output: application/json
print(headers['accept-encoding']) # Output: gzip, deflate

# Original casing is preserved for iteration
for key, value in headers.items():
    print(f"{key}: {value}")
# Output:
# Content-Type: application/json
# Accept-Encoding: gzip, deflate

# In Requests, default headers are also handled this way
import requests

response = requests.get('https://httpbin.org/headers')

# Accessing response headers (which are also a CaseInsensitiveDict)
print(response.headers['content-type'])
print(response.headers.get('Server'))
```

Requests 还提供了用于更高级请求头操作的实用函数，例如用于解析 `Link` 请求头的 `parse_header_links` 或用于从复杂请求头字符串中提取键值对的 `parse_dict_header`。这些实用函数通常用于内部使用或高度特定的自定义场景。

## HTTP 状态码

Requests 在其顶级模块中提供了一个方便的 `codes` 对象，以便通过其描述性名称轻松引用常见的 HTTP 状态码。这个 `codes` 对象是 `LookupDict` 的一个实例，它允许您使用属性访问（例如 `requests.codes.ok`）或字典式查找（例如 `requests.codes['not_found']`）来访问状态码。许多状态码都有多个别名，并且其名称的大写和小写版本都受识别。

以下是如何使用 `requests.codes` 对象的一些示例：

```python
import requests

# Access a status code by name
print(requests.codes.ok) # Output: 200
print(requests.codes['not_found']) # Output: 404

# Using aliases
print(requests.codes.all_ok) # Output: 200
print(requests.codes.teapot) # Output: 418
print(requests.codes['temporary_redirect']) # Output: 307
print(requests.codes['\o/']) # Output: 200 (for success)
print(requests.codes['/o\']) # Output: 500 (for server error)
```

下表列出了常见的 HTTP 状态码及其通过 `requests.codes` 可用的相关名称和别名：

| 代码 | 名称 / 别名 |
|---|---|
| 100 | continue |
| 200 | ok, okay, all_ok, all_okay, all_good, \o/, ✓ |
| 201 | created |
| 204 | no_content |
| 301 | moved_permanently, moved, \o- |
| 302 | found |
| 307 | temporary_redirect, temporary_moved, temporary |
| 400 | bad_request, bad |
| 401 | unauthorized |
| 403 | forbidden |
| 404 | not_found, -o- |
| 418 | im_a_teapot, teapot, i_am_a_teapot |
| 500 | internal_server_error, server_error, /o\, ✗ |
| 503 | service_unavailable, unavailable |

## 总结

理解 HTTP 请求头和状态码对于构建健壮且高效的 Web 应用程序至关重要。Requests 提供了直观的工具，如用于请求头管理的 `CaseInsensitiveDict` 和用于状态码解释的 `requests.codes` 对象，简化了常见的 HTTP 交互。这些核心概念为更复杂的场景奠定了基础。

有关 HTTP 请求的更高级配置，请继续阅读 [高级用法](./advanced-usage.md) 部分。如果您需要所有可用 API 及其详细信息的完整参考，请查阅 [API 参考](./api-reference.md) 。