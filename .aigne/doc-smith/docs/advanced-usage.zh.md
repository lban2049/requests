# 高级用法

Requests 虽然以其简洁性而著称，但也提供了强大的工具来应对复杂的 HTTP 场景。当你需要超越标准请求时，你可以对网络行为进行精细控制。这包括设置精确的超时、为不可靠的连接实施稳健的重试策略、通过代理路由流量、管理 SSL/TLS 证书验证，甚至通过自定义传输适配器和事件钩子扩展库的核心功能。

本节提供了这些高级功能的高级概述和详细指南链接，帮助你构建更具弹性和更复杂的应用程序。

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    了解如何通过设置请求超时、对失败的连接进行自动重试以及通过代理路由请求来配置网络行为。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    通过使用自定义 CA 捆绑包、客户端证书或在特定情况下禁用验证来管理 SSL/TLS 验证。
  </x-card>
  <x-card data-title="自定义适配器和钩子" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    通过创建自定义传输适配器和使用事件钩子系统来修改行为，从而扩展 Requests 的功能。
  </x-card>
</x-cards>

掌握这些功能将使你能够应对几乎任何 HTTP 通信挑战。一旦你熟悉了这些概念，就可以深入了解 [API 参考](./api-reference.md)，其中全面解析了库中可用的每个类、方法和函数。