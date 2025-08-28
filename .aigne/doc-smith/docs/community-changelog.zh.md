# 更新日志

Requests 库每个版本的所有变更、改进和错误修复的详细日志。

## 未发布

- [非重大变更的简短描述。]

**弃用**
- 新增对 Python 3.14 的支持。
- 因 Python 3.8 支持结束，已停止对其的支持。

## 2.32.4 (2025-06-10)

**安全**
- CVE-2024-47081 修复了一个问题，该问题会导致恶意构造的 URL 和受信任的环境从 netrc 文件中检索到错误主机名/计算机的凭据。

**改进**
- 大量文档改进

**弃用**
- 新增对 Linux 和 macOS 上 pypy 3.11 的支持。
- 因 pypy 3.9 支持结束，已停止对其的支持。

## 2.32.3 (2024-05-29)

**错误修复**
- 修复了在 HTTPAdapter 子类中无法指定自定义 SSLContexts 的错误。(#6716)
- 修复了在没有 `ssl` 模块的情况下编译的 Python 版本上 Requests 无法运行的问题。(#6724)

## 2.32.2 (2024-05-21)

**弃用**
- 为了给受 2.32.0 版本中 CVE 变更影响的自定义 HTTPAdapter 提供更稳定的迁移路径，我们将 `_get_connection` 重命名为一个新的公共 API `get_connection_with_tls_context`。现有的自定义 HTTPAdapter 需要迁移其代码以使用此新 API。在所有 Requests>=2.32.0 的版本中，`get_connection` 均被视为已弃用。

  在链接的 PR 中提供了一个最小（2 行）示例以便于迁移，但我们强烈建议用户评估其自定义适配器是否会受到 CVE-2024-35195 中描述的相同问题的影响。(#6710)

## 2.32.1 (2024-05-20)

**错误修复**
- 将缺失的测试证书添加到 PyPI 上分发的 sdist 中。

## 2.32.0 (2024-05-20)

**安全**
- 修复了一个问题：在 Session 的第一个请求中设置 `verify=False` 会导致后续对 _同一来源_ 的请求也忽略证书验证，无论 `verify` 的值如何。(https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56)

**改进**
- `verify=True` 现在会重用一个全局 SSLContext，这应能改善首次请求与后续请求之间的时间差异。当使用基于 OpenSSL 3.x 构建的 Python 版本时，这还应能最大限度地减少 Windows 系统上的证书加载时间。(#6667)
- Requests 现在支持在重新打包或 vendoring 时可选地使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最小化其 vendoring 范围。如果这两个库都不存在，`Response.text()` 和 `apparent_encoding` API 将默认使用 `utf-8`。(#6702)

**错误修复**
- 修复了长度检测中的一个错误，该错误导致请求的 content-length 中 emoji 长度计算不正确。(#6589)
- 修复了 JSONDecodeError 中的反序列化错误。(#6629)
- 修复了一个错误，该错误导致额外的前导 `/`（路径分隔符）可能致使 urllib3 不必要地重新解析请求 URI。(#6644)

**弃用**
- Requests 已正式添加对 CPython 3.12 的支持 (#6503)
- Requests 已正式添加对 PyPy 3.9 和 3.10 的支持 (#6641)
- Requests 已正式停止对 CPython 3.7 的支持 (#6642)
- Requests 已正式停止对 PyPy 3.7 和 3.8 的支持 (#6641)

**文档**
- 各种拼写错误修复和文档改进。

**打包**
- Requests 已开始采用一些现代化的打包实践。项目的源文件（以前是 `requests`）现在位于 Requests sdist 的 `src/requests` 目录中。(#6506)
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这不应影响普通用户，但极旧版本的打包工具可能会与新的打包格式存在兼容性问题。

## 2.31.0 (2023-05-22)

**安全**
- v2.3.0 到 v2.30.0 之间的 Requests 版本在遵循 HTTPS 重定向时，容易将 `Proxy-Authorization` 标头转发到目标服务器。完整详情请参阅我们的 [Github 安全公告](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 和 [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681)。

## 2.30.0 (2023-05-03)

**依赖项**
- ⚠️ 新增对 urllib3 2.0 的支持。⚠️ 这可能包含微小的破坏性变更。希望继续使用 urllib3 1.x 的用户可以固定版本为 `urllib3<2`。

## 2.29.0 (2023-04-26)

**改进**
- Requests 现在将分块请求推迟到 urllib3 实现，以提高标准化程度。(#6226)
- Requests 放宽了对标头组件的要求，以支持 bytes/str 子类。(#6356)

## 2.28.2 (2023-01-12)

**依赖项**
- Requests 现在支持 charset_normalizer 3.x。(#6261)

**错误修复**
- 更新了 MissingSchema 异常，建议使用 https 协议而非 http。(#6188)

## 2.28.1 (2022-06-29)

**改进**
- 通过转换为 `yield from` 优化了 `iter_content` 的速度。(#6170)

**依赖项**
- 新增对 chardet 5.0.0 的支持 (#6179)
- 新增对 charset-normalizer 2.1.0 的支持 (#6169)

## 2.28.0 (2022-06-09)

**弃用**
- ⚠️ Requests 已正式停止对 Python 2.7 的支持。⚠️ (#6091)
- Requests 已正式停止对 Python 3.6 (包括 pypy3.6) 的支持。(#6091)

**改进**
- 对于没有编码的有效负载，将 JSON 解析问题包装在 Request 的 JSONDecodeError 中，以使 `json()` API 保持一致。(#6097)
- 一致地解析标头组件，在所有无效情况下引发 InvalidHeader 错误。(#6154)
- 通过当前的 beta 版本增加了对 3.11 的临时支持。(#6155)
- Requests 焕然一新，我们决定将其涂成黑色。(#6095)

**错误修复**
- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会禁用证书验证的错误。2.28.0 之前的所有 Requests 2.x 版本均受影响。(#6074)
- 修复了 urllib3 异常泄漏问题，为 `content` 和 `iter_content` 将 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError`。(#6057)
- 修复了因无效的 Windows 注册表项导致代理解析引发异常而不是忽略该条目的问题。(#6149)
- 修复了有效负载的全部内容可能被包含在 JSONDecodeError 错误消息中的问题。(#6036)

## 2.27.1 (2022-01-05)

**错误修复**
- 修复了导致代理 URL 中的 `auth` 组件被丢弃的解析问题。(#6028)

## 2.27.0 (2022-01-03)

**改进**
- 正式添加对 Python 3.10 的支持。(#5928)
- 新增 `requests.exceptions.JSONDecodeError` 以统一 Python 2 和 3 之间的 JSON 异常。
- 改进了命名错误的 `InvalidSchema` 和 `MissingSchema` 异常的错误文本。
- 改进了缺少协议的代理 URL 的代理解析。

**错误修复**
- 修复了 `extract_zipped_paths` 中的一个缺陷，该缺陷可能导致某些路径出现无限循环。(#5851)
- 修复了计算文件长度时处理 `AttributeError` 的问题。(#5239)
- 修复了 `InvalidHeader` 的 urllib3 异常泄漏问题。(#5914)
- 修复了分块请求发送两个 Host 标头的错误。(#5391)
- 修复了 `Proxy-Authorization` 被错误移除的回归问题。(#5924)
- 修复了具有大量代理的主机的性能回归问题。(#5924)
- 修复了域名中带有前导点的 URL 的 idna 异常泄漏问题。(#5414)

**弃用**
- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。

## 2.26.0 (2021-07-13)

**改进**
- 如果安装了 `brotli` 或 `brotlicffi`，Requests 现在支持 Brotli 压缩。(#5783)
- `Session.send` 现在可以正确解析代理配置。(#5681)

**错误修复**
- 修复了在并行使用 Requests 时 zip 提取中的竞争条件。(#5707)

**依赖项**
- Python 3 使用 `charset_normalizer` 代替 `chardet`。Python 2 仍然依赖 `chardet`。
- Requests 现在在 Python 3 上支持 `idna` 3.x。

**弃用**
- `requests[security]` 附加功能已变为空操作安装。
- Requests 已正式停止对 Python 3.5 的支持。(#5867)

## 2.25.1 (2020-12-16)

**错误修复**
- Requests 现在默认将 `application/json` 视为 `utf8`。(#5673)

**依赖项**
- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

**改进**
- 新增对 NETRC 环境变量的支持。(#5643)

**依赖项**
- Requests 现在支持 urllib3 v1.26。

**弃用**
- Requests v2.25.x 将是支持 Python 3.5 的最后一个发布系列。
- `requests[security]` 附加功能已正式弃用，并将在 Requests v2.26.0 中移除。

## 2.24.0 (2020-06-17)

**改进**
- 只有当 Python 没有 `ssl` 模块或不支持 SNI 时，才会使用 pyOpenSSL TLS 实现。
- 现在只有在 `allow_redirects` 为 True 时才会进行重定向解析。
- 对于不会使用 Content-Length 的请求，不再执行不必要的内容长度计算。

## 2.23.0 (2020-02-19)

**改进**
- 移除了 Session `__attrs__` 中对已失效的 `prefetch` 的引用 (#5110)

**错误修复**
- Requests 不再在基本认证使用警告中输出密码。(#5099)

**依赖项**
- 对 `chardet` 和 `idna` 的版本固定现在使用主版本号而不是次版本号。

## 2.22.0 (2019-05-15)

**依赖项**
- Requests 现在支持 urllib3 v1.25.2。（注意：1.25.0 和 1.25.1 不兼容）

**弃用**
- Requests 已正式停止对 Python 3.4 的支持。

## 2.21.0 (2018-12-10)

**依赖项**
- Requests 现在支持 idna v2.8。

## 2.20.1 (2018-11-08)

**错误修复**
- 修复了在使用默认端口（http/80, https/443）重定向时意外剥离 Authorization 标头的错误。

## 2.20.0 (2018-10-18)

**错误修复**
- Content-Type 标头解析现在不区分大小写。
- 修复了某些重定向 URL 会引发未捕获的 urllib3 异常的异常泄漏问题。
- 对于从同一主机的 https 重定向到 http 的请求，Requests 会移除 Authorization 标头。(CVE-2018-18074)
- `should_bypass_proxies` 现在可以处理没有主机名的 URI（例如文件）。

**依赖项**
- Requests 现在支持 urllib3 v1.24。

**弃用**
- Requests 已正式停止对 Python 2.6 的支持。

... 以此类推，适用于所有早期版本。