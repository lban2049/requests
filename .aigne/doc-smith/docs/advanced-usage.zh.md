# 高级用法

掌握基础知识后，您可能需要处理更复杂的网络场景。本节将介绍 Requests 库的高级功能，让您可以微调 HTTP 交互，以提高性能、可靠性和安全性。

您将学习如何管理网络超时、自动重试失败的请求、通过代理路由流量、自定义 SSL 证书验证，甚至通过自定义适配器和钩子来扩展 Requests 的核心功能。

浏览以下主题，以更深入地控制您的 HTTP 请求。

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    通过设置超时来防止请求挂起、为暂时性网络故障配置自动重试，以及通过 HTTP 或 SOCKS 代理路由请求，从而控制网络行为。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:lock" data-href="/advanced-usage/ssl-cert-verification">
    管理 Requests 处理 SSL/TLS 证书的方式。学习使用自定义证书颁发机构 (CA) 证书包、为双向 TLS 身份验证提供客户端证书，或在必要时禁用验证。
  </x-card>
  <x-card data-title="自定义适配器和钩子" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    扩展 Requests 以满足特殊需求。创建自定义传输适配器来处理不同的传输协议或修改连接逻辑，并使用内置的钩子系统来检查和修改响应对象。
  </x-card>
</x-cards>

这些高级功能提供了构建稳健可靠的应用程序所需的灵活性。在了解这些主题后，您可以查阅详细的 [API 参考](./api-reference.md)，以全面了解所有可用的类和方法。