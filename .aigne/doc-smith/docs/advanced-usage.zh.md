# 高级用法

虽然 Requests 简洁的函数式 API 非常适合基础的 HTTP 任务，但真实世界的应用程序通常需要更强的控制力、更高的性能和更好的鲁棒性。Requests 的高级功能提供了构建复杂的 HTTP 客户端所需的工具，这些客户端能够优雅地处理持久连接、复杂的身份验证方案以及网络故障。

本节将探讨这些强大的功能。通过掌握它们，您可以优化应用程序的网络性能，在多个请求之间保持状态，并编写出能够预测和处理潜在问题的弹性代码。

<x-cards data-columns="2">
  <x-card data-title="会话对象" data-icon="lucide:book-copy" data-href="/advanced-usage/session-objects">
    学习如何使用会话对象在多个请求之间持久化 Cookie 和设置，并从连接池中获益以显著提升性能。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round" data-href="/advanced-usage/authentication">
    深入了解各种身份验证机制，包括对基本和摘要认证的内置支持，并学习如何实现您自己的自定义身份验证方案。
  </x-card>
  <x-card data-title="代理" data-icon="lucide:server" data-href="/advanced-usage/proxies">
    了解如何通过代理服务器路由您的 HTTP 和 HTTPS 请求，这是企业环境和网络爬虫任务的常见需求。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-verification">
    了解 Requests 如何处理 SSL 证书验证以确保安全连接，以及如何使用自定义 CA 包或客户端证书。
  </x-card>
  <x-card data-title="超时" data-icon="lucide:timer" data-href="/advanced-usage/timeouts">
    通过设置连接和读取超时来防止您的应用程序无限期挂起，确保在服务器无响应时网络请求能够快速失败。
  </x-card>
  <x-card data-title="错误处理" data-icon="lucide:alert-triangle" data-href="/advanced-usage/error-handling">
    探索 Requests 中全面的异常层次结构。学习捕获和处理特定的网络、协议和超时错误，以构建具有弹性的应用程序。
  </x-card>
</x-cards>

通过利用这些高级功能，您可以超越简单的脚本，构建能够可靠、高效地与 Web 服务交互的专业级应用程序。熟悉这些概念后，您可能需要查阅 [API 参考](./api-reference.md)，以获取所有可用类和方法的详细说明。