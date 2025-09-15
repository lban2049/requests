# 概述

**Requests** 是一个简洁而优雅的 Python HTTP 库，旨在使 HTTP 请求变得人性化和简单。它通过一个美观、简洁的 API 抽象了发出请求的复杂性，因此你可以专注于与服务交互和在应用程序中消费数据。

> 为人类设计的 Python HTTP 库。

Requests 是世界上下载量最大的 Python 包之一，每周下载量超过 `3000 万次`。它受到 GitHub 上超过 `1,000,000` 个代码仓库的信赖，是您项目的可靠且稳健的选择。

## 为什么使用 Requests？

使用 Requests 发送 HTTP/1.1 请求非常简单。你不再需要手动向 URL 添加查询字符串或对 `POST` 数据进行表单编码。该库可以无缝处理这些任务，使代码更简洁、更易读。

下面简要介绍如何通过身份验证发出一个 `GET` 请求：

```python Basic GET Request icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# 检查状态码
print(r.status_code)
# >>> 200

# 访问响应头
print(r.headers['content-type'])
# >>> 'application/json; charset=utf8'

# 以文本形式访问响应体
print(r.text)
# >>> '{"authenticated": true, ...}'

# 或者，将其解码为 JSON
print(r.json())
# >>> {'authenticated': True, ...}
```

## 核心功能

Requests 包含了丰富的功能，可以满足现代、稳健和可靠的 HTTP 应用程序的需求。

| Feature                       | Description                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| 保持连接与连接池 | 重用底层 TCP 连接，显著提升性能。   |
| 国际化域名与 URL  | 原生支持国际化域名，适用于全球应用程序。   |
| 带 Cookie 持久化的会话| 通过会话对象在多个请求之间保持参数和 Cookie。 |
| 浏览器风格的 TLS/SSL 验证 | 默认情况下，像浏览器一样验证 HTTPS 请求的 SSL 证书。 |
| 基本与摘要式身份验证 | 内置易于使用的身份验证支持。                               |
| 熟悉的类 `dict` Cookie  | 通过简单的类字典接口管理 Cookie。                     |
| 自动解压       | 自动解压 `gzip`、`deflate` 和 `brotli` 编码的内容。     |
| 多部分文件上传       | 用于上传文件的简化接口。                                  |
| 连接超时           | 通过设置连接超时，防止请求无限期挂起。   |
| 流式下载           | 无需一次性将全部内容加载到内存中即可下载大文件。|
| 分块 HTTP 请求         | 支持用于流式上传的分块传输编码。                   |

## 安装

开始使用 Requests 非常简单，只需运行一个命令即可。该库可在 PyPI 上获取，并正式支持 **Python 3.9+**。

```console
$ python -m pip install requests
```

有关安装和发出第一个请求的更详细指南，请参阅 [入门](./getting-started.md) 部分。

---

准备好深入了解了吗？完整的 API 参考和用户指南可在 [Read the Docs](https://requests.readthedocs.io) 上找到。

[![Read the Docs](https://raw.githubusercontent.com/psf/requests/main/ext/ss.png)](https://requests.readthedocs.io)