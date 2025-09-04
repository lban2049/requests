# 概述

![Requests Logo](../../../ext/requests-logo.png)

Requests 是一个为人类设计的、优雅简洁的 Python HTTP 库。它能让你轻松发送 HTTP/1.1 请求，而无需手动为 URL 添加查询字符串，也无需对 `PUT` 和 `POST` 数据进行表单编码。

如今，Requests 是下载次数最多的 Python 包之一，每周下载量约 `3000 万次`，并且是 GitHub 上超过 `1,000,000` 个代码库的依赖项。你可以完全信赖这段代码。

以下是使用 Requests 执行带有基本身份验证的 `GET` 请求的简单示例：

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

## 功能特性

Requests 足以满足构建稳健可靠的 HTTP 应用程序的需求。它提供了丰富的开箱即用功能。

<x-cards data-columns="3">
  <x-card data-title="连接管理" data-icon="lucide:network">
    包含 Keep-Alive 和连接池，可实现高效的网络利用。
  </x-card>
  <x-card data-title="国际化支持" data-icon="lucide:globe">
    原生处理国际化域名和 URL。
  </x-card>
  <x-card data-title="会话持久化" data-icon="lucide:cookie">
    使用会话对象，可在多个请求之间保持 Cookie。
  </x-card>
  <x-card data-title="SSL 验证" data-icon="lucide:shield-check">
    具备浏览器风格的 TLS/SSL 验证功能，确保连接安全。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round">
    内置支持基本和摘要式身份验证。
  </x-card>
  <x-card data-title="Cookie 处理" data-icon="lucide:cookie">
    提供类似字典的熟悉接口来管理 Cookie。
  </x-card>
  <x-card data-title="内容解压缩" data-icon="lucide:file-archive">
    自动进行内容解压缩和解码。
  </x-card>
  <x-card data-title="文件上传" data-icon="lucide:upload">
    轻松处理多部分文件上传。
  </x-card>
  <x-card data-title="代理支持" data-icon="lucide:server">
    支持 SOCKS 代理路由请求。
  </x-card>
  <x-card data-title="超时" data-icon="lucide:timer">
    可配置连接超时，防止请求挂起。
  </x-card>
  <x-card data-title="流式传输" data-icon="lucide:download">
    支持大文件的流式下载。
  </x-card>
  <x-card data-title="分块请求" data-icon="lucide:box-select">
    能够发送分块的 HTTP 请求。
  </x-card>
</x-cards>

## 完整文档

如需查阅包括详细 API 参考和高级用法在内的完整指南，请参阅托管在 Read the Docs 上的官方文档。

[![Read the Docs](https://raw.githubusercontent.com/psf/requests/main/ext/ss.png)](https://requests.readthedocs.io)

## 后续步骤

准备好开始了吗？请继续阅读 [入门指南](./getting-started.md)，了解安装说明并发出你的第一个请求。