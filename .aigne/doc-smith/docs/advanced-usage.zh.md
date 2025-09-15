# 高级用法

Requests 虽以其“为人类设计的 HTTP”的简单性而闻名，但也提供了一套强大的高级功能，用于处理复杂和严苛的场景。掌握[用户指南](./user-guide.md)中的基础知识后，你可以进一步深入，以实现对网络行为、安全性和可扩展性的精细控制。

本节提供了这些高级主题的概要介绍以及详细指南的链接。你将学习如何构建更具弹性、更安全、更定制化的 HTTP 客户端。

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-icon="lucide:timer" data-href="/advanced-usage/timeouts-retries-proxies">
    通过设置请求超时、配置自动重试以及通过代理服务器路由请求，保护你的应用程序免受不可靠网络状况的影响。
  </x-card>
  <x-card data-title="SSL 证书验证" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    通过管理 SSL/TLS 验证、使用自定义 CA 证书包以及提供客户端证书，全面掌控应用程序的安全性。
  </x-card>
  <x-card data-title="自定义适配器和钩子" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    通过为不同传输协议创建自定义传输适配器，并利用事件钩子系统修改请求周期，来扩展 Requests 的核心功能。
  </x-card>
</x-cards>

### 示例：使用自定义重试的适配器

一个常见的高级用例是配置 `Session`，使其能够自动重试因瞬时网络问题而失败的请求。这可以通过创建一个带有自定义重试策略的 `HTTPAdapter` 来实现。

```python title="挂载带重试功能的适配器" icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()

# 为特定的 HTTP 状态码和方法定义重试策略
retry_strategy = Retry(
    total=3,
    status_forcelist=[429, 500, 502, 503, 504],
    allowed_methods=["HEAD", "GET", "OPTIONS"]
)

# 创建一个带有此重试策略的适配器并将其挂载到会话中
adapter = HTTPAdapter(max_retries=retry_strategy)
session.mount("https://", adapter)
session.mount("http://", adapter)

try:
    # 现在，使用此会话发出的任何请求都将使用该重试策略
    response = session.get("https://api.example.com/data")
    print("请求成功！")
except requests.exceptions.RequestException as e:
    print(f"多次重试后请求失败: {e}")
```

这个例子仅仅是冰山一角。请浏览详细指南，以充分利用 Requests 的强大功能。

### 后续步骤

探索完这些主题后，你可以查阅完整的 [API 参考](./api-reference.md)，以全面了解所有可用的类和方法。