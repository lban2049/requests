# 高级用法

掌握了发送请求的基础知识后，你经常会遇到需要更复杂配置的实际场景。本节将深入探讨 Requests 的高级功能，让你能够自信地处理复杂的网络行为、安全需求和自定义逻辑。

你将学习如何通过超时和重试来微调网络操作，使用 SSL 证书管理安全连接，甚至通过自定义适配器和钩子扩展库的核心功能。

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-icon="lucide:timer" data-href="/advanced-usage/timeouts-retries-proxies">
    实现对网络操作的精细控制。学习如何设置超时以防止请求无限期挂起，如何自动重试失败的请求以构建弹性应用程序，以及如何出于安全或访问目的通过代理路由流量。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    管理 HTTPS 连接的安全性。本指南涵盖如何使用自定义 CA 证书包，为双向 TLS 身份验证提供客户端证书，以及在何种情况下（需谨慎）可以禁用 SSL 验证。
  </x-card>
  <x-card data-title="自定义适配器和钩子" data-icon="lucide:plug-zap" data-href="/advanced-usage/adapters-and-hooks">
    扩展 Requests 以满足你的独特需求。了解如何创建自定义传输适配器以实现不同的传输协议或连接逻辑，以及如何使用钩子系统注册回调来修改请求或检查响应。
  </x-card>
</x-cards>

掌握这些高级功能后，你就可以构建稳健、安全且高度定制的 HTTP 客户端。当你准备好详细探索每个类和方法时，完整的 API 参考将是你的下一站。

---

**下一步**：[深入了解 API 参考](./api-reference.md)