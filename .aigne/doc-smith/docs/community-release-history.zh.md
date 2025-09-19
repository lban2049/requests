# 发布历史

本文档详细介绍了 Requests 库的完整发布历史，涵盖了所有新功能、改进、错误修复和安全补丁。它作为该库演变的全面日志。

## dev

- [非重要变更的简短描述。]

**弃用**
- 增加了对 Python 3.14 的支持。
- 因 Python 3.8 支持结束，放弃了对其的支持。

## 2.32.4 (2025-06-10)

**安全**
- CVE-2024-47081 修复了一个问题，该问题会导致在恶意制作的 URL 和受信任的环境下，从 netrc 文件中检索到错误的主机名/机器的凭据。

**改进**
- 多项文档改进。

**弃用**
- 增加了对 Linux 和 macOS 上 pypy 3.11 的支持。
- 因 pypy 3.9 支持结束，放弃了对其的支持。


## 2.32.3 (2024-05-29)

**错误修复**
- 修复了在 HTTPAdapter 的子类中无法指定自定义 SSLContexts 的问题。(＃6716)
- 修复了 Requests 在未使用 `ssl` 模块编译的 Python 版本上无法运行的问题。(＃6724)

## 2.32.2 (2024-05-21)

**弃用**
- 为了给受 2.32.0 版本中 CVE 变更影响的自定义 HTTPAdapters 提供更稳定的迁移路径，我们将 `_get_connection` 重命名为一个新的公共 API：`get_connection_with_tls_context`。现有的自定义 HTTPAdapters 需要迁移其代码以使用此新 API。在所有 Requests>=2.32.0 的版本中，`get_connection` 已被视为弃用。

  在链接的 PR 中提供了一个最小（2 行）示例以便于迁移，但我们强烈建议用户评估其自定义适配器是否会受到 CVE-2024-35195 中描述的相同问题的影响。(＃6710)

## 2.32.1 (2024-05-20)

**错误修复**
- 将缺失的测试证书添加到 PyPI 上分发的 sdist 中。


## 2.32.0 (2024-05-20)

**安全**
- 修复了一个问题：在 Session 的第一个请求中设置 `verify=False` 会导致后续对_同一来源_的请求也忽略证书验证，无论 `verify` 的值是什么。(https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56)

**改进**
- `verify=True` 现在会重用一个全局 SSLContext，这应该能改善首次请求与后续请求之间的时间差异。当使用 OpenSSL 3.x 构建的 Python 版本时，它还应该能最大限度地减少 Windows 系统上的证书加载时间。(＃6667)
- Requests 现在支持在重新打包或捆绑时可选地使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最大限度地减少其捆绑范围。如果这两个库都不存在，`Response.text()` 和 `apparent_encoding` API 将默认为 `utf-8`。(＃6702)

**错误修复**
- 修复了长度检测中的一个错误，该错误导致在请求内容长度中错误地计算了表情符号的长度。(＃6589)
- 修复了 JSONDecodeError 中的反序列化错误。(＃6629)
- 修复了一个错误，该错误中一个额外的开头 `/`（路径分隔符）可能导致 urllib3 不必要地重新解析请求 URI。(＃6644)

**弃用**

- Requests 已正式增加对 CPython 3.12 的支持 (#6503)
- Requests 已正式增加对 PyPy 3.9 和 3.10 的支持 (#6641)
- Requests 已正式放弃对 CPython 3.7 的支持 (#6642)
- Requests 已正式放弃对 PyPy 3.7 和 3.8 的支持 (#6641)

**文档**
- 各种拼写错误修复和文档改进。

**打包**
- Requests 已开始采用一些现代打包实践。项目的源文件（以前是 `requests`）现在位于 Requests sdist 的 `src/requests` 中。(＃6506)
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这不应影响普通用户，但极旧版本的打包工具可能会与新的打包格式存在问题。


## 2.31.0 (2023-05-22)

**安全**
- v2.3.0 到 v2.30.0 之间的 Requests 版本在处理 HTTPS 重定向时，容易受到潜在的 `Proxy-Authorization` 标头转发给目标服务器的攻击。

  当使用用户信息（`https://user:pass@proxy:8080`）定义代理时，Requests 将构造一个 `Proxy-Authorization` 标头附加到请求中，用于向代理进行身份验证。

  在 Requests 收到重定向响应的情况下，它以前会错误地重新附加 `Proxy-Authorization` 标头，导致该值通过隧道连接发送到目标服务器。我们*强烈*建议依赖在 URL 中定义代理凭据的用户升级到 Requests 2.31.0+ 以防止意外泄漏，并在变更完全部署后轮换其代理凭据。

  不使用代理或不通过代理 URL 的用户信息部分提供代理凭据的用户不受此漏洞影响。

  完整细节可以在我们的 [Github 安全公告](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 和 [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681) 中阅读。


## 2.30.0 (2023-05-03)

**依赖**
- ⚠️ 增加了对 urllib3 2.0 的支持。 ⚠️

  这可能包含微小的破坏性变更，因此我们建议在升级前仔细测试并查阅 https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html。

  希望继续使用 urllib3 1.x 的用户可以固定版本为 `urllib3<2`。

## 2.29.0 (2023-04-26)

**改进**

- Requests 现在将分块请求交由 urllib3 实现，以提高标准化程度。(＃6226)
- Requests 放宽了标头组件的要求，以支持 bytes/str 的子类。(＃6356)

## 2.28.2 (2023-01-12)

**依赖**

- Requests 现在支持 charset_normalizer 3.x。(＃6261)

**错误修复**

- 更新了 MissingSchema 异常，建议使用 https 方案而不是 http。(＃6188)

## 2.28.1 (2022-06-29)

**改进**

- `iter_content` 中通过过渡到 `yield from` 实现了速度优化。(＃6170)

**依赖**

- 增加了对 chardet 5.0.0 的支持 (＃6179)
- 增加了对 charset-normalizer 2.1.0 的支持 (＃6169)

## 2.28.0 (2022-06-09)

**弃用**

- ⚠️ Requests 已正式放弃对 Python 2.7 的支持。 ⚠️ (＃6091)
- Requests 已正式放弃对 Python 3.6（包括 pypy3.6）的支持。(＃6091)

**改进**

- 对于没有编码的有效负载，将 JSON 解析问题包装在 Request 的 JSONDecodeError 中，以使 `json()` API 保持一致。(＃6097)
- 在所有无效情况下，一致地解析标头组件，并引发 InvalidHeader 错误。(＃6154)
- 增加了对当前 beta 版 3.11 的临时支持。(＃6155)
- Requests 进行了改版，我们决定将其涂成黑色。(＃6095)

**错误修复**

- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会禁用证书验证的错误。所有 2.28.0 之前的 Requests 2.x 版本都受影响。(＃6074)
- 修复了 urllib3 异常泄漏问题，将 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError` 用于 `content` 和 `iter_content`。(＃6057)
- 修复了无效的 Windows 注册表项导致代理解析引发异常而不是忽略该条目的问题。(＃6149)
- 修复了整个有效负载可能包含在 JSONDecodeError 错误消息中的问题。(＃6036)

## 2.27.1 (2022-01-05)

**错误修复**

- 修复了导致代理 URL 中 `auth` 组件被丢弃的解析问题。(＃6028)

## 2.27.0 (2022-01-03)

**改进**

- 正式增加了对 Python 3.10 的支持。(＃5928)

- 增加了一个 `requests.exceptions.JSONDecodeError` 来统一 Python 2 和 3 之间的 JSON 异常。它在 `response.json()` 方法中被引发，并且向后兼容，因为它继承自先前抛出的异常。也可以从 `requests.exceptions.RequestException` 中捕获。(＃5856)

- 改进了命名错误的 `InvalidSchema` 和 `MissingSchema` 异常的错误文本。这是一个临时修复，直到异常可以重命名（Schema->Scheme）。(＃6017)

- 改进了对缺少方案的代理 URL 的代理解析。这将解决 Python 3.9+ 中 `urlparse` 的最新变更。(＃5917)

**错误修复**

- 修复了 `extract_zipped_paths` 中可能导致某些路径无限循环的缺陷。(＃5851)

- 修复了在计算通过 `Tarfile.extractfile()` 获取的文件长度时处理 `AttributeError` 的问题。(＃5239)

- 修复了 urllib3 异常泄漏问题，将 `urllib3.exceptions.InvalidHeader` 包装为 `requests.exceptions.InvalidHeader`。(＃5914)

- 修复了分块请求发送两个 Host 标头的错误。(＃5391)

- 修复了 Requests 2.26.0 中的一个回归问题，即 `Proxy-Authorization` 被错误地从所有使用 `Session.send` 发送的请求中剥离。(＃5924)

- 修复了 2.26.0 版本中对于环境中存在大量可用代理的主机的性能回归问题。(＃5924)

- 修复了 idna 异常泄漏问题，对于域中以点 (.) 开头的 URL，将 `UnicodeError` 包装为 `requests.exceptions.InvalidURL`。(＃5414)

**弃用**

- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。虽然我们没有确切日期，但 Requests 2.27.x 很可能是提供支持的最后一个发布系列。

## 2.26.0 (2021-07-13)

**改进**

- 如果安装了 `brotli` 或 `brotlicffi` 包，Requests 现在支持 Brotli 压缩。(＃5783)

- `Session.send` 现在可以正确解析来自 Session 和 Request 的代理配置。行为现在与 `Session.request` 一致。(＃5681)

**错误修复**

- 修复了在使用 Requests 并行从 zip 存档中进行 zip 提取时的竞争条件。(＃5707)

**依赖**

- 在 Python3 中，使用 MIT 许可的 `charset_normalizer` 代替 `chardet`，以消除捆绑 requests 的项目的许可模糊性。如果您的机器上已经安装了 `chardet`，它将代替 `charset_normalizer` 被使用，以保持向后兼容性。(＃5797)

  您也可以在安装 requests 时指定 `[use_chardet_on_py3]` extra 来安装 `chardet`，如下所示：

    ```shell
    pip install "requests[use_chardet_on_py3]"
    ```

  Python2 仍然依赖于 `chardet` 模块。

- 在 Python 3 上，Requests 现在支持 `idna` 3.x。`idna` 2.x 将继续在 Python 2 安装中使用。(＃5711)

**弃用**

- `requests[security]` extra 已被转换为空操作安装。PyOpenSSL 不再是 Requests 推荐的安全选项。(＃5867)

- Requests 已正式放弃对 Python 3.5 的支持。(＃5867)

## 2.25.1 (2020-12-16)

**错误修复**

- Requests 现在默认将 `application/json` 视为 `utf8`。解决了 `r.text` 和 `r.json` 输出之间的不一致问题。(＃5673)

**依赖**

- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

**改进**

- 增加了对 NETRC 环境变量的支持。(＃5643)

**依赖**

- Requests 现在支持 urllib3 v1.26。

**弃用**

- Requests v2.25.x 将是支持 Python 3.5 的最后一个发布系列。
- `requests[security]` extra 已被正式弃用，并将在 Requests v2.26.0 中移除。

## 2.24.0 (2020-06-17)

**改进**

- 现在只有在 Python 没有 `ssl` 模块或不支持 SNI 的情况下，才会使用 pyOpenSSL TLS 实现。以前，如果 pyOpenSSL 可用，则无条件使用。即使通过 `requests[security]` extra 安装了 pyOpenSSL，这也适用。(＃5443)

- 现在只有在 `allow_redirects` 为 True 时才会进行重定向解析。(＃5492)

- 对于不会使用它的请求，不再执行不必要的 Content-Length 计算。(＃5496)

## 2.23.0 (2020-02-19)

**改进**

- 移除了 Session `__attrs__` 中对 `prefetch` 的已失效引用 (＃5110)

**错误修复**

- Requests 不再在基本认证使用警告中输出密码。(＃5099)

**依赖**

- 对 `chardet` 和 `idna` 的固定版本现在使用主版本号而不是次版本号。希望这能减少每次依赖更新时都需要发布新版本的需求。

## 2.22.0 (2019-05-15)

**依赖**

- Requests 现在支持 urllib3 v1.25.2。（注意：1.25.0 和 1.25.1 不兼容）

**弃用**

- Requests 已正式停止对 Python 3.4 的支持。

## 2.21.0 (2018-12-10)

**依赖**

- Requests 现在支持 idna v2.8。

## 2.20.1 (2018-11-08)

**错误修复**

- 修复了在使用默认端口（http/80, https/443）重定向时意外剥离 Authorization 标头的错误。

## 2.20.0 (2018-10-18)

**错误修复**

-   Content-Type 标头解析现在不区分大小写（例如 charset=utf8 v Charset=utf8）。
-   修复了某些重定向 url 会引发未捕获的 urllib3 异常的泄漏问题。
-   Requests 从 https 重定向到同一主机名的 http 请求中移除了 Authorization 标头。(CVE-2018-18074)
-   `should_bypass_proxies` 现在可以处理没有主机名的 URI（例如文件）。

**依赖**

- Requests 现在支持 urllib3 v1.24。

**弃用**

- Requests 已正式停止对 Python 2.6 的支持。

## 2.19.1 (2018-06-14)

**错误修复**

-   修复了 status_codes.py 的 `init` 函数在尝试附加到 `None` 的 `__doc__` 值时失败的问题。

## 2.19.0 (2018-06-12)

**改进**

-   当使用版本 < 1.3.4 的 cryptography 时，警告用户可能出现的速度变慢问题
-   在将请求转发到适配器之前，检查代理 URL 中的无效主机。
-   现在，片段在重定向过程中得到正确维护。(RFC7231 7.1.2)
-   移除了对 cgi 模块的使用，以加快库的加载时间。
-   增加了对 SHA-256 和 SHA-512 摘要认证算法的支持。
-   对 `Request.content` 的微小性能改进。
-   迁移到使用 collections.abc 以兼容 3.7 版本。

**错误修复**

-   使用 `parse_header_links()` 解析空的 `Link` 标头不再返回一个虚假的条目。
-   修复了从 zip 存档加载默认证书包时会引发 `IOError` 的问题。
-   修复了在不支持 `winreg` 模块的 Windows 系统上出现意外 `ImportError` 的问题。
-   代理绕过中的 DNS 解析不再在请求中包含用户名和密码。这也修复了在 macOS 上 DNS 查询失败的问题。
-   为 URL 比较正确规范化适配器前缀。
-   将 `None` 作为文件指针传递给 `files` 参数不再引发异常。
-   在 `RequestsCookieJar` 上调用 `copy` 现在将正确保留 cookie 策略。

**依赖**

-   我们现在支持 idna v2.7。
-   我们现在支持 urllib3 v1.23。

## 2.18.4 (2017-08-15)

**改进**

-   无效标头的错误消息现在包含标头名称，以便于调试。

**依赖**

-   我们现在支持 idna v2.6。

## 2.18.3 (2017-08-02)

**改进**

-   运行 `$ python -m requests.help` 现在包含已安装的 idna 版本。

**错误修复**

-   修复了在使用 urllib3 v1.22 时，Requests 在遇到 SSL 问题时会引发 `ConnectionError` 而不是 `SSLError` 的问题。

## 2.18.2 (2017-07-25)

**错误修复**

-   由于缺少 `ssl.OPENSSL_VERSION_NUMBER`，`requests.help` 不再在 Python 2.6 上失败。

**依赖**

-   我们现在支持 urllib3 v1.22。

## 2.18.1 (2017-06-14)

**错误修复**

-   修复打包中的一个错误，该错误导致 `*.whl` 包含不正确的数据，从而使 v2.17.3 中的修复回归。

## 2.18.0 (2017-06-14)

**改进**

-   `Response` 现在是一个上下文管理器，因此可以直接在 `with` 语句中使用，而无需先用 `contextlib.closing()` 包装。

**错误修复**

-   如果 multiprocessing 不可用，解决安装失败问题。
-   如果 multiprocessing 无法确定 CPU 核心数，解决测试崩溃问题。
-   解决 utils set_environ 生成器中的错误吞没问题。

## 2.17.3 (2017-05-29)

**改进**

-   改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.2 (2017-05-29)

**改进**

-   改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.1 (2017-05-29)

**改进**

-   改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.0 (2017-05-29)

**改进**

-   移除了 301 重定向缓存。这提高了线程安全性。

## 2.16.5 (2017-05-28)

-   对 `$ python -m requests.help` 的改进。

## 2.16.4 (2017-05-27)

-   引入 `$ python -m requests.help` 命令，用于与维护者一起调试！

## 2.16.3 (2017-05-27)

-   出于兼容性原因，进一步恢复了 `requests.packages` 命名空间。

## 2.16.2 (2017-05-27)

-   出于兼容性原因，进一步恢复了 `requests.packages` 命名空间。

不再需要（下面提到的）代码修改。

## 2.16.1 (2017-05-27)

-   出于兼容性原因，恢复了 `requests.packages` 命名空间。
-   修复了 `urllib3` 版本解析的错误。

**注意**：之前为 `requests.packages` 命名空间编写的导入代码现在必须导入位于此模块级别的代码。

例如：

```python
from requests.packages.urllib3.poolmanager import PoolManager
```

需要重写为：

```python
from requests.packages import urllib3
urllib3.poolmanager.PoolManager
```

或者，更好的是：

```python
from urllib3.poolmanager import PoolManager
```

## 2.16.0 (2017-05-26)

-   取消捆绑所有东西！

## 2.15.1 (2017-05-26)

-   人非圣贤，孰能无过。

## 2.15.0 (2017-05-26)

**改进**

-   引入 `Response.next` 属性，用于从重定向链中获取下一个 `PreparedResponse`（当 `allow_redirects=False` 时）。
-   `__version__` 模块的内部重构。

**错误修复**

-   恢复了 `requests.utils.get_environ_proxies()` 的一个曾是可选的参数。

## 2.14.2 (2017-05-10)

**错误修复**

-   在依赖标记中将一个小于号更改为等于号和一个 or，以扩大与旧版 setuptools 的兼容性。

## 2.14.1 (2017-05-09)

**错误修复**

-   更改了依赖标记以扩大与旧版 pip 的兼容性。

## 2.14.0 (2017-05-09)

**改进**

-   现在可以将 `no_proxy` 作为键传递给 `proxies` 字典，以提供与 `NO_PROXY` 环境变量类似的处理方式。
-   当用户提供无效的证书包文件或目录路径时，Requests 现在会引发 `IOError`，而不是在 HTTPS 请求时因一个相当费解的证书验证错误而失败。
-   `SessionRedirectMixin` 的行为略有改变。`resolve_redirects` 现在将通过调用 `get_redirect_target(response)` 来检测重定向，而不是直接查询 `Response.is_redirect` 和 `Response.headers['location']`。高级用户将能够更容易地处理格式错误的重定向。
-   更改了请求耗时时间的内部计算方式，以在 Windows 上获得更高的分辨率。
-   在 Windows 上使用 Python 2.7 时，为 `[socks]` extra 添加了 `win_inet_pton` 作为条件依赖。
-   更改了 Windows 上的代理绕过实现：代理绕过检查不再使用正向和反向 DNS 请求。
-   对于以 `http` 开头但不是 `http` 或 `https` 的方案的 URL，其主机部分不再强制转换为小写。

**错误修复**

-   大大改进了对重定向中非 ASCII `Location` 标头值的处理。在 Python 2 上遇到的 `UnicodeDecodeErrors` 更少，并且 Python 3 现在能正确理解 Latin-1 不太可能是正确的编码。
-   如果尝试 `seek` 文件以找出其长度失败，我们现在通过中止内容长度计算来适当地处理该情况。
-   将 `HTTPDigestAuth` 限制为仅响应对 4XX 响应发出的认证挑战，而不是对所有认证挑战都响应。
-   修复了一些在 Python 3.6 上触发 `DeprecationWarning` 的代码。
-   沮丧的人表情符号 (`/o\`) 不再有一个大脑袋。我相信这是大家最担心的事。

**其他**

-   将捆绑的 urllib3 更新至 v1.21.1。
-   将捆绑的 chardet 更新至 v3.0.2。
-   将捆绑的 idna 更新至 v2.5。
-   将捆绑的 certifi 更新至 2017.4.17。

## 2.13.0 (2017-01-24)

**功能**

-   仅在我们确定需要时才加载 `idna` 库。这将为用户节省一些内存。

**其他**

-   将捆绑的 urllib3 更新至 1.20。
-   将捆绑的 idna 更新至 2.2。

## 2.12.5 (2017-01-18)

**错误修复**

-   修复了 JSON 编码检测的问题，特别是检测带有 BOM 的大端 UTF-32。

## 2.12.4 (2016-12-14)

**错误修复**

-   修复了 2.12.2 中引入的回归问题，该问题拒绝了基本认证参数中的非字符串类型。虽然已重新添加对此行为的支持，但该行为已被弃用，并将在未来移除。

## 2.12.3 (2016-12-01)

**错误修复**

-   修复了 v2.12.1 中对于以“http”开头的方案的 URL 的回归问题。这些 URL 历史上一直被当作 HTTP 方案的 URL 处理，因此添加了参数。在 v2.12.2 中，为了解决对这些 URL 进行 IDNA 编码的问题，这一行为被过于激进地移除了。此更改已被还原：其他对 IDNA 编码的修复被认为足以恢复到 Requests 在 v2.12.0 之前的行为。

## 2.12.2 (2016-11-30)

**错误修复**

-   修复了几个对技术上无效但被广泛接受的 URL 进行 IDNA 编码的问题。Requests 现在会尝试对 URL 进行 IDNA 编码，但如果失败，且主机仅包含 ASCII 字符，则会乐观地通过。这将允许用户如果愿意，可以自行选择使用 IDNA2003，并且也允许技术上无效但仍然常见的主机名。
-   修复了带有前导空格的 URL 会引发 `InvalidSchema` 错误的问题。
-   修复了一些不带 HTTP 或 HTTPS 方案的 URL 仍然会应用 HTTP URL 准备的问题。
-   修复了基本认证中不能使用 Unicode 字符串的问题。
-   修复了某些 Requests 插件遇到的问题，即构造一个 Response 对象会导致 `Response.content` 引发 `AttributeError`。

## 2.12.1 (2016-11-16)

**错误修复**

-   为 urllib3 中新的 PyOpenSSL 后端更新了 setuptools 的 'security' extra。

**其他**

-   将捆绑的 urllib3 更新至 1.19.1。

## 2.12.0 (2016-11-15)

**改进**

-   将国际化域名支持从 IDNA2003 更新到 IDNA2008。此更新的支持对于几种形式的 IDN 是必需的，并且对于 .de 域名是强制性的。
-   大大改进了猜测内容长度的启发式方法：Requests 将不再将整个 `StringIO` 读入内存。
-   大大改进了为 `PreparedRequest` 对象重新计算 `Content-Length` 标头的逻辑。
-   提高了对没有 `tell` 方法但有 `seek` 方法的类文件对象的容忍度。
-   任何 `Mapping` 的子类现在都会被 `data=` 关键字参数当作字典处理。
-   Requests 现在容忍代理凭据中的空密码，而不是剥离凭据。
-   如果一个请求的主体是类文件对象，并且该请求被 307 或 308 状态码重定向，Requests 现在会尝试倒回主体对象，以便可以重放。

**错误修复**

-   当调用 `response.close` 时，对 `close` 的调用将传播到非 urllib3 的后端。
-   修复了 `ALL_PROXY` 环境变量会优先于特定方案的变量（如 `HTTP_PROXY`）的问题。
-   修复了非 UTF8 原因短语因回退到使用 ISO 8859-1 解码而严重乱码的问题。
-   修复了一个错误，即当使用自定义 Host 标头时，如果这些 Host 标头没有使用平台的原生字符串类型，Requests 将无法正确关联设置的 cookie。

**其他**

-   将捆绑的 urllib3 更新至 1.19。
-   将捆绑的 certifi 证书更新至 2016.09.26。

## 2.11.1 (2016-08-17)

**错误修复**

-   修复了在流式主体上使用 `iter_content` 并设置 `decode_unicode=True` 时会引发 `AttributeError` 的错误。此错误是在 2.11 中引入的。
-   在将动词从 POST/PUT 转换为 GET 的重定向上，从标头块中剥离 Content-Type 和 Transfer-Encoding 标头。

## 2.11.0 (2016-08-08)

**改进**

-   增加了对 `ALL_PROXY` 环境变量的支持。
-   拒绝包含前导空格或换行符的标头值，以降低标头走私的风险。

**错误修复**

-   修复了在错误情况下尝试解码 JSON 响应时偶尔出现的 `TypeError`。现在正确返回 `ValueError`。
-   Requests 之前会错误地忽略 `NO_PROXY` 环境变量中的非 CIDR IP 地址：Requests 现在将其视为特定的 IP。
-   修复了在某些网络条件下发送 JSON 数据时可能导致我们遇到晦涩的 OpenSSL 错误的错误（是的，真的）。
-   增加了类型检查，以确保 `iter_content` 的块大小只接受整数和 `None`。
-   修复了响应体未被完全消耗时，底层连接会被关闭但不会返回到连接池的问题，这可能导致在 `HTTPAdapter` 配置为使用阻塞连接池的情况下 Requests 挂起。

**其他**

-   将捆绑的 urllib3 更新至 1.16。
-   之前的一些版本意外地接受非字符串作为可接受的标头值。此版本不接受。

## 2.10.0 (2016-04-29)

**新功能**

-   SOCKS 代理支持！（需要 PySocks；`$ pip install requests[socks]`）

**其他**

-   将捆绑的 urllib3 更新至 1.15.1。

## 2.9.2 (2016-04-29)

**改进**

-   将内置的 CaseInsensitiveDict（用于标头）的底层数据存储更改为使用 OrderedDict。

**错误修复**

-   如果 allow_redirects=False，则不使用 redirect_cache。
-   当传递的对象在 `tell()` 时抛出异常时，通过分块传输编码发送它们，而不是失败。
-   为与代理相关的连接问题引发 ProxyError。

## 2.9.1 (2015-12-21)

**错误修复**

-   解决了在 2.9.0 中引入的回归问题，该问题导致在 Python 3 中无法发送二进制字符串作为主体。
-   修复了在某些区域设置中计算 cookie 过期日期时的错误。

**其他**

-   将捆绑的 urllib3 更新至 1.13.1。

## 2.9.0 (2015-12-15)

**微小改进**（向后兼容）

-   `verify` 关键字参数现在支持传递一个 CA 证书目录的路径，而不仅仅是单个文件包。
-   现在，在发送以文本模式打开的文件时会发出警告。
-   将 511 Network Authentication Required 状态码添加到状态码注册表中。

**错误修复**

-   对于未寻址到文件开头的类文件对象，我们现在发送实际将读取的字节数的内容长度，而不是文件的总大小，从而允许部分文件上传。
-   上传类文件对象时，如果它们是空的或没有明显的内容长度，我们设置 `Transfer-Encoding: chunked` 而不是 `Content-Length: 0`。
-   在上传分块主体时，我们正确地以缓冲模式接收响应。
-   我们现在通过以 UTF-8 解码来处理在 Python 3 上作为字节串传递的查询字符串。
-   在使用功能性 API 时，Sessions 现在在所有情况下（异常和非异常）都会被关闭，而不是泄漏并等待垃圾回收器清理它们。
-   通过将格式错误的 `qop` 指令（不包含令牌）视为空 `qop` 指令来正确处理摘要认证标头。
-   按名称移除特定 cookie 时的小幅性能改进。

**其他**

-   将 urllib3 更新至 1.13。

## 2.8.1 (2015-10-13)

**错误修复**

-   更新证书包以匹配 `certifi` 2015.9.6.2 的弱证书包。
-   修复 2.8.0 中的一个错误，该错误导致 requests 会引发 `ConnectTimeout` 而不是 `ConnectionError`。
-   使用 PreparedRequest 流程时，requests 现在将正确遵守 `json` 参数。在 2.8.0 中被破坏。
-   使用 PreparedRequest 流程时，requests 现在将正确处理 Python 2 上的 Unicode 字符串方法名。在 2.8.0 中被破坏。

## 2.8.0 (2015-10-05)

**微小改进**（向后兼容）

-   Requests 现在支持按主机代理。这允许 `proxies` 字典具有 `{'<scheme>://<hostname>': '<proxy>'}` 形式的条目。主机特定的代理将优先于先前支持的方案特定代理使用，但先前的语法将继续有效。
-   `Response.raise_for_status` 现在将失败的 URL 作为异常消息的一部分打印出来。
-   `requests.utils.get_netrc_auth` 现在接受一个 `raise_errors` 关键字参数，默认为 `False`。当为 `True` 时，解析 `.netrc` 文件时的错误会导致抛出异常。
-   更改了捆绑项目的导入逻辑，以便下游更容易地取消捆绑 requests。
-   更改了默认的 User-Agent 字符串以避免在 Linux 上泄漏数据：现在仅包含 requests 版本。

**错误修复**

-   `post()` 等函数的 `json` 参数现在只有在 `data` 和 `files` 都不存在时才会使用，与文档一致。
-   我们现在忽略 `NO_PROXY` 环境变量中的空字段。
-   修复了将 `stream=True` 与 `contextlib.closing` 结合使用时会引发 `httplib.BadStatusLine` 的问题。
-   防止了在发送分块主体时，我们会尝试将同一个连接两次返回到连接池的错误。
-   各种微小的内部变更。
-   摘要认证支持现在是线程安全的。

**更新**

-   将 urllib3 更新至 1.12。

## 2.7.0 (2015-05-03)

这是我们遵循新发布流程的第一个版本。更多信息，请参阅[我们的文档](https://requests.readthedocs.io/en/latest/community/release-process/)。

**错误修复**

-   将 urllib3 更新至 1.10.4，解决了一些涉及分块传输编码和响应分帧的错误。

## 2.6.2 (2015-04-23)

**错误修复**

-   修复了以分块数据形式发送的压缩数据未被正确解压的回归问题。(＃2561)

## 2.6.1 (2015-04-22)

**错误修复**

-   移除了在 v2.5.2 中引入的 VendorAlias 导入机制。
-   简化 PreparedRequest.prepare API：我们不再要求用户向 hooks 关键字参数传递一个空列表。(c.f. #2552)
-   Resolve redirects 现在接收并转发所有原始参数到适配器。(＃2503)
-   在处理无法用 ASCII 编码的 unicode URL 时处理 UnicodeDecodeErrors。(＃2540)
-   在执行摘要认证时填充 URI 字段的已解析路径。(＃2426)
-   当 PreparedRequest 的 CookieJar 不是 RequestsCookieJar 的实例时，更可靠地复制它。(＃2527)

## 2.6.0 (2015-03-14)

**错误修复**

-   CVE-2015-2296：修复重定向时的 cookie 处理。以前，没有设置主机值的 cookie 会使用重定向 URL 的主机名，这使用户面临会话固定攻击和潜在的 cookie 窃取风险。此问题由 [BugFuzz](https://bugfuzz.com) 的 Matthew Daley 私下披露。这影响了从 v2.1.0 到 v2.5.3（两端均包含）的所有 requests 版本。
-   修复了当 requests 是 `install_requires` 依赖项并且运行 `python setup.py test` 时出现的错误。(＃2462)
-   修复了当 urllib3 被取消捆绑且 requests 继续使用捆绑导入位置时出现的错误。
-   包含了对 `urllib3` 标头处理的修复。
-   Requests 对未捆绑依赖项的处理现在更加严格。

**功能和改进**

-   支持在 `files` 参数中传递 bytearrays。(＃2468)
-   在使用 `str`、`bytes` 或 `bytearray` 输入到 `files` 参数创建请求时，避免数据重复。

## 2.5.3 (2015-02-24)

**错误修复**

-   恢复对我们捆绑的证书包的更改。更多背景信息请参见 (#2455, #2456, 和 <https://bugs.python.org/issue23476>)

## 2.5.2 (2015-02-23)

**功能和改进**

-   增加 sha256 指纹支持。([shazow/urllib3#540](https://github.com/shazow/urllib3/pull/540))
-   提高标头的性能。([shazow/urllib3#544](https://github.com/shazow/urllib3/pull/544))

**错误修复**

-   复制 pip 的导入机制。当下游重新分发者移除 requests.packages.urllib3 时，导入机制将继续让这些相同的符号起作用。requests 文档中的示例用法以及依赖于 urllib3 捆绑副本的第三方库将无需回退到系统 urllib3 即可工作。
-   如果取消引用然后引用失败，则尝试在重定向上引用 URL 的部分。(＃2356)
-   修复多部分表单数据上传的文件名类型检查。(＃2411)
-   正确处理发出摘要认证挑战的服务器同时提供 auth 和 auth-int qop-values 的情况。(＃2408)
-   修复一个套接字泄漏。([shazow/urllib3#549](https://github.com/shazow/urllib3/pull/549))
-   正确修复多个 `Set-Cookie` 标头。([shazow/urllib3#534](https://github.com/shazow/urllib3/pull/534))
-   禁用内置的主机名验证。([shazow/urllib3#526](https://github.com/shazow/urllib3/pull/526))
-   修复解码已耗尽流的行为。([shazow/urllib3#535](https://github.com/shazow/urllib3/pull/535))

**安全**

-   引入了更新的 `cacert.pem`。
-   从默认密码列表中删除 RC4。([shazow/urllib3#551](https://github.com/shazow/urllib3/pull/551))

## 2.5.1 (2014-12-23)

**行为变更**

-   在 raise_for_status 中只捕获 HTTPErrors (#2382)

**错误修复**

-   处理来自 urllib3 的 LocationParseError (#2344)
-   处理非字符串的类文件对象文件名 (#2379)
-   修复 HTTPDigestAuth 处理程序。允许协商新的 nonces (#2389)

## 2.5.0 (2014-12-01)

**改进**

-   允许将 urllib3 的 Retry 对象与 HTTPAdapters 一起使用 (#2216)
-   响应的 `iter_lines` 方法现在接受一个用于分割内容的分隔符 (#2295)

**行为变更**

-   为 requests.utils 中将在 3.0 中移除的函数添加弃用警告 (#2309)
-   功能性 API 使用的会话总是被关闭 (#2326)
-   将请求限制为 HTTP/1.1 和 HTTP/1.0（停止接受 HTTP/0.9）(#2323)

**错误修复**

-   只解析一次 URL (#2353)
-   允许始终覆盖 Content-Length 标头 (#2332)
-   在 HTTPDigestAuth 中正确处理文件 (#2333)
-   限制 redirect_cache 大小以防止内存滥用 (#2299)
-   修复 HTTPDigestAuth 在成功认证后处理重定向的问题 (#2253)
-   修复 Session.request 中自定义方法参数导致的崩溃 (#2317)
-   修复使用正则表达式库解析 Link 标头的方式 (#2271)

**文档**

-   为互联添加更多参考 (#2348)
-   更新主题的 CSS (#2290)
-   更新按钮和侧边栏的宽度 (#2289)
-   将 Gittip 的引用替换为 Gratipay (#2282)
-   在侧边栏中添加变更日志链接 (#2273)

## 2.4.3 (2014-10-06)

**错误修复**

-   针对 Python 2 的 Unicode URL 改进。
-   为向后兼容性重新排序 JSON 参数。
-   自动从 host/pass URI 中解构认证方案。([#2249](https://github.com/psf/requests/issues/2249))

## 2.4.2 (2014-10-05)

**改进**

-   终于！为上传添加了 json 参数！([#2258](https://github.com/psf/requests/pull/2258))
-   支持在 Python 3.x 上使用字节串 URL ([#2238](https://github.com/psf/requests/pull/2238))

**错误修复**

-   避免陷入循环 ([#2244](https://github.com/psf/requests/pull/2244))
-   多次调用 iter* 会失败并出现无用的错误。([#2240](https://github.com/psf/requests/issues/2240), [#2241](https://github.com/psf/requests/issues/2241))

**文档**

-   更正重定向介绍 ([#2245](https://github.com/psf/requests/pull/2245/))
-   添加了如何在一次请求中发送多个文件的示例。([#2227](https://github.com/psf/requests/pull/2227/))
-   阐明如何传递一组自定义的 CA ([#2248](https://github.com/psf/requests/pull/2248/))

## 2.4.1 (2014-09-09)

-   现在有一个“security”包 extras 集合，`$ pip install requests[security]`
-   如果 Certifi 可用，Requests 现在将使用它。
-   捕获并重新引发 urllib3 ProtocolError。
-   修复了响应尝试永远重定向到自身的错误（搞什么鬼？）。

## 2.4.0 (2014-08-29)

**行为变更**

-   现在自动发送 `Connection: keep-alive` 标头。

**改进**

-   支持连接超时！Timeout 现在接受一个元组 (connect, read)，用于设置单独的连接和读取超时。
-   允许在没有标头/cookie 的情况下复制 PreparedRequests。
-   更新了捆绑的 urllib3 版本。
-   重构了从环境中加载设置的逻辑——新的 Session.merge_environment_settings。
-   处理 iter_content 中的套接字错误。

## 2.3.0 (2014-05-16)

**API 变更**

-   新的 `Response` 属性 `is_redirect`，当库本可以处理此响应为重定向时为 true（无论它是否实际处理了）。
-   `timeout` 参数现在对 `stream=True` 和 `stream=False` 的请求影响相同。
-   v2.0.0 中强制要求显式代理方案的更改已被还原。代理方案现在默认为 `http://`。
-   用于 HTTP 标头的 `CaseInsensitiveDict` 在作为字符串引用或在解释器中查看时，其行为现在像一个普通字典。

**错误修复**

-   重定向上不再暴露 Authorization 或 Proxy-Authorization 标头。分别修复 CVE-2014-1829 和 CVE-2014-1830。
-   每次重定向都会重新评估授权。
-   重定向上，将 url 作为原生字符串传递。
-   当 Unicode 检测失败时，回退到自动检测的 JSON 编码。
-   在 `Session` 上设置为 `None` 的标头现在可以正确地不被发送。
-   即使在同一响应中之前没有使用过 `decode_unicode`，现在也能正确地遵守它。
-   停止将 `compress` 宣传为支持的 Content-Encoding。
-   `Response.history` 参数现在总是一个列表。
-   许多，许多 `urllib3` 错误修复。

## 2.2.1 (2014-01-23)

**错误修复**

-   修复了包含字面或编码的 '#' 字符的代理凭据的错误解析。
-   各种 urllib3 修复。

## 2.2.0 (2014-01-09)

**API 变更**

-   新异常：`ContentDecodingError`。代替 `urllib3` `DecodeError` 异常引发。

**错误修复**

-   避免了在 OS X 的 Python 2.6 中 `proxy_bypass` 的错误实现导致的许多异常。
-   避免了在没有主目录的用户下运行时，尝试从 ~/.netrc 获取身份验证凭据时崩溃。
-   为代理连接池使用正确的池大小。
-   修复 `CookieJar` 对象的迭代。
-   确保 cookie 在重定向时被持久化。
-   由于 chardet 已与 charade 合并，切换回使用 chardet。

## 2.1.0 (2013-12-05)

-   当然，更新了 CA 包。
-   通过 `Session`（例如通过 `Session.get()`）在单个请求上设置的 Cookie 不再持久化到 `Session` 中。
-   在分块上传过程中遇到问题时清理连接，而不是泄漏它们。
-   当分块上传成功时，将连接返回到池中，而不是泄漏它。
-   匹配 HTTPbis 对 HTTP 301 重定向的建议。
-   当收到 401 时，在使用流式上传和摘要认证时防止挂起。
-   由 Requests 设置的标头值现在始终是原生字符串类型。
-   修复了之前损坏的 SNI 支持。
-   修复了使用代理身份验证访问 HTTP 代理的问题。
-   对从 URL 中提取的 HTTP 基本认证用户名和密码进行解码。
-   支持 no_proxy 环境变量的 IP 地址范围。
-   当用户覆盖默认的 `Host:` 标头时，正确解析标头。
-   避免在大小写敏感的服务器上混淆 URL。
-   对非 HTTP/HTTPS url 的更宽松的 URL 处理。
-   在 Python 2.6 和 2.7 中接受 unicode 方法。
-   更具弹性的 cookie 处理。
-   使 `Response` 对象可序列化。
-   实际上添加了 MD5-sess 到摘要认证，而不是像上次那样假装添加了。
-   更新了内部的 urllib3。
-   修复了 @Lukasa 的品味问题。

## 2.0.1 (2013-10-24)

-   更新了包含的 CA 包，加入了新的不信任证书，并为未来实现了自动化流程。
-   为摘要认证添加了 MD5-sess。
-   在多部分文件 POST 消息中接受每个文件的标头。
-   已修复：不在 CONNECT 消息上发送完整的 URL。
-   已修复：正确地将重定向方案转换为小写。
-   已修复：通过功能性 API 设置的 Cookies 未被持久化。
-   已修复：将 urllib3 ProxyError 转换为派生自 ConnectionError 的 requests ProxyError。
-   更新了内部的 urllib3 和 chardet。

## 2.0.0 (2013-09-24)

**API 变更：**

-   在所有 Python 版本中，Headers 字典中的键现在都是原生字符串，即 Python 2 上的字节串，Python 3 上的 unicode。
-   代理 URL 现在*必须*有明确的方案。如果没有，将引发 `MissingSchema` 异常。
-   如果 `Stream=False`，超时现在适用于读取时间。
-   `RequestException` 现在是 `IOError` 的子类，而不是 `RuntimeError`。
-   为 `PreparedRequest` 对象添加了新方法：`PreparedRequest.copy()`。
-   为 `Session` 对象添加了新方法：`Session.update_request()`。此方法使用存储在 `Session` 上的数据（例如 cookies）更新 `Request` 对象。
-   为 `Session` 对象添加了新方法：`Session.prepare_request()`。此方法更新并准备一个 `Request` 对象，并返回相应的 `PreparedRequest` 对象。
-   为 `HTTPAdapter` 对象添加了新方法：`HTTPAdapter.proxy_headers()`。这不应直接调用，但改进了子类接口。
-   由不正确的分块编码引起的 `httplib.IncompleteRead` 异常现在将引发 Requests 的 `ChunkedEncodingError`。
-   无效的百分号转义序列现在会导致引发 Requests 的 `InvalidURL` 异常。
-   HTTP 208 不再使用原因短语 `"im_used"`。正确使用 `"already_reported"`。
-   添加了 HTTP 226 原因（`"im_used"`）。

**错误修复：**

-   大大改进了代理支持，包括 CONNECT 动词。特别感谢为这一改进做出贡献的许多贡献者。
-   当收到 401 认证响应时，现在可以正确管理 Cookies。
-   分块编码修复。
-   支持混合大小写的方案。
-   更好地处理流式下载。
-   从更多位置检索环境代理。
-   微小的 cookies 修复。
-   改进的重定向行为。
-   改进的流式行为，特别是对于压缩数据。
-   各种小的 Python 3 文本编码错误。
-   `.netrc` 不再覆盖显式认证。
-   由钩子设置的 Cookies 现在可以在 Sessions 上正确持久化。
-   修复了在其主机字段中指定端口号的 cookies 的问题。
-   `BytesIO` 可用于执行流式上传。
-   对 `no_proxy` 环境变量的更宽松的解析。
-   非字符串对象可以与文件一起在数据值中传递。

## 1.2.3 (2013-05-25)

-   简单的打包修复。

## 1.2.2 (2013-05-23)

-   简单的打包修复。

## 1.2.1 (2013-05-20)

-   301 和 302 重定向现在对所有动词都将动词更改为 GET，而不仅仅是 POST，从而提高了浏览器兼容性。
-   Python 3.3.2 兼容性。
-   始终对 location 标头进行百分号编码。
-   修复连接适配器匹配为最具体优先。
-   为默认连接适配器添加了新参数，用于传递 block 参数。
-   防止在没有链接标头时出现 KeyError。

## 1.2.0 (2013-03-31)

-   修复了会话和请求中的 cookie 问题。
-   显著改变了钩子的分派方式——钩子现在接收用户在发出请求时指定的所有参数，因此钩子可以使用相同的参数发出二次请求。这对于身份验证处理程序作者尤其必要。
-   移除了 certifi 支持。
-   修复了使用 OAuth 1 和 `signature_type` 主体时未发送数据的问题。
-   感谢 @Lukasa 的重大代理工作，包括从代理 url 解析代理身份验证。
-   修复 DigestAuth 处理过多 401 的问题。
-   更新捆绑的 urllib3 以包含 SSL 错误修复。
-   允许通过 `Response.json()` 方法将关键字参数传递给 `json.loads()`。
-   默认情况下，不在 `GET` 或 `HEAD` 请求上发送 `Content-Length` 标头。
-   向 `Response` 对象添加 `elapsed` 属性，以计时请求花费的时间。
-   修复 `RequestsCookieJar`。
-   会话和适配器现在是可序列化的，即可以与 multiprocessing 库一起使用。
-   将 charade 更新至 1.0.3 版本。

钩子分派方式的改变可能会导致大量问题。

## 1.1.0 (2013-01-10)

-   分块请求。
-   支持可迭代的响应主体。
-   假设服务器持久化重定向参数。
-   允许为文件数据指定显式内容类型。
-   在查找键时使 merge_kwargs 不区分大小写。

## 1.0.3 (2012-12-18)

-   修复文件上传编码错误。
-   修复 cookie 行为。

## 1.0.2 (2012-12-17)

-   HTTPAdapter 的代理修复。

## 1.0.1 (2022-12-17)

-   证书验证异常错误。
-   HTTPAdapter 的代理修复。

## 1.0.0 (2012-12-17)

-   大规模重构和简化。
-   切换到 Apache 2.0 许可证。
-   可交换的连接适配器。
-   可挂载的连接适配器。
-   可变的 ProcessedRequest 链。
-   /s/prefetch/stream。
-   移除所有配置。
-   标准库日志记录。
-   使 Response.json() 可调用，而不是属性。
-   使用新的 charade 项目，该项目同时为 python 2 和 3 提供 chardet。
-   移除除 'response' 之外的所有钩子。
-   移除所有身份验证辅助程序（OAuth, Kerberos）。

这不是一个向后兼容的变更。

## 0.14.2 (2012-10-27)

-   改进了与 MIME 兼容的 JSON 处理。
-   代理修复。
-   路径 hack 修复。
-   不区分大小写的 Content-Encoding 标头。
-   支持在表单帖子中使用 CJK 参数。

## 0.14.1 (2012-10-01)

-   Python 3.3 兼容性。
-   简化默认的 accept-encoding。
-   错误修复。

## 0.14.0 (2012-09-02)

-   如果已经下载，则不再出现 iter_content 错误。

## 0.13.9 (2012-08-25)

-   修复 OAuth + POSTs。
-   从 dispatch_hook 中移除异常吞噬。
-   常规错误修复。

## 0.13.8 (2012-08-21)

-   令人难以置信的 Link 标头支持 :)

## 0.13.7 (2012-08-19)

-   到处都支持 (key, value) 列表。
-   摘要认证改进。
-   确保代理排除正常工作。
-   更清晰的 UnicodeError 异常。
-   自动将 URL 转换为字符串（fURL 等）。
-   错误修复。

## 0.13.6 (2012-08-06)

-   期待已久的连接挂起问题修复！

## 0.13.5 (2012-07-27)

-   打包修复。

## 0.13.4 (2012-07-27)

-   GSSAPI/Kerberos 认证！
-   App Engine 2.7 修复！
-   修复连接泄漏（来自 urllib3 更新）。
-   OAuthlib 路径 hack 修复。
-   OAuthlib URL 参数修复。

## 0.13.3 (2012-07-12)

-   如果可用，使用 simplejson。
-   不要将 SSLErrors 隐藏在 Timeouts 之后。
-   修复了包含片段的 url 的参数处理。
-   显著改进了 User Agent 中的信息。
-   当 verify=False 时，客户端证书被忽略。

## 0.13.2 (2012-06-28)

-   零依赖（再次）！
-   新增：Response.reason。
-   在 OAuth 1.0 中对查询字符串参数进行签名。
-   当 verify=False 时，客户端证书不再被忽略。
-   增加 openSUSE 证书支持。

## 0.13.1 (2012-06-07)

-   允许传递文件或类文件对象作为数据。
-   允许钩子返回指示错误的响应。
-   修复无主体的响应的 Response.text 和 Response.json。

## 0.13.0 (2012-05-29)

-   移除了 Requests.async，转而使用 [grequests](https://github.com/kennethreitz/grequests)。
-   允许禁用 cookie 持久化。
-   safe_mode 的新实现。
-   cookies.get 现在支持默认参数。
-   当使用 return_response=False 调用 Session.request 时，不保存会话 cookie。
-   环境：支持 no_proxy。
-   RequestsCookieJar 改进。
-   各种错误修复。

## 0.12.1 (2012-05-08)

-   新的 `Response.json` 属性。
-   能够添加字符串文件上传。
-   修复 iter_lines 的越界问题。
-   修复 iter_content 的默认大小。
-   修复包含文件的 POST 重定向。

## 0.12.0 (2012-05-02)

-   实验性 OAUTH 支持！
-   基于 Proper CookieJar 的 cookie 接口，具有出色的类字典接口。
-   非迭代内容块的速度修复。
-   将 `pre_request` 移动到更可用的位置。
-   新的 `pre_send` 钩子。
-   延迟编码数据、参数、文件。
-   如果 `certify` 不可用，则加载系统证书包。
-   清理、修复。

## 0.11.2 (2012-04-22)

-   如果 `certifi` 不可用，尝试使用操作系统的证书包。
-   修复无限摘要认证重定向问题。
-   多部分文件上传改进。
-   修复 URL 中无效 % 编码的解码问题。
-   如果响应中没有内容，第二次尝试读取内容时不要抛出错误。
-   在重定向上上传数据。

## 0.11.1 (2012-03-30)

-   POST 重定向现在违反 RFC，以实现浏览器的行为：后续使用 GET。
-   新的 `strict_mode` 配置，用于禁用新的重定向行为。

## 0.11.0 (2012-03-14)

-   支持私有 SSL 证书。
-   从 Gevent 猴子补丁中移除 select.poll。
-   移除冗余的分块传输编码生成器。
-   修复：在 safe_mode 中 Response.ok 引发 Timeout 异常。

## 0.10.8 (2012-03-09)

-   修复生成分块的 ValueError。
-   通过环境变量进行代理配置。
-   简化 iter_lines。
-   新的 trust_env 配置，用于禁用系统/环境提示。
-   抑制 cookie 错误。

## 0.10.7 (2012-03-07)

-   encode_uri = False。

## 0.10.6 (2012-02-25)

-   允许在 cookie 中使用 '='。

## 0.10.5 (2012-02-25)

-   修复内容长度为 0 的响应主体问题。
-   新增 async.imap。
-   不要因 netrc 失败。

## 0.10.4 (2012-02-20)

-   遵守 netrc。

## 0.10.3 (2012-02-20)

-   HEAD 请求不再遵循重定向。
-   raise_for_status() 不再为 3xx 引发异常。
-   使 Session 对象可序列化。
-   对无效方案的 URL 抛出 ValueError。

## 0.10.2 (2012-01-15)

-   大幅改进了 URL 引用。
-   增加了允许的 cookie 键值。
-   尝试修复“打开文件过多”错误。
-   在第一遍替换 unicode 错误，无需第二遍。
-   在查询插入前，为裸域 url 附加 '/'。
-   异常现在继承自 RuntimeError。
-   修复二进制上传 + 认证问题。
-   错误修复。

## 0.10.1 (2012-01-23)

-   PYTHON 3 支持！
-   放弃了对 2.5 的支持。（*向后不兼容*）

## 0.10.0 (2012-01-21)

-   `Response.content` 现在仅为字节。（*向后不兼容*）
-   新的 `Response.text` 仅为 unicode。
-   如果未指定 `Response.encoding` 且 `chardet` 可用，`Response.text` 将猜测一种编码。
-   对于“text”子类型，默认为 ISO-8859-1 (Western) 编码。
-   移除 decode_unicode。（*向后不兼容*）
-   新的多钩子系统。
-   新的 `Response.register_hook` 用于在管道内注册钩子。
-   `Response.url` 现在是 Unicode。

## 0.9.3 (2012-01-18)

-   SSL verify=False 错误修复（在 Windows 机器上明显）。

## 0.9.2 (2012-01-18)

-   异步 async.send 方法。
-   支持带有边界的正确分块流。
-   Session 类的 session 参数。
-   打印整个钩子回溯，而不仅仅是异常实例。
-   修复 response.iter_lines 从挂起的下一行。
-   修复 HTTP-digest 认证中 URI 带有查询字符串的错误。
-   修复事件钩子部分。
-   Urllib3 更新。

## 0.9.1 (2012-01-06)

-   danger_mode 用于自动 Response.raise_for_status()。
-   Response.iter_lines 重构。

## 0.9.0 (2011-12-28)

-   默认开启 ssl 验证。

## 0.8.9 (2011-12-28)

-   打包修复。

## 0.8.8 (2011-12-28)

-   SSL 证书验证！
-   发布 Cerifi：Mozilla 的证书列表。
-   为 SSL 请求新增 'verify' 参数。
-   Urllib3 更新。

## 0.8.7 (2011-12-24)

-   修复 iter_lines 最后一行截断问题。
-   强制异步请求使用 safe_mode。
-   更一致地处理 safe_mode 异常。
-   修复在 safe_mode 中对空响应的迭代问题。

## 0.8.6 (2011-12-18)

-   套接字超时修复。
-   代理授权支持。

## 0.8.5 (2011-12-14)

-   Response.iter_lines!

## 0.8.4 (2011-12-11)

-   预取错误修复。
-   将许可证添加到已安装版本中。

## 0.8.3 (2011-11-27)

-   将认证系统转换为使用更简单的可调用对象。
-   API 方法新增 session 参数。
-   日志记录时显示完整 URL。

## 0.8.2 (2011-11-19)

-   新的 Unicode 解码系统，基于可覆盖的 Response.encoding。
-   正确的 URL 斜杠-引用处理。
-   允许使用 `[`、`]` 和 `_` 的 Cookie。

## 0.8.1 (2011-11-15)

-   URL 请求路径修复。
-   代理修复。
-   超时修复。

## 0.8.0 (2011-11-13)

-   支持 Keep-alive！
-   完全移除 Urllib2。
-   完全移除 Poster。
-   完全移除 CookieJars。
-   新的 ConnectionError 引发机制。
-   用于错误捕获的 safe_mode。
-   请求方法的 prefetch 参数。
-   OPTION 方法。
-   异步池大小节流。
-   文件上传发送真实名称。
-   捆绑了 urllib3。

## 0.7.6 (2011-11-07)

-   摘要认证错误修复（将查询数据附加到路径）。

## 0.7.5 (2011-11-04)

-   如果响应无效，则 Response.content = None。
-   重定向认证处理。

## 0.7.4 (2011-10-26)

-   会话钩子修复。

## 0.7.3 (2011-10-23)

-   摘要认证修复。

## 0.7.2 (2011-10-23)

-   PATCH 修复。

## 0.7.1 (2011-10-23)

-   不再使用 urllib2 认证处理。
-   完全移除 AuthManager, AuthObject 等。
-   新的基于元组的认证系统，带有处理程序回调。

## 0.7.0 (2011-10-22)

-   会话现在是主要接口。
-   弃用 InvalidMethodException。
-   PATCH 修复。
-   新的配置系统（不再有全局设置）。

## 0.6.6 (2011-10-19)

-   会话参数错误修复（参数合并）。

## 0.6.5 (2011-10-18)

-   离线（快速）测试套件。
-   会话字典参数合并。

## 0.6.4 (2011-10-13)

-   根据 HTTP 标头自动解码 unicode。
-   新的 `decode_unicode` 设置。
-   移除 `r.read/close` 方法。
-   新的 `r.faw` 接口，用于高级响应使用。*
-   自动扩展参数化标头。

## 0.6.3 (2011-10-13)

-   漂亮的 `requests.async` 模块，用于使用 gevent 发出异步请求。

## 0.6.2 (2011-10-09)

-   GET/HEAD 遵守 allow_redirects=False。

## 0.6.1 (2011-08-20)

-   增强的状态码体验 `\o/`
-   设置最大重定向次数 (`settings.max_redirects`)。
-   完整的 Unicode URL 支持。
-   支持无协议重定向。
-   允许任意请求类型。
-   错误修复。

## 0.6.0 (2011-08-17)

-   新的回调钩子系统。
-   新的持久会话对象和上下文管理器。
-   透明的字典-cookie 处理。
-   状态码参考对象。
-   移除了 Response.cached。
-   添加了 Response.request。
-   所有参数都是关键字参数。
-   支持相对重定向。
-   HTTPError 处理改进。
-   改进了 https 测试。
-   错误修复。

## 0.5.1 (2011-07-23)

-   国际化域名支持！
-   在不获取整个主体的情况下访问标头 (`read()`)。
-   使用列表作为参数的字典。
-   添加强制基本认证。
-   强制基本认证是默认的认证类型。
-   `python-requests.org` 默认 User-Agent 标头。
-   CaseInsensitiveDict 小写缓存。
-   Response.history 错误修复。

## 0.5.0 (2011-06-21)

-   PATCH 支持。
-   支持代理。
-   HTTPBin 测试套件。
-   重定向修复。
-   settings.verbose 流式写入。
-   所有方法都支持查询字符串。
-   URLErrors（连接被拒、超时、无效 URL）被视为显式引发 `r.requests.get('hwe://blah'); r.raise_for_status()`。

## 0.4.1 (2011-05-22)

-   改进的重定向处理。
-   新的 'allow_redirects' 参数，用于遵循非 GET/HEAD 重定向。
-   设置模块重构。

## 0.4.0 (2011-05-15)

-   Response.history：重定向响应的列表。
-   不区分大小写的标头字典！
-   Unicode URL。

## 0.3.4 (2011-05-14)

-   修复 Urllib2 HTTPAuthentication 递归问题 (Basic/Digest)。
-   内部重构。
-   修复字节数据上传错误。

## 0.3.3 (2011-05-12)

-   请求超时。
-   Unicode url 编码数据。
-   设置上下文管理器和模块。

## 0.3.2 (2011-04-15)

-   自动解压 GZip 编码的内容。
-   支持元组形式的 HTTP Auth 的 AutoAuth。

## 0.3.1 (2011-04-01)

-   Cookie 变更。
-   Response.read()。
-   Poster 修复。

## 0.3.0 (2011-02-25)

-   自动认证 API 变更。
-   更智能的查询 URL 参数化。
-   允许同时上传文件和 POST 数据。
-   新的认证管理器系统：
    -   更简单的基本 HTTP 系统。
    -   支持所有内置的 urllib2 认证。
    -   允许自定义认证处理程序。

## 0.2.4 (2011-02-19)

-   支持 Python 2.5。
-   支持 PyPy-c v1.4。
-   自动认证测试。
-   改进的 Request 对象构造函数。

## 0.2.3 (2011-02-15)

-   新的 HTTPHandling 方法：
    -   Response.__nonzero__（如果 HTTP 状态不佳则为 false）。
    -   Response.ok（如果 HTTP 状态符合预期则为 True）。
    -   Response.error（如果 HTTP 状态不佳则记录 HTTPError）。
    -   Response.raise_for_status()（引发存储的 HTTPError）。

## 0.2.2 (2011-02-14)

-   在发生 HTTPError 的情况下仍然处理请求。（问题 #2）
-   支持 Eventlet 和 Gevent 猴子补丁。
-   支持 Cookie（问题 #1）。

## 0.2.1 (2011-02-14)

-   为 POST 和 PUT 请求添加了文件属性，用于多部分编码文件上传。
-   为上下文和重定向添加了 Request.url 属性。

## 0.2.0 (2011-02-14)

-   诞生！

## 0.0.1 (2011-02-13)

-   挫败感。
-   构思。