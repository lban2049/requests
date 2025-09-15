# 概述

![Requests Logo](../../../ext/requests-logo.png)

**Requests** 是一个简单而优雅的 Python HTTP 库，旨在使 HTTP 请求变得人性化和直接。它将发出请求的复杂性抽象在一个美观、简单的 API 背后，让你能专注于与服务交互和在应用程序中消费数据。

Requests 是世界上下载量最大的 Python 包之一，每周下载量超过 `3000 万`次。它受到超过 `1,000,000` 个代码仓库的信赖，是构建健壮可靠的 HTTP 应用程序的坚实基础。

### 快速示例

了解如何轻松地发出带有身份验证的 `GET` 请求并访问响应数据。

```python Basic GET Request icon=logos:python
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

>>> r.status_code
200

>>> r.headers['content-type']
'application/json; charset=utf8'

>>> r.encoding
'utf-8'

>>> r.text
'{"authenticated": true, ...}'

>>> r.json()
{'authenticated': True, ...}
```

使用 Requests，你无需手动向 URL 添加查询字符串或对 `POST` 数据进行表单编码。只需使用直观的方法，让库来处理繁重的工作。

### 功能与最佳实践

Requests 的构建考虑了现代 Web 开发的需求，开箱即用，提供了一套强大的功能。

| Feature                       | Description                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| Keep-Alive & Connection Pooling | 重用底层 TCP 连接，显著提升性能。 |
| International Domains and URLs  | 原生支持非 ASCII 域名和 URL。 |
| Sessions with Cookie Persistence| 在通过 Session 对象发出的所有请求之间持久化 Cookie。 |
| Browser-style TLS/SSL Verification | 像 Web 浏览器一样自动验证服务器证书。 |
| Basic & Digest Authentication   | 内置易用的身份验证辅助工具。 |
| Familiar `dict`–like Cookies    | 使用简单的字典接口管理 Cookie。 |
| Automatic Content Decompression | 自动解压 gzip、deflate 和 brotli 编码的响应。 |
| Multi-part File Uploads         | 用于上传文件的简单接口。 |
| SOCKS Proxy Support           | 通过额外的依赖项将你的请求路由到 SOCKS 代理。 |
| Connection Timeouts           | 通过设置超时防止请求无限期挂起。 |
| Streaming Downloads           | 通过迭代响应内容高效下载大文件。 |
| Automatic honoring of `.netrc`  | 如果可用，则使用你 `.netrc` 文件中的身份验证信息。 |

### 支持的版本

Requests 官方支持 Python 3.9+。

---

准备好开始了吗？请前往 [入门指南](./getting-started.md) 安装该库并发起你的第一个请求。

<x-card data-title="完整的 API 参考和用户指南" data-image="../../../ext/ss.png" data-href="https://requests.readthedocs.io" data-cta="阅读文档" >
要获取每个模块、类和函数的全面指南，请浏览我们在 Read the Docs 上托管的完整文档。
</x-card>