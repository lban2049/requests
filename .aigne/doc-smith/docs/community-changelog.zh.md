# 更新日志

本页面详细记录了 Requests 库每个版本的全部变更、改进和错误修复。

## dev

- [非重大变更的简短描述。]

### 弃用
- 新增对 Python 3.14 的支持。
- 因 Python 3.8 停止支持，故放弃对其的支持。

## 2.32.4 (2025-06-10)

### 安全性
- **CVE-2024-47081** 修复了在受信任的环境下，恶意构造的 URL 会从 netrc 文件中检索到错误主机名/机器凭据的问题。

### 改进
- 大量的文档改进。

### 弃用
- 新增对 Linux 和 macOS 平台下 pypy 3.11 的支持。
- 因 pypy 3.9 停止支持，故放弃对其的支持。

## 2.32.3 (2024-05-29)

### 错误修复
- 修复了在 HTTPAdapter 子类中无法指定自定义 SSLContext 的错误。(#6716)
- 修复了 Requests 在未使用 `ssl` 模块编译的 Python 版本上无法运行的问题。(#6724)

## 2.32.2 (2024-05-21)

### 弃用
- 为了给受 2.32.0 版本中 CVE 变更影响的自定义 HTTPAdapters 提供更稳定的迁移路径，我们将 `_get_connection` 重命名为一个新的公共 API `get_connection_with_tls_context`。现有的自定义 HTTPAdapters 需要迁移其代码以使用此新 API。在所有 Requests>=2.32.0 的版本中，`get_connection` 均被视为已弃用。

  在关联的 PR 中提供了一个最小（2 行）示例以简化迁移，但我们强烈建议用户评估其自定义适配器是否会受到 CVE-2024-35195 中描述的相同问题的影响。(#6710)

## 2.32.1 (2024-05-20)

### 错误修复
- 将缺失的测试证书添加到在 PyPI 上分发的 sdist 中。

## 2.32.0 (2024-05-20)

### 安全性
- 修复了在一个会话（Session）的首次请求中设置 `verify=False` 会导致后续对_同一来源_的请求也忽略证书验证的问题，无论 `verify` 的值如何。([GHSA-9wx4-h78v-vm56](https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56))

### 改进
- `verify=True` 现在会重用一个全局 SSLContext，这应能改善首次请求与后续请求之间的时间差异。当使用基于 OpenSSL 3.x 构建的 Python 版本时，这也应能最大限度地减少 Windows 系统上的证书加载时间。(#6667)
- Requests 现在支持在重新打包或内置时可选地使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最大限度地减少其内置范围。如果这两个库都不存在，`Response.text()` 和 `apparent_encoding` API 将默认使用 `utf-8`。(#6702)

### 错误修复
- 修复了长度检测中的一个错误，该错误导致请求内容长度中表情符号的长度计算不正确。(#6589)
- 修复了 JSONDecodeError 中的反序列化错误。(#6629)
- 修复了因额外的前导 `/`（路径分隔符）可能导致 urllib3 不必要地重新解析请求 URI 的错误。(#6644)

### 弃用
- Requests 已正式添加对 CPython 3.12 的支持 (#6503)
- Requests 已正式添加对 PyPy 3.9 和 3.10 的支持 (#6641)
- Requests 已正式放弃对 CPython 3.7 的支持 (#6642)
- Requests 已正式放弃对 PyPy 3.7 和 3.8 的支持 (#6641)

### 文档
- 各种拼写错误修复和文档改进。

### 打包
- Requests 已开始采用一些现代化的打包实践。项目的源文件（以前是 `requests`）现在位于 Requests sdist 的 `src/requests` 目录中。(#6506)
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这不应影响普通用户，但极旧版本的打包工具可能会在新打包格式下出现问题。

## 2.31.0 (2023-05-22)

### 安全性
- v2.3.0 到 v2.30.0 之间的 Requests 版本在处理 HTTPS 重定向时，容易受到 `Proxy-Authorization` 标头可能被转发到目标服务器的攻击。完整详情可在我们的 [Github 安全公告](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 和 [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681) 中阅读。

## 2.30.0 (2023-05-03)

### 依赖项
- ⚠️ 新增对 urllib3 2.0 的支持。⚠️ 这可能包含微小的破坏性变更。我们建议您查阅 [urllib3 v2 迁移指南](https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html)。希望继续使用 urllib3 1.x 的用户可以固定版本为 `urllib3<2`。

## 2.29.0 (2023-04-26)

### 改进
- Requests 现在将分块请求委托给 urllib3 实现，以提高标准化程度。(#6226)
- Requests 放宽了对标头组件的要求，以支持 bytes/str 子类。(#6356)

## 2.28.2 (2023-01-12)

### 依赖项
- Requests 现在支持 charset_normalizer 3.x。(#6261)

### 错误修复
- 更新了 MissingSchema 异常，建议使用 https 协议而非 http。(#6188)

## 2.28.1 (2022-06-29)

### 改进
- 通过过渡到 `yield from`，优化了 `iter_content` 的速度。(#6170)

### 依赖项
- 新增对 chardet 5.0.0 的支持 (#6179)
- 新增对 charset-normalizer 2.1.0 的支持 (#6169)

## 2.28.0 (2022-06-09)

### 弃用
- ⚠️ Requests 已正式放弃对 Python 2.7 的支持。⚠️ (#6091)
- Requests 已正式放弃对 Python 3.6 (包括 pypy3.6) 的支持。(#6091)

### 改进
- 对于没有编码的有效负载，将 JSON 解析问题包装在 Request 的 JSONDecodeError 中，以使 `json()` API 保持一致。(#6097)
- 统一解析标头组件，在所有无效情况下引发 InvalidHeader 错误。(#6154)
- 根据当前的 beta 构建版本，添加了对 3.11 的临时支持。(#6155)
- Requests 进行了改造，我们决定使用 black 格式化工具进行代码格式化。(#6095)

### 错误修复
- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会禁用证书验证的错误。(#6074)
- 修复了 urllib3 异常泄漏问题，将 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError`，适用于 `content` 和 `iter_content`。(#6057)
- 修复了无效的 Windows 注册表条目导致代理解析引发异常的问题。(#6149)
- 修复了整个有效负载可能被包含在 JSONDecodeError 错误消息中的问题。(#6036)

## 2.27.1 (2022-01-05)

### 错误修复
- 修复了导致代理 URL 中 `auth` 组件被丢弃的解析问题。(#6028)

## 2.27.0 (2022-01-03)

### 改进
- 正式添加对 Python 3.10 的支持。(#5928)
- 添加了 `requests.exceptions.JSONDecodeError` 以统一 JSON 异常。(#5856)
- 改进了 `InvalidSchema` 和 `MissingSchema` 异常命名错误的错误文本。(#6017)
- 改进了对缺少协议的代理 URL 的代理解析。(#5917)

### 错误修复
- 修复了 `extract_zipped_paths` 中可能导致无限循环的缺陷。(#5851)
- 修复了从 `Tarfile.extractfile()` 计算文件长度时处理 `AttributeError` 的问题。(#5239)
- 修复了 urllib3 异常泄漏问题，包装了 `urllib3.exceptions.InvalidHeader`。(#5914)
- 修复了分块请求发送两个 Host 标头的错误。(#5391)
- 修复了 `Proxy-Authorization` 被错误地从请求中剥离的回归问题。(#5924)
- 修复了具有大量代理的主机的性能回归问题。(#5924)
- 修复了 idna 异常泄漏问题，将 `UnicodeError` 包装为 `requests.exceptions.InvalidURL`。(#5414)

### 弃用
- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。Requests 2.27.x 很可能是提供支持的最后一个发布系列。

## 2.26.0 (2021-07-13)

### 改进
- 如果安装了 `brotli` 或 `brotlicffi`，Requests 现在支持 Brotli 压缩。(#5783)
- `Session.send` 现在能正确解析代理配置。(#5681)

### 错误修复
- 修复了在并行使用 Requests 时 zip 解压中的一个竞争条件。(#5707)

### 依赖项
- 对于 Python 3，使用 `charset_normalizer` 代替 `chardet`。如果 `chardet` 已安装，为了向后兼容性将继续使用它。(#5797)
- Requests 现在在 Python 3 上支持 `idna` 3.x。(#5711)

### 弃用
- `requests[security]` 附加功能已变为空操作安装。不再推荐使用 PyOpenSSL。(#5867)
- Requests 已正式放弃对 Python 3.5 的支持。(#5867)

## 2.25.1 (2020-12-16)

### 错误修复
- Requests 现在默认将 `application/json` 视为 `utf8`。(#5673)

### 依赖项
- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

### 改进
- 添加了对 NETRC 环境变量的支持。(#5643)

### 依赖项
- Requests 现在支持 urllib3 v1.26。

### 弃用
- Requests v2.25.x 将是支持 Python 3.5 的最后一个发布系列。
- `requests[security]` 附加功能已被正式弃用，并将在 Requests v2.26.0 中移除。

## 2.24.0 (2020-06-17)

### 改进
- 现在仅在 Python 缺少 `ssl` 模块或 SNI 支持时才使用 pyOpenSSL TLS 实现。(#5443)
- 重定向解析现在应仅在 `allow_redirects` 为 True 时发生。(#5492)
- 不再执行不必要的 Content-Length 计算。(#5496)

## 2.23.0 (2020-02-19)

### 改进
- 移除了 Session `__attrs__` 中对 `prefetch` 的失效引用。(#5110)

### 错误修复
- Requests 不再在基本认证使用警告中输出密码。(#5099)

### 依赖项
- 对 `chardet` 和 `idna` 的版本锁定现在使用主版本号而非次版本号。

## 2.22.0 (2019-05-15)

### 依赖项
- Requests 现在支持 urllib3 v1.25.2。（注意：1.25.0 和 1.25.1 不兼容）

### 弃用
- Requests 已正式停止对 Python 3.4 的支持。

## 2.21.0 (2018-12-10)

### 依赖项
- Requests 现在支持 idna v2.8。

## 2.20.1 (2018-11-08)

### 错误修复
- 修复了在使用默认端口重定向时意外剥离 Authorization 标头的错误。

## 2.20.0 (2018-10-18)

### 错误修复
- Content-Type 标头解析现在不区分大小写。
- 修复了某些重定向 URL 会引发未捕获的 urllib3 异常的泄漏问题。
- 对于从 https 重定向到同一主机名的 http 请求，Requests 会移除 Authorization 标头。(CVE-2018-18074)
- `should_bypass_proxies` 现在可以处理没有主机名的 URI。

### 依赖项
- Requests 现在支持 urllib3 v1.24。

### 弃用
- Requests 已正式停止对 Python 2.6 的支持。

## 2.19.1 (2018-06-14)

### 错误修复
- 修复了 status_codes.py 的 `init` 函数失败的问题。

## 2.19.0 (2018-06-12)

### 改进
- 当使用低于 1.3.4 版本的 cryptography 时，向用户发出可能减慢速度的警告。
- 检查代理 URL 中的无效主机。
- 片段现在可以在重定向中正确保留。
- 添加了对 SHA-256 和 SHA-512 摘要式身份验证算法的支持。

### 错误修复
- 解析空的 `Link` 标头不再返回虚假条目。
- 修复了从 zip 归档文件加载默认证书包时的 `IOError`。
- 正确规范化适配器前缀以便进行 URL 比较。

## 2.18.4 (2017-08-15)

### 改进
- 无效标头的错误消息现在包含标头名称。

### 依赖项
- 我们现在支持 idna v2.6。

## 2.13.0 (2017-01-24)

### 功能
- 仅在确定需要时才加载 `idna` 库。

### 其他
- 将捆绑的 urllib3 更新至 1.20。
- 将捆绑的 idna 更新至 2.2。

## 2.12.0 (2016-11-15)

### 改进
- 将对国际化域名的支持从 IDNA2003 更新至 IDNA2008。
- 改进了猜测内容长度的启发式方法。
- Requests 现在容忍代理凭据中的空密码。

### 错误修复
- 当调用 `response.close` 时，该调用会传播到非 urllib3 后端。
- 修复了 `ALL_PROXY` 会优先于特定协议变量的问题。

## 2.11.0 (2016-08-08)

### 改进
- 添加了对 `ALL_PROXY` 环境变量的支持。
- 拒绝包含前导空格或换行符的标头值。

### 错误修复
- 修复了在错误情况下解码 JSON 响应时偶尔出现的 `TypeError`。
- 修复了发送 JSON 数据时可能导致模糊的 OpenSSL 错误的错误。

## 2.10.0 (2016-04-29)

### 新功能
- SOCKS 代理支持！（需要 PySocks）

## 2.9.1 (2015-12-21)

### 错误修复
- 解决了在 Python 3 中无法将二进制字符串作为请求体发送的回归问题。
- 修复了在某些区域设置中计算 cookie 过期日期时的错误。

## 2.9.0 (2015-12-15)

### 小幅改进
- `verify` 关键字参数现在支持指向 CA 证书目录的路径。
- 发送以文本模式打开的文件时会发出警告。

### 错误修复
- 我们现在发送实际读取的字节数作为内容长度，允许部分文件上传。
- 使用函数式 API 时，会话在所有情况下都会被关闭。

## 2.8.0 (2015-10-05)

### 小幅改进
- Requests 现在支持按主机设置代理。
- `Response.raise_for_status` 现在会打印失败的 URL。

### 错误修复
- 仅当 `data` 和 `files` 都不存在时，才会使用 `json` 参数。
- 我们现在忽略 `NO_PROXY` 环境变量中的空字段。

## 2.7.0 (2015-05-03)

### 错误修复
- 将 urllib3 更新至 1.10.4，解决了几个涉及分块传输编码的错误。

## 2.6.0 (2015-03-14)

### 错误修复
- **CVE-2015-2296**：修复重定向时处理 cookie 的方式，以防止会话固定攻击。

### 功能和改进
- 在 `files` 参数中支持 bytearray。

## 2.5.2 (2015-02-23)

### 功能和改进
- 添加 sha256 指纹支持。

### 安全性
- 引入了更新的 `cacert.pem`。
- 从默认密码套件列表中移除 RC4。

## 2.4.2 (2014-10-05)

### 改进
- 为上传添加了 `json` 参数。
- 在 Python 3.x 上支持字节字符串 URL。

## 2.4.0 (2014-08-29)

### 行为变更
- `Connection: keep-alive` 标头现在会自动发送。

### 改进
- 支持连接超时！Timeout 现在接受一个元组 (connect, read)。

## 2.3.0 (2014-05-16)

### API 变更
- 新的 `Response` 属性 `is_redirect`。
- `timeout` 参数现在对 `stream=True` 和 `stream=False` 的请求具有同等影响。

### 错误修复
- 重定向时不再暴露 Authorization 或 Proxy-Authorization 标头。(CVE-2014-1829, CVE-2014-1830)。

## 2.0.0 (2013-09-24)

### API 变更
- Headers 字典中的键现在在所有 Python 版本上都是原生字符串。
- 代理 URL 现在*必须*有明确的协议。
- `RequestException` 现在是 `IOError` 的子类。

### 错误修复
- 大大改进了代理支持，包括 CONNECT 动词。
- 收到 401 认证响应时，现在能正确管理 Cookie。

## 1.2.0 (2013-03-31)

- 重大的代理功能改进，包括从代理 URL 解析代理认证。
- 向 `Response` 对象添加 `elapsed` 属性，以计时请求花费的时间。

## 1.1.0 (2013-01-10)

- 分块请求。
- 支持可迭代的响应体。

## 1.0.0 (2012-12-17)

- 大规模重构和简化。
- 切换到 Apache 2.0 许可证。
- 可插拔和可挂载的连接适配器。
- 移除了除 'response' 之外的所有钩子。

## 0.14.0 (2012-09-02)

- 如果已下载，不再出现 iter_content 错误。

## 0.13.4 (2012-07-27)

- GSSAPI/Kerberos 认证！

## 0.12.0 (2012-05-02)

- 实验性的 OAUTH 支持！
- 基于 Proper CookieJar 的 cookie 接口。

## 0.10.1 (2012-01-23)

- 支持 PYTHON 3！
- 放弃了对 2.5 的支持。（*向后不兼容*）

## 0.10.0 (2012-01-21)

- `Response.content` 现在仅为字节。（*向后不兼容*）
- 新的 `Response.text` 仅为 unicode。

## 0.8.8 (2011-12-28)

- SSL 证书验证！
- 为 SSL 请求新增 'verify' 参数。

## 0.8.0 (2011-11-13)

- Keep-alive 支持！
- 完全移除 Urllib2。

## 0.6.3 (2011-10-13)

- 精美的 `requests.async` 模块，用于通过 gevent 发出异步请求。

## 0.6.0 (2011-08-17)

- 新的持久化会话对象和上下文管理器。
- 透明的字典式 cookie 处理。

## 0.5.1 (2011-07-23)

- 国际化域名支持！

## 0.5.0 (2011-06-21)

- PATCH 支持。
- 支持代理。

## 0.2.0 (2011-02-14)

- 诞生！

## 0.0.1 (2011-02-13)

- 挫败。
- 构想。