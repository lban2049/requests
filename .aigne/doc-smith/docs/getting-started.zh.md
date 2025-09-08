# 快速入门

本指南提供了安装 Requests 库及发起首次 HTTP 请求的基本步骤，旨在帮助您快速上手，以便开始构建应用。

## 安装

在开始之前，请确保您已安装受支持的 Python 版本（3.9+）。Requests 已发布在 Python 包索引（PyPI）上，可使用 `pip` 进行安装。

```console 安装 Requests icon=logos:python
$ python -m pip install requests
```

这一条命令将下载并安装 Requests 及其所有必需的依赖项，包括 `urllib3`、`certifi`、`charset_normalizer` 和 `idna`。

## 发起首次请求

安装 Requests 后，您就可以开始发起 HTTP 请求。该过程非常简单，体现了 Web 的简洁性。以下是向测试端点发送 `GET` 请求的方法。

```python 发起 GET 请求 icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')
```

执行此代码后，您将得到一个名为 `r` 的 `Response` 对象。该对象包含了服务器返回的所有信息，例如内容、状态码和响应头。

## 检查响应

通过 `Response` 对象，可以轻松访问服务器响应的详细信息。以下是您会用到的一些最常见的属性。

### 状态码

您可以检查 HTTP 状态码，以验证请求是否成功。状态码 `200` 表示成功。

```python 检查状态码
>>> r.status_code
200
```

### 响应头

服务器的响应头可通过一个类字典对象获取。您可以通过键来访问任何响应头。

```python 访问响应头
>>> r.headers['content-type']
'application/json; charset=utf8'
```

### 响应体

对于文本类型的响应，您可以使用 `.text` 属性以字符串的形式访问其内容。

```python 以文本形式获取响应体
>>> r.text
'{"authenticated": true, ...'
```

如果端点返回的是 JSON（许多 API 都是如此），Requests 提供了一个便捷的内置 JSON 解码器，可将内容解析为 Python 字典。

```python 解码 JSON 响应
>>> r.json()
{'authenticated': True, ...}
```

## 后续步骤

现在，您已成功安装 Requests 并完成了一次基本的 `GET` 请求。要深入了解该库的功能，下一步应探索构建请求和处理不同类型数据的各种方法。

<x-card data-title="发起请求" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request" data-cta="继续阅读用户指南">
  了解如何使用 GET、POST 和 PUT 等多种 HTTP 方法，以及如何传递 URL 参数、请求头和请求体。
</x-card>