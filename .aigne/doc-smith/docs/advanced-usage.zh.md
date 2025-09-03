# 高级用法

Requests 因其在处理常见 HTTP 任务方面的简洁性而广受好评，但它也为更复杂、更严苛的场景提供了一系列强大的功能。本节将深入探讨这些高级功能，它们能让您对网络行为、安全协议以及库的核心功能进行精细控制。

无论您需要配置特定的网络计时、管理 SSL 证书，还是使用自定义逻辑扩展 Requests，都有相应的工具可用。以下是本指南涵盖的高级主题概览。

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    了解如何控制连接超时、自动重试失败的请求以及通过代理服务器路由流量。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    管理 SSL/TLS 验证、使用自定义证书颁发机构 (CA) 捆绑包以及提供客户端证书。
  </x-card>
  <x-card data-title="自定义适配器和挂钩" data-icon="lucide:plug-zap" data-href="/advanced-usage/adapters-and-hooks">
    通过创建自定义传输适配器和使用事件挂钩系统来修改请求行为，从而扩展 Requests 的功能。
  </x-card>
</x-cards>

## 超时、重试和代理

网络状况可能难以预测。Requests 允许您通过为请求设置 `timeout` 来防止应用程序无限期挂起。您可以为连接服务器和等待响应分别指定超时时间。

为处理瞬态网络错误，您可以配置 Requests 自动重试失败的请求。这可以通过将带有自定义 `Retry` 策略的 `HTTPAdapter` 挂载到 `Session` 对象上来实现。

此外，如果您需要通过中间方路由请求，Requests 支持 HTTP 和 SOCKS 代理。您可以基于单个请求或整个 `Session` 来配置代理。

有关这些功能的详细指南，请参阅 [超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md)。

## SSL 证书验证

安全是网络通信中的首要考虑因素。默认情况下，Requests 会验证 HTTPS 请求的 SSL 证书，以确保您与预期的服务器通信。您可以通过传递 `verify` 参数来自定义此行为。该参数可以设置为布尔值以启用或禁用验证，也可以设置为自定义 CA 证书包文件或目录的字符串路径。

对于需要客户端证书身份验证 (mTLS) 的服务，您可以使用 `cert` 参数提供证书。

在 [SSL 证书验证](./advanced-usage-ssl-cert-verification.md) 部分探索这些安全配置。

## 自定义适配器和挂钩

Requests 采用模块化设计，支持高度自定义。传输适配器是该系统的核心，为处理 HTTP 和 HTTPS 请求提供逻辑。您可以创建自己的传输适配器以实现自定义传输协议或修改连接的管理方式。

Requests 还提供了一个挂钩系统，允许您将回调函数附加到请求-响应周期中的特定点。可用的主要挂钩是 `response`，它允许您在响应对象从初始请求调用返回之前对其进行检查或修改。

在 [自定义适配器和挂钩](./advanced-usage-adapters-and-hooks.md) 中了解如何根据您的特定需求扩展 Requests。

---

通过掌握这些高级功能，您可以使 Requests 适应各种复杂的网络任务。要获取所有类和方法的完整说明，请继续阅读 [API 参考](./api-reference.md)。