# 更新日志

本页面直接摘自官方发布历史，详细记录了 Requests 库每个版本的全部变更、改进和错误修复。

## dev

- [重要变更的简短描述。]

### 废弃
- 增加了对 Python 3.14 的支持。
- 因 Python 3.8 支持结束，停止了对其的支持。

## 2.32.4 (2025-06-10)

### 安全性
- CVE-2024-47081 修复了一个问题，即在受信任的环境中，恶意构造的 URL 会从 netrc 文件中检索到错误的主机名/计算机的凭据。

### 改进
- 多项文档改进

### 废弃
- 增加了对 Linux 和 macOS 上 pypy 3.11 的支持。
- 因 pypy 3.9 支持结束，停止了对其的支持。

## 2.32.3 (2024-05-29)

### 错误修复
- 修复了在 HTTPAdapter 的子类中无法指定自定义 SSLContexts 的错误。(＃6716)
- 修复了 Requests 在未使用 `ssl` 模块编译的 Python 版本上运行失败的问题。(＃6724)

## 2.32.2 (2024-05-21)

### 废弃
- 为了给受 2.32.0 版本中 CVE 变更影响的自定义 HTTPAdapters 提供更稳定的迁移路径，我们将 `_get_connection` 重命名为一个新的公共 API `get_connection_with_tls_context`。现有的自定义 HTTPAdapters 需要迁移其代码以使用此新 API。在所有 Requests>=2.32.0 的版本中，`get_connection` 均被视为已废弃。

  我们在关联的 PR 中提供了一个最小（2 行）示例以简化迁移，但我们强烈建议用户评估其自定义适配器是否会受到 CVE-2024-35195 中所描述问题的相同影响。(＃6710)

## 2.32.1 (2024-05-20)

### 错误修复
- 将缺失的测试证书添加到 PyPI 上分发的 sdist 中。

## 2.32.0 (2024-05-20)

### 安全性
- 修复了一个问题：在 Session 的第一个请求中设置 `verify=False` 会导致后续对_同一来源_的请求也忽略证书验证，无论 `verify` 的值如何。(https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56)

### 改进
- `verify=True` 现在会重用一个全局 SSLContext，这应能改善首次请求与后续请求之间的时间差异。当使用 OpenSSL 3.x 构建的 Python 版本时，它还应能最大限度地减少 Windows 系统上的证书加载时间。(＃6667)
- 现在，当 Requests 被重新打包或作为依赖项包含时，支持可选地使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最大限度地减少其依赖项的体积。`Response.text()` 和 `apparent_encoding` API 在这两个库都不存在时将默认使用 `utf-8`。(＃6702)

### 错误修复
- 修复了长度检测中的一个错误，该错误导致在请求的 content-length 中错误地计算了表情符号的长度。(＃6589)
- 修复了 JSONDecodeError 中的反序列化错误。(＃6629)
- 修复了一个错误，即额外的前导 `/`（路径分隔符）可能导致 urllib3 不必要地重新解析请求 URI。(＃6644)

### 废弃
- Requests 已正式增加对 CPython 3.12 的支持 (#6503)
- Requests 已正式增加对 PyPy 3.9 和 3.10 的支持 (#6641)
- Requests 已正式停止对 CPython 3.7 的支持 (#6642)
- Requests 已正式停止对 PyPy 3.7 和 3.8 的支持 (#6641)

### 文档
- 各种拼写错误修复和文档改进。

### 打包
- Requests 已开始采用一些现代化的打包实践。项目的源文件（以前是 `requests`）现在位于 Requests sdist 中的 `src/requests` 目录下。(＃6506)
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这不应影响普通用户，但极旧版本的打包工具可能会在新打包格式下出现问题。

## 2.31.0 (2023-05-22)

### 安全性
- v2.3.0 至 v2.30.0 版本的 Requests 存在一个漏洞，在遵循 HTTPS 重定向时，可能会将 `Proxy-Authorization` 标头转发到目标服务器。

  当代理使用用户信息（`https://user:pass@proxy:8080`）定义时，Requests 会构造一个 `Proxy-Authorization` 标头附加到请求上，用于向代理进行身份验证。

  在 Requests 收到重定向响应的情况下，它之前会错误地重新附加 `Proxy-Authorization` 标头，导致该值通过隧道连接发送到目标服务器。我们*强烈*建议那些依赖在 URL 中定义代理凭据的用户升级到 Requests 2.31.0+ 以防止凭据意外泄露，并在更改完全部署后轮换其代理凭据。

  不使用代理或不通过代理 URL 的用户信息部分提供代理凭据的用户不受此漏洞影响。

  完整细节可在我们的 [Github 安全公告](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 和 [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681) 中阅读。

## 2.30.0 (2023-05-03)

### 依赖项
- ⚠️ 增加了对 urllib3 2.0 的支持。 ⚠️

  这可能包含一些小的破坏性变更，因此我们建议在升级前仔细测试并查阅 https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html。

  希望继续使用 urllib3 1.x 的用户可以将其版本固定为 `urllib3<2`。

## 2.29.0 (2023-04-26)

### 改进
- Requests 现在将分块请求交由 urllib3 实现处理，以提高标准化程度。(＃6226)
- Requests 放宽了对标头组件的要求，以支持 bytes/str 的子类。(＃6356)

## 2.28.2 (2023-01-12)

### 依赖项
- Requests 现在支持 charset_normalizer 3.x。(＃6261)

### 错误修复
- 更新了 MissingSchema 异常，建议使用 https 协议而非 http。(＃6188)

## 2.28.1 (2022-06-29)

### 改进
- 通过过渡到 `yield from` 优化了 `iter_content` 的速度。(＃6170)

### 依赖项
- 增加了对 chardet 5.0.0 的支持 (#6179)
- 增加了对 charset-normalizer 2.1.0 的支持 (#6169)

## 2.28.0 (2022-06-09)

### 废弃
- ⚠️ Requests 已正式停止对 Python 2.7 的支持。 ⚠️ (#6091)
- Requests 已正式停止对 Python 3.6 (包括 pypy3.6) 的支持。(＃6091)

### 改进
- 将无编码的负载的 JSON 解析问题包装在 Request 的 JSONDecodeError 中，以使 `json()` API 保持一致。(＃6097)
- 统一解析标头组件，在所有无效情况下引发 InvalidHeader 错误。(＃6154)
- 增加了对 3.11 的临时支持，基于当前的 beta 构建版本。(＃6155)
- Requests 焕然一新，我们决定用 black 格式化代码。(＃6095)

### 错误修复
- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会禁用证书验证的错误。2.28.0 之前的所有 Requests 2.x 版本都受影响。(＃6074)
- 修复了 urllib3 异常泄漏问题，将 `content` 和 `iter_content` 的 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError`。(＃6057)
- 修复了无效的 Windows 注册表项导致代理解析引发异常而不是忽略该条目的问题。(＃6149)
- 修复了整个负载可能被包含在 JSONDecodeError 错误消息中的问题。(＃6036)

## 2.27.1 (2022-01-05)

### 错误修复
- 修复了导致代理 URL 中的 `auth` 组件被丢弃的解析问题。(＃6028)

## 2.27.0 (2022-01-03)

### 改进
- 正式增加了对 Python 3.10 的支持。(＃5928)
- 添加了 `requests.exceptions.JSONDecodeError` 以统一 Python 2 和 3 之间的 JSON 异常。此异常在 `response.json()` 方法中引发，并且由于它继承自先前抛出的异常，因此是向后兼容的。也可以通过 `requests.exceptions.RequestException` 捕获。(＃5856)
- 改进了命名错误的 `InvalidSchema` 和 `MissingSchema` 异常的错误文本。这是一个临时修复，直到异常可以重命名（Schema->Scheme）。(＃6017)
- 改进了对缺少协议的代理 URL 的解析。这将解决 Python 3.9+ 中 `urlparse` 的近期更改。(＃5917)

### 错误修复
- 修复了 `extract_zipped_paths` 中可能导致某些路径无限循环的缺陷。(＃5851)
- 修复了在计算从 `Tarfile.extractfile()` 获取的文件长度时处理 `AttributeError` 的问题。(＃5239)
- 修复了 urllib3 异常泄漏问题，将 `urllib3.exceptions.InvalidHeader` 包装为 `requests.exceptions.InvalidHeader`。(＃5914)
- 修复了分块请求发送两个 Host 标头的错误。(＃5391)
- 修复了 Requests 2.26.0 中的一个回归问题，即 `Proxy-Authorization` 被错误地从所有使用 `Session.send` 发送的请求中剥离。(＃5924)
- 修复了 2.26.0 版本中对于环境中存在大量可用代理的主机的性能回归问题。(＃5924)
- 修复了 idna 异常泄漏问题，将 `UnicodeError` 包装为 `requests.exceptions.InvalidURL`，用于处理域名中以点（.）开头的 URL。(＃5414)

### 废弃
- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。虽然我们没有确切日期，但 Requests 2.27.x 很可能是提供支持的最后一个发布系列。

## 2.26.0 (2021-07-13)

### 改进
- 如果安装了 `brotli` 或 `brotlicffi` 包，Requests 现在支持 Brotli 压缩。(＃5783)
- `Session.send` 现在可以正确解析来自 Session 和 Request 的代理配置。行为现在与 `Session.request` 一致。(＃5681)

### 错误修复
- 修复了在 zip 归档中并行使用 Requests 时 zip 提取中的竞争条件问题。(＃5707)

### 依赖项
- 对于 Python3，使用 MIT 许可的 `charset_normalizer` 替代 `chardet`，以消除打包 requests 的项目的许可模糊性。如果你的机器上已经安装了 `chardet`，它将被优先使用以保持向后兼容性。(＃5797)

  你也可以在安装 requests 时通过指定 `[use_chardet_on_py3]` extra 来同时安装 `chardet`，如下所示：

  ```shell
  pip install "requests[use_chardet_on_py3]"
  ```

  Python2 仍然依赖于 `chardet` 模块。
- Requests 现在在 Python 3 上支持 `idna` 3.x。`idna` 2.x 将继续在 Python 2 安装中使用。(＃5711)

### 废弃
- `requests[security]` extra 已被转换为空操作安装。PyOpenSSL 不再是 Requests 推荐的安全选项。(＃5867)
- Requests 已正式停止对 Python 3.5 的支持。(＃5867)

## 2.25.1 (2020-12-16)

### 错误修复
- Requests 现在默认将 `application/json` 视为 `utf8`。解决了 `r.text` 和 `r.json` 输出之间的不一致问题。(＃5673)

### 依赖项
- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

### 改进
- 增加了对 NETRC 环境变量的支持。(＃5643)

### 依赖项
- Requests 现在支持 urllib3 v1.26。

### 废弃
- Requests v2.25.x 将是支持 Python 3.5 的最后一个发布系列。
- `requests[security]` extra 已被正式废弃，并将在 Requests v2.26.0 中移除。

## 2.24.0 (2020-06-17)

### 改进
- 只有在 Python 没有 `ssl` 模块或不支持 SNI 的情况下，才会使用 pyOpenSSL TLS 实现。以前，如果 pyOpenSSL 可用，则无条件使用。即使通过 `requests[security]` extra 安装了 pyOpenSSL，此规则也适用 (#5443)
- 现在只有在 `allow_redirects` 为 True 时才会进行重定向解析。(＃5492)
- 不再为不会使用它的请求执行不必要的 Content-Length 计算。(＃5496)

## 2.23.0 (2020-02-19)

### 改进
- 移除了 Session `__attrs__` 中对已失效的 `prefetch` 的引用 (#5110)

### 错误修复
- Requests 不再在基本认证使用警告中输出密码。(＃5099)

### 依赖项
- 对 `chardet` 和 `idna` 的版本固定现在使用主版本号而不是次版本号。希望这能减少每次依赖项更新时都需要发布新版本的需求。

## 2.22.0 (2019-05-15)

### 依赖项
- Requests 现在支持 urllib3 v1.25.2。（注意：1.25.0 和 1.25.1 不兼容）

### 废弃
- Requests 已正式停止对 Python 3.4 的支持。

## 2.21.0 (2018-12-10)

### 依赖项
- Requests 现在支持 idna v2.8。

## 2.20.1 (2018-11-08)

### 错误修复
- 修复了在使用默认端口（http/80, https/443）重定向时意外剥离 Authorization 标头的错误。

## 2.20.0 (2018-10-18)

### 错误修复
- Content-Type 标头解析现在不区分大小写（例如 charset=utf8 与 Charset=utf8）。
- 修复了某些重定向 url 会引发未捕获的 urllib3 异常的泄漏问题。
- 对于从 https 重定向到同一主机名的 http 的请求，Requests 会移除 Authorization 标头。(CVE-2018-18074)
- `should_bypass_proxies` 现在可以处理没有主机名的 URI（例如文件）。

### 依赖项
- Requests 现在支持 urllib3 v1.24。

### 废弃
- Requests 已正式停止对 Python 2.6 的支持。

## 2.19.1 (2018-06-14)

### 错误修复
- 修复了 status_codes.py 的 `init` 函数在尝试附加到 `None` 值的 `__doc__` 时失败的问题。

## 2.19.0 (2018-06-12)

### 改进
- 当使用低于 1.3.4 版本的 cryptography 时，警告用户可能出现的速度减慢问题。
- 在将请求转发到适配器之前，检查代理 URL 中的无效主机。
- 现在可以在重定向中正确保留片段。(RFC7231 7.1.2)
- 移除了对 cgi 模块的使用，以加快库加载时间。
- 增加了对 SHA-256 和 SHA-512 摘要认证算法的支持。
- 对 `Request.content` 进行了轻微的性能改进。
- 迁移到使用 collections.abc 以兼容 3.7 版本。

### 错误修复
- 使用 `parse_header_links()` 解析空的 `Link` 标头不再返回一个虚假的条目。
- 修复了从 zip 归档加载默认证书包时会引发 `IOError` 的问题。
- 修复了在不支持 `winreg` 模块的 Windows 系统上出现意外 `ImportError` 的问题。
- 代理绕过中的 DNS 解析不再在请求中包含用户名和密码。这也修复了 DNS 查询在 macOS 上失败的问题。
- 为 URL 比较正确规范化适配器前缀。
- 将 `None` 作为文件指针传递给 `files` 参数不再引发异常。
- 在 `RequestsCookieJar` 上调用 `copy` 现在将正确保留 cookie 策略。

### 依赖项
- 我们现在支持 idna v2.7。
- 我们现在支持 urllib3 v1.23。
