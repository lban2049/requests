# 传递 URL 参数

通常，你需要在 URL 的查询字符串中发送数据。如果手动构建 URL，这些数据会以键值对的形式出现在问号后面，例如，`httpbin.org/get?key=val`。Requests 简化了这一过程，允许你使用 `params` 关键字参数，将这些参数作为字典或元组列表提供。

### 在字典中传递参数

在大多数情况下，使用字典是向 URL 添加查询参数最简单的方法。Requests 会自动将字典格式化为 URL 编码的查询字符串。

**参数**

<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="新请求对象的 URL。"></x-field>
  <x-field data-name="params" data-type="dict | list | bytes" data-required="false" data-desc="在请求的查询字符串中发送的数据。"></x-field>
</x-field-group>

**示例**

让我们将 `key1=value1` 和 `key2=value2` 传递给 `httpbin.org/get` 端点：

```python 使用 URL 参数发送 GET 请求 icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

通过打印 URL，可以看到它已被正确编码。输出将是：

```text URL 输出
https://httpbin.org/get?key1=value1&key2=value2
```

**响应示例**

来自 `httpbin.org` 的响应将反映你发送的参数：

```json 来自 httpbin.org 的响应 icon=mdi:code-json
{
  "args": {
    "key1": "value1", 
    "key2": "value2"
  }, 
  ...
}
```

### 传递元组列表

在某些情况下，你可能需要为单个键提供多个值。为此，你应该传递一个元组列表作为 `params` 的值。

**示例**

```python 为单个键传递多个值 icon=logos:python
import requests

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

这将导致 URL 中 `key1` 出现两次：

```text URL 输出
https://httpbin.org/get?key1=value1&key1=value2
```

**响应示例**

`httpbin.org` 会将其解析为 `key1` 参数的值列表：

```json 来自 httpbin.org 的响应 icon=mdi:code-json
{
  "args": {
    "key1": [
      "value1", 
      "value2"
    ]
  }, 
  ...
}
```

通过使用 `params` 参数，你可以让 Requests 处理 URL 编码，确保参数被正确发送，而无需手动操作字符串。

现在你已经知道如何使用参数发出请求，下一步是学习[处理响应内容](./core-usage-handling-response-content.md)。