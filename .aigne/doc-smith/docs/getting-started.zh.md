# 快速入门

本指南提供了安装 Requests 库和发起首次 HTTP 请求的基本步骤。跟随本指南，只需几分钟即可完成所有设置并开始使用。

## 安装

首先，你需要安装该库。Requests 已发布在 Python Package Index (PyPI) 上，可通过 pip 进行安装。

```console Install with pip icon=logos:python
$ python -m pip install requests
```

Requests 官方支持 Python 3.9 及更高版本。在继续操作前，请确保你的环境满足此要求。

## 发起首次请求

安装 Requests 后，发起 HTTP 请求就非常简单。我们先从 GitHub Events API 获取一些数据作为开始。

```python Making a GET request icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')
```

现在，你得到了一个名为 `r` 的 `Response` 对象。该对象包含了服务器响应的所有信息。

你可以通过检查状态码轻松判断请求是否成功：

```python Check the status code
>>> r.status_code
200
```

状态码 `200` 表示请求成功。其他状态码，如 `404`，则表示未找到相应资源。

Requests 也让访问响应内容变得很简单。对于文本类型的响应，你可以使用 `.text` 属性：

```python Access response content as text
>>> r.text
'{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.32.3", \n    "X-Amzn-Trace-Id": "Root=1-66a93555-0123456789abcdef01234567"\n  }, \n  "origin": "127.0.0.1", \n  "url": "https://httpbin.org/get"\n}'
```

对于返回 JSON 的 API（这种情况非常普遍），你可以使用内置的 `.json()` 方法将其内容直接解析为 Python 字典：

```python Decode JSON response
>>> r.json()
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=1-66a93555-0123456789abcdef01234567'}, 'origin': '127.0.0.1', 'url': 'https://httpbin.org/get'}
```

## 后续步骤

恭喜！你已成功安装 Requests 并完成了首次 API 调用。现在你可以开始探索该库提供的更多功能了。

如需深入了解使用 POST 请求发送数据、利用 Session 对象提升性能以及处理身份验证等功能，请继续阅读[用户指南](./user-guide.md)。