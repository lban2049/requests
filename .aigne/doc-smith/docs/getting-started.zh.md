# 入门

Requests 库简化了 HTTP/1.1 请求的发送。本节将引导您安装 Requests 库并执行您的第一个 HTTP 请求。

## 安装

Requests 库可在 Python 包索引 PyPI 上获取。您可以使用 Python 的包安装程序 pip 来安装它。

要安装 Requests 库，请打开您的终端或命令提示符，然后运行以下命令：

```console
$ python -m pip install requests
```

Requests 库官方支持 Python 3.9 及更高版本。如果您使用的是较旧的 Python 版本，则需要升级到受支持的版本，或者指定使用 Requests 库的旧版本（2.32.0 之前）。

根据 GitHub 的数据，Requests 库是一个被广泛采用的 Python 包，每周下载量约为 3000 万次，并被超过 1,000,000 个仓库所依赖。

[![Downloads](https://static.pepy.tech/badge/requests/month)](https://pepy.tech/project/requests)
[![Supported Versions](https://img.shields.io/pypi/pyversions/requests.svg)](https://pypi.org/project/requests)
[![Contributors](https://img.shields.io/github/contributors/psf/requests.svg)](https://github.com/psf/requests/graphs/contributors)

## 发送您的第一个请求

安装完成后，您就可以开始使用 Requests 库与 Web 服务进行交互。以下是一个如何发出 GET 请求并检查响应的基本示例：

```python
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
'{"authenticated": true, ...'
>>> r.json()
{'authenticated': True, ...}
```

此示例演示了向 `https://httpbin.org/basic-auth/user/pass` 发送带有基本身份验证的 GET 请求。让我们来解析一下这个响应：

-   `r.status_code`：用于获取响应的 HTTP 状态码。`200` 表示请求成功。
-   `r.headers['content-type']`：用于访问 `Content-Type` 头部，它指定了资源的媒体类型。这里是 `application/json; charset=utf8`。
-   `r.encoding`：显示响应内容的检测到的字符编码，为 `utf-8`。
-   `r.text`：以 Unicode 格式提供响应内容，使其易于作为字符串读取。
-   `r.json()`：如果响应包含 JSON 数据，此方法会自动将其解析为 Python 字典或列表，从而方便地访问结构化数据。

Requests 库抽象化了手动添加查询字符串或表单编码数据的复杂性，让您能够以最小的努力发送请求。

---

现在您已经成功安装了 Requests 库并发送了您的第一个 HTTP 请求，您可以进一步探索其功能。请继续前往[核心概念](./core-concepts.md)部分，了解 Requests 库的基本组成部分。