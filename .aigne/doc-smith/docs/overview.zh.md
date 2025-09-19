# 概述

**Requests** 是一个为人类设计的、优雅而简洁的 Python HTTP 库。它让你能极其轻松地发送 HTTP/1.1 请求，将手动处理查询字符串、表单编码和连接管理的复杂性抽象出来。

作为下载量最大的 Python 包之一，Requests 每周的下载量约为 `3000 万次`，并且是 GitHub 上超过 `1,000,000` 个公共仓库的依赖项。这段代码值得你信赖。

### 快速入门

以下是一个使用 Requests 执行带基本身份验证的 GET 请求并检查响应的快速示例：

```python Basic GET Request icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# 检查状态码
print(r.status_code)
# >>> 200

# 检查响应头
print(r.headers['content-type'])
# >>> 'application/json; charset=utf8'

# 以文本形式访问响应体
print(r.text)
# >>> '{"authenticated": true, ...}'

# 或者将其自动解码为 JSON
print(r.json())
# >>> {'authenticated': True, ...}
```

### 核心功能

Requests 能够满足构建稳健可靠的 HTTP 通信应用程序的需求。它开箱即用，功能丰富。

<x-cards data-columns="2">
  <x-card data-title="Keep-Alive 与连接池" data-icon="lucide:network">
    复用底层 TCP 连接以提升性能。
  </x-card>
  <x-card data-title="国际化域名和 URL" data-icon="lucide:globe">
    原生支持国际化域名，适用于全球应用程序。
  </x-card>
  <x-card data-title="带 Cookie 持久化的会话" data-icon="lucide:cookie">
    会话对象可在所有请求之间持久化参数和 Cookie。
  </x-card>
  <x-card data-title="浏览器风格的 SSL 验证" data-icon="lucide:shield-check">
    像网络浏览器一样，自动验证主机的 SSL 证书。
  </x-card>
  <x-card data-title="内置身份验证" data-icon="lucide:key-round">
    为基本和摘要式身份验证提供优雅的内置支持。
  </x-card>
  <x-card data-title="自动解压" data-icon="lucide:folder-up">
    自动解压并解码响应内容（例如 gzip）。
  </x-card>
  <x-card data-title="文件上传" data-icon="lucide:upload">
    使用 multipart 编码轻松上传文件。
  </x-card>
  <x-card data-title="连接超时" data-icon="lucide:timer-off">
    设置超时以防止请求无限期挂起。
  </x-card>
</x-cards>

### 完整文档

完整的 API 参考和用户指南可在 [Read the Docs](https://requests.readthedocs.io) 上获取，其中对所有功能都提供了深入的解释。

![Requests 在 Read the Docs 上的文档](../../../ext/ss.png)

### 后续步骤

准备好将 Requests 集成到你的项目中了吗？让我们开始安装吧。

<x-card data-title="安装" data-icon="lucide:package-plus" data-href="/installation" data-cta="安装 Requests">
  指导你如何安装该库，并详细说明其支持的 Python 版本。
</x-card>