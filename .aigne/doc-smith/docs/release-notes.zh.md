# 发布说明

本节按时间顺序列出了 Requests 库不同版本的更改、新功能、错误修复和弃用。及时了解 Requests 的发展以及每个版本中的重大更新。

## dev

- [非微小更改的简短描述。]

### 弃用

- 新增对 Python 3.14 的支持。
- Python 3.8 生命周期结束后，停止对其支持。

## 2.32.4 (2025-06-10)

### 安全

- CVE-2024-47081 修复了一个问题：恶意构造的 URL 和受信任的环境会从 netrc 文件中检索到错误主机名/机器的凭据。

### 改进

- 大量文档改进

### 弃用

- 新增对 Linux 和 macOS 上 pypy 3.11 的支持。
- pypy 3.9 生命周期结束后，停止对其支持。

## 2.32.3 (2024-05-29)

### 错误修复

- 修复了破坏在 HTTPAdapter 子类中指定自定义 SSLContext 能力的错误。（#6716）
- 修复了 Requests 在没有 `ssl` 模块编译的 Python 版本上无法运行的问题。（#6724）

## 2.32.2 (2024-05-21)

### 弃用

- 为了给受 2.32.0 中 CVE 更改影响的自定义 HTTPAdapter 提供更稳定的迁移，我们将 `_get_connection` 重命名为一个新的公共 API：`get_connection_with_tls_context`。现有自定义 HTTPAdapter 需要将其代码迁移以使用此新 API。`get_connection` 在所有 Requests >=2.32.0 版本中均被视为弃用。

  链接的 PR 中提供了一个最小（2 行）示例以简化迁移，但我们强烈建议用户评估其自定义适配器是否受 CVE-2024-35195 中描述的相同问题影响。（#6710）

## 2.32.1 (2024-05-20)

### 错误修复

- 添加了 PyPI 上分发的 sdist 中缺失的测试证书。

## 2.32.0 (2024-05-20)

### 安全

- 修复了一个问题：在 Session 中首次请求时设置 `verify=False` 会导致后续对_同一源_的请求也忽略证书验证，无论 `verify` 的值如何。（https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56）

### 改进

- `verify=True` 现在会重用一个全局 SSLContext，这应该能改善首次请求和后续请求之间的请求时间差异。在使用 OpenSSL 3.x 构建的 Python 版本时，它还应最大程度地减少 Windows 系统上的证书加载时间。（#6667）
- Requests 现在支持在重新打包或捆绑时可选使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最小化其捆绑表面积。如果两个库都不存在，`Response.text()` 和 `apparent_encoding` API 将默认为 `utf-8`。（#6702）

### 错误修复

- 修复了长度检测中的错误，即表情符号的长度在请求内容长度中计算不正确。（#6589）
- 修复了 JSONDecodeError 中的反序列化错误。（#6629）
- 修复了一个错误，即多余的开头 `/`（路径分隔符）可能导致 urllib3 不必要地重新解析请求 URI。（#6644）

### 弃用

- Requests 已正式新增对 CPython 3.12 的支持（#6503）
- Requests 已正式新增对 PyPy 3.9 和 3.10 的支持（#6641）
- Requests 已正式停止对 CPython 3.7 的支持（#6642）
- Requests 已正式停止对 PyPy 3.7 和 3.8 的支持（#6641）

### 文档

- 各种错字修复和文档改进。

### 打包

- Requests 已开始采用一些现代打包实践。项目（以前是 `requests`）的源文件现在位于 Requests sdist 中的 `src/requests`。（#6506）
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这应该不会影响普通用户，但极旧版本的打包工具可能对新打包格式存在问题。

## 2.31.0 (2023-05-22)

### 安全

- Requests v2.3.0 到 v2.30.0 之间的版本在遵循 HTTPS 重定向时，容易将 `Proxy-Authorization` 头部潜在转发到目标服务器。

  当代理使用用户信息（`https://user:pass@proxy:8080`）定义时，Requests 会构造一个 `Proxy-Authorization` 头部，并将其附加到请求以与代理进行身份验证。

  在 Requests 收到重定向响应的情况下，它以前会错误地重新附加 `Proxy-Authorization` 头部，导致该值通过隧道连接发送到目标服务器。强烈建议依赖在 URL 中定义代理凭据的用户升级到 Requests 2.31.0+，以防止意外泄露，并在完全部署更改后轮换其代理凭据。

  不使用代理或不通过代理 URL 的用户信息部分提供代理凭据的用户不受此漏洞影响。

  完整详情可在我们的 [Github 安全咨询](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 和 [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681) 中查阅。

## 2.30.0 (2023-05-03)

### 依赖

- ⚠️ 新增对 urllib3 2.0 的支持。⚠️

  这可能包含次要的破坏性更改，因此我们建议在升级之前仔细测试并查看 https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html。

  希望保留在 urllib3 1.x 上的用户可以将其锁定为 `urllib3<2`。

## 2.29.0 (2023-04-26)

### 改进

- Requests 现在将分块请求推迟到 urllib3 实现，以提高标准化。（#6226）
- Requests 放宽了头部组件要求，以支持 bytes/str 子类。（#6356）

## 2.28.2 (2023-01-12)

### 依赖

- Requests 现在支持 charset_normalizer 3.x。（#6261）

### 错误修复

- 更新了 MissingSchema 异常以建议使用 https 方案而非 http。（#6188）

## 2.28.1 (2022-06-29)

### 改进

- `iter_content` 中使用 `yield from` 的速度优化。（#6170）

### 依赖

- 新增对 chardet 5.0.0 的支持（#6179）
- 新增对 charset-normalizer 2.1.0 的支持（#6169）

## 2.28.0 (2022-06-09)

### 弃用

- ⚠️ Requests 已正式停止对 Python 2.7 的支持。⚠️ （#6091）
- Requests 已正式停止对 Python 3.6（包括 pypy3.6）的支持。（#6091）

### 改进

- 针对没有编码的有效负载，将 JSON 解析问题包装在 Request 的 JSONDecodeError 中，使 `json()` API 保持一致。（#6097）
- 一致地解析头部组件，在所有无效情况下引发 InvalidHeader 错误。（#6154）
- 新增对 3.11 的临时支持，使用当前测试版本。（#6155）
- Requests 进行了改进，我们决定将其“涂黑”。（#6095）

### 错误修复

- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会禁用证书验证的错误。Requests 2.x 2.28.0 之前的所有版本都受此影响。（#6074）
- 修复了 urllib3 异常泄露问题，将 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError`，适用于 `content` 和 `iter_content`。（#6057）
- 修复了 Windows 注册表条目无效导致代理解析引发异常而不是忽略条目的问题。（#6149）
- 修复了 JSONDecodeError 错误消息中可能包含整个有效负载的问题。（#6036）

## 2.27.1 (2022-01-05)

### 错误修复

- 修复了导致 `auth` 组件从代理 URL 中删除的解析问题。（#6028）

## 2.27.0 (2022-01-03)

### 改进

- 正式新增对 Python 3.10 的支持。（#5928）
- 新增了 `requests.exceptions.JSONDecodeError` 以统一 Python 2 和 3 之间的 JSON 异常。此异常在 `response.json()` 方法中引发，并且由于它继承自以前抛出的异常，因此向后兼容。也可从 `requests.exceptions.RequestException` 捕获。（#5856）
- 改进了命名错误的 `InvalidSchema` 和 `MissingSchema` 异常的错误文本。这是一个临时修复，直到异常可以重命名（Schema->Scheme）。（#6017）
- 改进了缺少方案的代理 URL 的代理解析。这将解决 Python 3.9+ 中 `urlparse` 的最新更改。（#5917）

### 错误修复

- 修复了 `extract_zipped_paths` 中的缺陷，该缺陷可能导致某些路径出现无限循环。（#5851）
- 修复了计算通过 `Tarfile.extractfile()` 获取的文件长度时 `AttributeError` 的处理问题。（#5239）
- 修复了 urllib3 异常泄露问题，将 `urllib3.exceptions.InvalidHeader` 包装为 `requests.exceptions.InvalidHeader`。（#5914）
- 修复了分块请求发送两个 Host 头部的问题。（#5391）
- 修复了 Requests 2.26.0 中的回归问题，即 `Proxy-Authorization` 从 `Session.send` 发送的所有请求中被错误地移除。（#5924）
- 修复了 2.26.0 中在环境中存在大量可用代理的主机的性能回归问题。（#5924）
- 修复了 idna 异常泄露问题，将 `UnicodeError` 包装为 `requests.exceptions.InvalidURL`，适用于域名中带有前导点（.）的 URL。（#5414）

### 弃用

- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。虽然我们没有确切日期，但 Requests 2.27.x 可能是提供支持的最后一个发布系列。

## 2.26.0 (2021-07-13)

### 改进

- Requests 现在支持 Brotli 压缩，如果安装了 `brotli` 或 `brotlicffi` 包。（#5783）
- `Session.send` 现在能正确解析 Session 和 Request 中的代理配置。行为现在与 `Session.request` 匹配。（#5681）

### 错误修复

- 修复了在 zip 存档中并行使用 Requests 时，zip 提取中的竞争条件。（#5707）

### 依赖

- 对于 Python3，使用 MIT 许可的 `charset_normalizer` 代替 `chardet`，以消除捆绑 Requests 的项目的许可模糊性。如果您的机器上已安装 `chardet`，它将代替 `charset_normalizer` 使用，以保持向后兼容性。（#5797）

  您也可以在安装 Requests 时通过指定 `[use_chardet_on_py3]` extra 来安装 `chardet`，如下所示：

  ```shell
pip install "requests[use_chardet_on_py3]"
  ```

  Python2 仍然依赖 `chardet` 模块。
- Requests 现在支持 Python 3 上的 `idna` 3.x。`idna` 2.x 将继续在 Python 2 安装上使用。（#5711）

### 弃用

- `requests[security]` extra 已转换为无操作安装。PyOpenSSL 不再是 Requests 的推荐安全选项。（#5867）
- Requests 已正式停止对 Python 3.5 的支持。（#5867）

## 2.25.1 (2020-12-16)

### 错误修复

- Requests 现在默认将 `application/json` 视为 `utf8`。解决了 `r.text` 和 `r.json` 输出之间不一致的问题。（#5673）

### 依赖

- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

### 改进

- 新增对 NETRC 环境变量的支持。（#5643）

### 依赖

- Requests 现在支持 urllib3 v1.26。

### 弃用

- Requests v2.25.x 将是支持 Python 3.5 的最后一个发布系列。
- `requests[security]` extra 已正式弃用，并将在 Requests v2.26.0 中删除。

## 2.24.0 (2020-06-17)

### 改进

- pyOpenSSL TLS 实现现在仅在 Python 不包含 `ssl` 模块或不支持 SNI 时使用。以前，如果可用，pyOpenSSL 会无条件使用。即使通过 `requests[security]` extra 安装 pyOpenSSL，这也适用。（#5443）
- 重定向解析现在应仅在 `allow_redirects` 为 True 时发生。（#5492）
- 不再对不会使用 Content-Length 的请求执行不必要的计算。（#5496）

## 2.23.0 (2020-02-19)

### 改进

- 移除了 Session `__attrs__` 中已废弃的 `prefetch` 引用。（#5110）

### 错误修复

- Requests 不再在基本身份验证使用警告中输出密码。（#5099）

### 依赖

- `chardet` 和 `idna` 的锁定现在使用主版本而非次版本。这有望减少每次依赖更新时发布新版本的需要。

## 2.22.0 (2019-05-15)

### 依赖

- Requests 现在支持 urllib3 v1.25.2。（注意：1.25.0 和 1.25.1 不兼容）

### 弃用

- Requests 已正式停止对 Python 3.4 的支持。

## 2.21.0 (2018-12-10)

### 依赖

- Requests 现在支持 idna v2.8。

## 2.20.1 (2018-11-08)

### 错误修复

- 修复了使用默认端口（http/80，https/443）重定向时意外剥离 Authorization 头部的问题。

## 2.20.0 (2018-10-18)

### 错误修复

- Content-Type 头部解析现在不区分大小写（例如 charset=utf8 vs Charset=utf8）。
- 修复了异常泄露问题，某些重定向 URL 会引发未捕获的 urllib3 异常。
- Requests 会从同一主机名上从 https 重定向到 http 的请求中删除 Authorization 头部。（CVE-2018-18074）
- `should_bypass_proxies` 现在处理没有主机名的 URI（例如文件）。

### 依赖

- Requests 现在支持 urllib3 v1.24。

### 弃用

- Requests 已正式停止对 Python 2.6 的支持。

## 2.19.1 (2018-06-14)

### 错误修复

- 修复了 status_codes.py 的 `init` 函数尝试附加到 `__doc__` 值为 `None` 时失败的问题。

## 2.19.0 (2018-06-12)

### 改进

- 警告用户使用 cryptography 版本 < 1.3.4 时可能出现的减速。
- 在将请求转发到适配器之前，检查代理 URL 中的无效主机。
- 片段现在在重定向过程中正确维护。（RFC7231 7.1.2）
- 移除了 cgi 模块的使用，以加快库加载时间。
- 新增了对 SHA-256 和 SHA-512 摘要认证算法的支持。
- `Request.content` 的次要性能改进。
- 迁移到使用 collections.abc 以实现 3.7 兼容性。

### 错误修复

- 使用 `parse_header_links()` 解析空的 `Link` 头部不再返回一个虚假条目。
- 修复了从 zip 存档加载默认证书捆绑包时引发 `IOError` 的问题。
- 修复了 Windows 系统上意外的 `ImportError` 问题，该系统不支持 `winreg` 模块。
- 代理绕过中的 DNS 解析不再包含请求中的用户名和密码。这也修复了 macOS 上 DNS 查询失败的问题。
- 正确规范化适配器前缀以进行 URL 比较。
- 将 `None` 作为文件指针传递给 `files` 参数不再引发异常。
- 调用 `copy` 到 `RequestsCookieJar` 现在将正确保留 cookie 策略。

### 依赖

- 我们现在支持 idna v2.7。
- 我们现在支持 urllib3 v1.23。

## 2.18.4 (2017-08-15)

### 改进

- 无效头部错误消息现在包含头部名称，以便于调试。

### 依赖

- 我们现在支持 idna v2.6。

## 2.18.3 (2017-08-02)

### 改进

- 运行 `$ python -m requests.help` 现在包含已安装的 idna 版本。

### 错误修复

- 修复了使用 urllib3 v1.22 遇到 SSL 问题时 Requests 会引发 `ConnectionError` 而不是 `SSLError` 的问题。

## 2.18.2 (2017-07-25)

### 错误修复

- 由于缺少 `ssl.OPENSSL_VERSION_NUMBER`，`requests.help` 不再在 Python 2.6 上失败。

### 依赖

- 我们现在支持 urllib3 v1.22。

## 2.18.1 (2017-06-14)

### 错误修复

- 修复了打包中的错误，即 `*.whl` 包含不正确的数据，导致 v2.17.3 中的修复回归。

## 2.18.0 (2017-06-14)

### 改进

- `Response` 现在是一个上下文管理器，因此可以直接在 `with` 语句中使用，而无需先用 `contextlib.closing()` 包装。

### 错误修复

- 解决多进程不可用时的安装失败问题。
- 解决多进程无法确定 CPU 核心数量时测试崩溃的问题。
- 解决 utils set_environ 生成器中错误吞噬的问题。

## 2.17.3 (2017-05-29)

### 改进

- 改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.2 (2017-05-29)

### 改进

- 改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.1 (2017-05-29)

### 改进

- 改进了 `packages` 命名空间身份支持，用于猴子补丁库。

## 2.17.0 (2017-05-29)

### 改进

- 移除了 301 重定向缓存。这提高了线程安全性。

## 2.16.5 (2017-05-28)

- 改进了 `$ python -m requests.help`。

## 2.16.4 (2017-05-27)

- 引入了 `$ python -m requests.help` 命令，用于与维护人员一起调试！

## 2.16.3 (2017-05-27)

- 为兼容性原因，进一步恢复了 `requests.packages` 命名空间。

## 2.16.2 (2017-05-27)

- 为兼容性原因，进一步恢复了 `requests.packages` 命名空间。

  不再需要（如下所述）任何代码修改。

## 2.16.1 (2017-05-27)

- 为兼容性原因，恢复了 `requests.packages` 命名空间。
- `urllib3` 版本解析的错误修复。

  **注意**：以前编写的用于导入 `requests.packages` 命名空间的代码现在必须导入位于此模块级别上的代码。

  例如：

  ```
from requests.packages.urllib3.poolmanager import PoolManager
  ```

  需要重写为：

  ```
from requests.packages import urllib3
urllib3.poolmanager.PoolManager
  ```

  或者，甚至更好：

  ```
from urllib3.poolmanager import PoolManager
  ```

## 2.16.0 (2017-05-26)

- 移除了所有vendored内容！

## 2.15.1 (2017-05-26)

- 每个人都会犯错。

## 2.15.0 (2017-05-26)

### 改进

- 引入了 `Response.next` 属性，用于从重定向链中获取下一个 `PreparedResponse`（当 `allow_redirects=False` 时）。
- `__version__` 模块的内部重构。

### 错误修复

- 恢复了 `requests.utils.get_environ_proxies()` 曾经是可选的参数。

## 2.14.2 (2017-05-10)

### 错误修复

- 更改了依赖标记中的小于号为等于号和或，以扩大与旧版 setuptools 的兼容性。

## 2.14.1 (2017-05-09)

### 错误修复

- 更改了依赖标记，以扩大与旧版 pip 的兼容性。

## 2.14.0 (2017-05-09)

### 改进

- 现在可以将 `no_proxy` 作为键传递给 `proxies` 字典，以提供与 `NO_PROXY` 环境变量类似的处理。
- 当用户提供无效的证书捆绑文件或目录路径时，Requests 现在会引发 `IOError`，而不是在 HTTPS 请求时因难以理解的证书验证错误而失败。
- `SessionRedirectMixin` 的行为略有改变。`resolve_redirects` 现在将通过调用 `get_redirect_target(response)` 而不是直接查询 `Response.is_redirect` 和 `Response.headers['location']` 来检测重定向。高级用户将能够更轻松地处理格式错误的重定向。
- 更改了已用请求时间的内部计算，以在 Windows 上获得更高的分辨率。
- 在 Windows 上使用 Python 2.7 时，为 `[socks]` extra 添加了 `win_inet_pton` 作为条件依赖项。
- 更改了 Windows 上的代理绕过实现：代理绕过检查不再使用正向和反向 DNS 请求。
- 方案以 `http` 开头但不是 `http` 或 `https` 的 URL，其主机部分不再强制小写。

### 错误修复

- 大幅改进了对重定向中非 ASCII `Location` 头部值的处理。Python 2 上遇到的 `UnicodeDecodeErrors` 较少，Python 3 现在正确理解 Latin-1 不太可能是正确的编码。
- 如果尝试 `seek` 文件以找出其长度失败，我们现在会适当处理，中止内容长度计算。
- 限制 `HTTPDigestAuth` 仅响应 4XX 响应中的身份验证挑战，而不是所有身份验证挑战。
- 修复了 Python 3.6 上引发 `DeprecationWarning` 的某些代码。
- 沮丧的人表情符号（`\o/`）不再有大头。我相信这是你们最担心的事情。

### 杂项

- 更新了捆绑的 urllib3 至 v1.21.1。
- 更新了捆绑的 chardet 至 v3.0.2。
- 更新了捆绑的 idna 至 v2.5。
- 更新了捆绑的 certifi 至 2017.4.17。

## 2.13.0 (2017-01-24)

### 功能

- 仅在确定需要 `idna` 库时才加载它。这将为用户节省一些内存。

### 杂项

- 更新了捆绑的 urllib3 至 1.20。
- 更新了捆绑的 idna 至 2.2。

## 2.12.5 (2017-01-18)

### 错误修复

- 修复了 JSON 编码检测问题，特别是检测带 BOM 的大端 UTF-32。

## 2.12.4 (2016-12-14)

### 错误修复

- 修复了 2.12.2 版本中的回归，该回归导致基本身份验证参数中拒绝非字符串类型。虽然已重新添加对这种行为的支持，但该行为已弃用，并将在未来移除。

## 2.12.3 (2016-12-01)

### 错误修复

- 修复了 v2.12.1 版本中 URL 方案以“http”开头的回归问题。这些 URL 历史上一直被视为 HTTP 方案 URL，并因此添加了参数。在 v2.12.2 中，为了过度解决 IDNA 编码这些 URL 的问题，此功能被移除。此更改已恢复：其他 IDNA 编码的修复已被判断为足以恢复 Requests 在 v2.12.0 之前的行为。

## 2.12.2 (2016-11-30)

### 错误修复

- 修复了 IDNA 编码 URL 的几个问题，这些 URL 技术上无效但被广泛接受。Requests 现在将尝试对 URL 进行 IDNA 编码，但如果失败，且主机只包含 ASCII 字符，它将乐观地通过。这将允许用户自行选择使用 IDNA2003，并且也允许技术上无效但仍常见的主机名。
- 修复了 URL 带有前导空格时会引发 `InvalidSchema` 错误的问题。
- 修复了某些没有 HTTP 或 HTTPS 方案的 URL 仍会应用 HTTP URL 准备的问题。
- 修复了 Unicode 字符串无法用于基本身份验证的问题。
- 修复了某些 Requests 插件在构造 Response 对象时会导致 `Response.content` 引发 `AttributeError` 的问题。

## 2.12.1 (2016-11-16)

### 错误修复

- 更新了 setuptools 的“security”extra，以适应 urllib3 中的新 PyOpenSSL 后端。

### 杂项

- 更新了捆绑的 urllib3 至 1.19.1。

## 2.12.0 (2016-11-15)

### 改进

- 将国际化域名支持从 IDNA2003 更新到 IDNA2008。此更新后的支持是多种 IDN 形式所必需的，并且是 .de 域名的强制要求。
- 大幅改进了内容长度猜测启发式方法：Requests 不再将整个 `StringIO` 读入内存。
- 大幅改进了 `PreparedRequest` 对象 `Content-Length` 头部重新计算的逻辑。
- 提高了对没有 `tell` 方法但有 `seek` 方法的文件类对象的容忍度。
- 任何 `Mapping` 的子类现在都像字典一样被 `data=` 关键字参数处理。
- Requests 现在容忍代理凭据中的空密码，而不是剥离凭据。
- 如果使用文件类对象作为主体发出请求，并且该请求以 307 或 308 状态码重定向，Requests 现在将尝试回绕主体对象，以便可以重新播放。

### 错误修复

- 调用 `response.close` 时，`close` 调用将传播到非 urllib3 后端。
- 修复了 `ALL_PROXY` 环境变量优先于 `HTTP_PROXY` 等特定方案变量的问题。
- 修复了非 UTF8 原因短语因回退到使用 ISO 8859-1 解码而严重损坏的问题。
- 修复了当使用自定义 Host 头部（如果这些 Host 头部不使用平台的本机字符串类型）时，Requests 无法正确关联设置的 cookie 的错误。

### 杂项

- 更新了捆绑的 urllib3 至 1.19。
- 更新了捆绑的 certifi 证书至 2016.09.26。

## 2.11.1 (2016-08-17)

### 错误修复

- 修复了在使用 `iter_content` 和 `decode_unicode=True` 处理流式主体时引发 `AttributeError` 的错误。此错误是在 2.11 版本中引入的。
- 当重定向将动词从 POST/PUT 转换为 GET 时，从头部块中删除 Content-Type 和 Transfer-Encoding 头部。

## 2.11.0 (2016-08-08)

### 改进

- 新增了对 `ALL_PROXY` 环境变量的支持。
- 拒绝包含前导空格或换行符的头部值，以减少头部走私的风险。

### 错误修复

- 修复了在错误情况下尝试解码 JSON 响应时偶尔出现的 `TypeError`。现在正确返回 `ValueError`。
- Requests 会错误地忽略 `NO_PROXY` 环境变量中的非 CIDR IP 地址：Requests 现在将其视为特定 IP。
- 修复了发送 JSON 数据时可能导致在某些网络条件下遇到晦涩的 OpenSSL 错误的问题（确实如此）。
- 添加了类型检查，以确保 `iter_content` 仅接受整数和 `None` 作为块大小。
- 修复了一个问题，即主体未完全消耗的响应会关闭底层连接，但不会返回到连接池，这可能导致 Requests 在 `HTTPAdapter` 配置为使用阻塞连接池的情况下挂起。

### 杂项

- 更新了捆绑的 urllib3 至 1.16。
- 之前的一些版本意外地接受了非字符串作为可接受的头部值。此版本不接受。

## 2.10.0 (2016-04-29)

### 新功能

- SOCKS 代理支持！（需要 PySocks；`$ pip install requests[socks]`）

### 杂项

- 更新了捆绑的 urllib3 至 1.15.1。

## 2.9.2 (2016-04-29)

### 改进

- 更改内置的 CaseInsensitiveDict（用于头部）以使用 OrderedDict 作为其底层数据存储。

### 错误修复

- 如果 `allow_redirects=False`，则不使用 `redirect_cache`。
- 当传入从 `tell()` 抛出异常的对象时，通过分块传输编码发送它们，而不是失败。
- 对于代理相关的连接问题，引发 ProxyError。

## 2.9.1 (2015-12-21)

### 错误修复

- 解决了 2.9.0 版本中引入的回归问题，该问题导致在 Python 3 中无法发送二进制字符串作为主体。
- 修复了在某些区域设置中计算 cookie 过期日期时出现的错误。

### 杂项

- 更新了捆绑的 urllib3 至 1.13.1。

## 2.9.0 (2015-12-15)

### 次要改进

- `verify` 关键字参数现在支持传递 CA 证书目录路径，而不仅仅是单个文件捆绑包。
- 现在发送在文本模式下打开的文件时会发出警告。
- 将 511 Network Authentication Required 状态码添加到状态码注册表。

### 错误修复

- 对于未查找至起始的文件类对象，我们现在发送实际读取的字节数作为内容长度，而不是文件的总大小，从而允许部分文件上传。
- 当上传文件类对象时，如果它们为空或没有明显的内容长度，我们设置 `Transfer-Encoding: chunked` 而不是 `Content-Length: 0`。
- 当上传分块主体时，我们现在能正确地在缓冲模式下接收响应。
- 我们现在处理在 Python 3 上以字节字符串形式传递查询字符串的情况，通过将其解码为 UTF-8。
- 使用函数式 API 时，会话现在在所有情况下（异常和非异常）都会关闭，而不是泄露并等待垃圾回收器清理它们。
- 正确处理包含无效令牌的格式错误的 `qop` 指令的摘要认证头部，将其视为未提供 `qop` 指令的情况。
- 移除特定名称 cookie 时的次要性能改进。

### 杂项

- 更新了 urllib3 至 1.13。

## 2.8.1 (2015-10-13)

### 错误修复

- 更新证书捆绑包以匹配 `certifi` 2015.9.6.2 的弱证书捆绑包。
- 修复了 2.8.0 版本中的一个错误，即 requests 会引发 `ConnectTimeout` 而不是 `ConnectionError`。
- 使用 PreparedRequest 流程时，requests 现在将正确遵守 `json` 参数。2.8.0 版本中已损坏。
- 使用 PreparedRequest 流程时，requests 现在将正确处理 Python 2 上 Unicode 字符串方法名称。2.8.0 版本中已损坏。

## 2.8.0 (2015-10-05)

### 次要改进

- Requests 现在支持每个主机的代理。这允许 `proxies` 字典包含 `{'<scheme>://<hostname>': '<proxy>'}` 形式的条目。主机特定代理将优先于以前支持的方案特定代理使用，但以前的语法将继续有效。
- `Response.raise_for_status` 现在将失败的 URL 作为异常消息的一部分打印出来。
- `requests.utils.get_netrc_auth` 现在接受一个 `raise_errors` kwarg，默认为 `False`。当为 `True` 时，解析 `.netrc` 文件时的错误会导致抛出异常。
- 更改了捆绑项目导入逻辑，以便更容易地将 requests 解捆。
- 更改了默认的 User-Agent 字符串，以避免在 Linux 上泄露数据：现在只包含 requests 版本。

### 错误修复

- `post()` 及相关方法的 `json` 参数现在仅在 `data` 和 `files` 都不存在时使用，这与文档一致。
- 我们现在忽略 `NO_PROXY` 环境变量中的空字段。
- 修复了当 `stream=True` 与 `contextlib.closing` 结合使用时会引发 `httplib.BadStatusLine` 的问题。
- 防止了在发送分块主体时我们试图将同一连接两次返回到连接池的错误。
- 杂项次要内部更改。
- Digest Auth 支持现在是线程安全的。

### 更新

- 更新了 urllib3 至 1.12。

## 2.7.0 (2015-05-03)

这是我们新发布流程后的第一个版本。更多信息请参阅[我们的文档](https://requests.readthedocs.io/en/latest/community/release-process/)。

### 错误修复

- 更新了 urllib3 至 1.10.4，解决了多个涉及分块传输编码和响应帧的错误。

## 2.6.2 (2015-04-23)

### 错误修复

- 修复了压缩数据作为分块数据发送时未正确解压的回归问题。（#2561）

## 2.6.1 (2015-04-22)

### 错误修复

- 移除了 v2.5.2 中引入的 VendorAlias 导入机制。
- 简化了 PreparedRequest.prepare API：我们不再要求用户向 hooks 关键字参数传递一个空列表。（参见 #2552）
- Resolve redirects 现在接收并转发所有原始参数到适配器。（#2503）
- 尝试处理无法编码为 ASCII 的 Unicode URL 时，处理 UnicodeDecodeErrors。（#2540）
- 执行 Digest Authentication 时，填充 URI 字段的已解析路径。（#2426）
- 当 PreparedRequest 的 CookieJar 不是 RequestsCookieJar 实例时，更可靠地复制它。（#2527）

## 2.6.0 (2015-03-14)

### 错误修复

- CVE-2015-2296：修复了重定向时 cookie 的处理问题。以前，未设置主机值的 cookie 会使用重定向 URL 的主机名，从而使 Requests 用户面临会话固定攻击和潜在的 cookie 窃取。此问题由 [BugFuzz](https://bugfuzz.com) 的 Matthew Daley 私下披露。此问题影响 Requests v2.1.0 到 v2.5.3（包括两端）的所有版本。
- 修复了当 requests 是 `install_requires` 依赖项且运行 `python setup.py test` 时出现的错误。（#2462）
- 修复了 urllib3 解捆时，requests 继续使用 vendored 导入位置的错误。
- 包含了对 `urllib3` 头部处理的修复。
- Requests 对未捆绑依赖项的处理现在更严格。

### 功能和改进

- 支持 `files` 参数中作为参数传递的 bytearrays。（#2468）
- 创建具有 `str`、`bytes` 或 `bytearray` 输入的请求时，避免数据重复。（#2468）

## 2.5.3 (2015-02-24)

### 错误修复

- 恢复了我们捆绑证书捆绑包的更改。更多背景信息请参阅（#2455、#2456 和 <https://bugs.python.org/issue23476>）

## 2.5.2 (2015-02-23)

### 功能和改进

- 新增 sha256 指纹支持。（[shazow/urllib3#540](https://github.com/shazow/urllib3/pull/540)）
- 改进了头部的性能。（[shazow/urllib3#544](https://github.com/shazow/urllib3/pull/544)）

### 错误修复

- 复制 pip 的导入机制。当下游分发商移除 requests.packages.urllib3 时，导入机制将继续使这些相同的符号工作。requests 文档中的示例用法和依赖 vendored urllib3 副本的第三方库将无需回退到系统 urllib3 即可工作。
- 尝试在重定向时引用 URL 的部分，如果取消引用然后引用失败。（#2356）
- 修复了 multipart form-data 上传的文件名类型检查。（#2411）
- 正确处理服务器发出摘要认证挑战时同时提供 auth 和 auth-int qop 值的情况。（#2408）
- 修复了 socket 泄露问题。（[shazow/urllib3#549](https://github.com/shazow/urllib3/pull/549)）
- 正确修复了多个 `Set-Cookie` 头部。（[shazow/urllib3#534](https://github.com/shazow/urllib3/pull/534)）
- 禁用了内置的主机名验证。（[shazow/urllib3#526](https://github.com/shazow/urllib3/pull/526)）
- 修复了解码已耗尽流的行为。（[shazow/urllib3#535](https://github.com/shazow/urllib3/pull/535)）

### 安全

- 引入了更新的 `cacert.pem`。
- 从默认密码列表中移除了 RC4。（[shazow/urllib3#551](https://github.com/shazow/urllib3/pull/551)）

## 2.5.1 (2014-12-23)

### 行为变更

- 仅在 raise_for_status 中捕获 HTTPErrors (#2382)

### 错误修复

- 处理 urllib3 的 LocationParseError (#2344)
- 处理非字符串的文件类对象文件名 (#2379)
- 解除 HTTPDigestAuth 处理程序阻塞。允许协商新的随机数 (#2389)

## 2.5.0 (2014-12-01)

### 改进

- 允许 urllib3 的 Retry 对象与 HTTPAdapters 一起使用 (#2216)
- 响应的 `iter_lines` 方法现在接受一个分隔符来分割内容 (#2295)

### 行为变更

- 为 requests.utils 中将在 3.0 版本中移除的函数添加弃用警告 (#2309)
- 函数式 API 使用的会话始终关闭 (#2326)
- 将请求限制为 HTTP/1.1 和 HTTP/1.0（停止接受 HTTP/0.9）(#2323)

### 错误修复

- 只解析一次 URL (#2353)
- 允许 Content-Length 头部始终被覆盖 (#2332)
- 正确处理 HTTPDigestAuth 中的文件 (#2333)
- 限制 redirect_cache 大小以防止内存滥用 (#2299)
- 修复 HTTPDigestAuth 在成功认证后处理重定向的问题 (#2253)
- 修复了 Session.request 自定义方法参数导致的崩溃 (#2317)
- 修复了使用正则表达式库解析 Link 头部的方式 (#2271)

### 文档

- 新增更多交叉链接参考 (#2348)
- 更新主题 CSS (#2290)
- 更新按钮和侧边栏宽度 (#2289)
- 将 Gittip 的引用替换为 Gratipay (#2282)
- 在侧边栏中添加变更日志链接 (#2273)

## 2.4.3 (2014-10-06)

### 错误修复

- Python 2 的 Unicode URL 改进。
- 重新排序 JSON 参数以实现向后兼容。
- 自动从主机/密码 URI 中碎片整理身份验证方案。（[#2249](https://github.com/psf/requests/issues/2249)）

## 2.4.2 (2014-10-05)

### 改进

- 终于！为上传添加 JSON 参数！（[#2258](https://github.com/psf/requests/pull/2258)）
- 支持 Python 3.x 上的字节字符串 URL（[#2238](https://github.com/psf/requests/pull/2238)）

### 错误修复

- 避免陷入循环（[#2244](https://github.com/psf/requests/pull/2244)）
- 多次调用 iter* 失败并出现无用的错误。（[#2240](https://github.com/psf/requests/issues/2240)，[#2241](https://github.com/psf/requests/issues/2241)）

### 文档

- 更正重定向介绍（[#2245](https://github.com/psf/requests/pull/2245/)）
- 添加了如何在一次请求中发送多个文件的示例。（[#2227](https://github.com/psf/requests/pull/2227/)）
- 澄清如何传递自定义 CA 集（[#2248](https://github.com/psf/requests/pull/2248/)）

## 2.4.1 (2014-09-09)

- 现在有一个“security”包 extras set，`$ pip install requests[security]`
- Requests 现在将在可用时使用 Certifi。
- 捕获并重新引发 urllib3 ProtocolError
- 修复了响应尝试永久重定向到自身（搞什么？）的错误。

## 2.4.0 (2014-08-29)

### 行为变更

- `Connection: keep-alive` 头部现在自动发送。

### 改进

- 支持连接超时！Timeout 现在接受一个元组 (connect, read)，用于设置单独的连接和读取超时。
- 允许复制 PreparedRequests 而不带头部/cookie。
- 更新了捆绑的 urllib3 版本。
- 重构了从环境中加载设置的部分——新增 Session.merge_environment_settings。
- 处理 iter_content 中的 socket 错误。

## 2.3.0 (2014-05-16)

### API 更改

- 新的 `Response` 属性 `is_redirect`，当库可以处理此响应为重定向时（无论是否实际处理），此属性为真。
- `timeout` 参数现在对 `stream=True` 和 `stream=False` 的请求影响相同。
- v2.0.0 中要求显式代理方案的更改已恢复。代理方案现在默认为 `http://`。
- 用于 HTTP 头部的 `CaseInsensitiveDict` 在作为字符串引用或在解释器中查看时，现在行为与普通字典相同。

### 错误修复

- 不再在重定向时暴露 Authorization 或 Proxy-Authorization 头部。分别修复 CVE-2014-1829 和 CVE-2014-1830。
- 每次重定向时重新评估授权。
- 在重定向时，将 URL 作为原生字符串传递。
- 当 Unicode 检测失败时，回退到 JSON 的自动检测编码。
- `Session` 中设置为 `None` 的头部现在已正确地不发送。
- 即使之前在同一响应中未使用 `decode_unicode`，也正确遵守它。
- 停止宣传 `compress` 为支持的 Content-Encoding。
- `Response.history` 参数现在始终是一个列表。
- 许多 `urllib3` 的错误修复。

## 2.2.1 (2014-01-23)

### 错误修复

- 修复了包含字面量或编码的 '#' 字符的代理凭据解析不正确的问题。
- 各种 urllib3 修复。

## 2.2.0 (2014-01-09)

### API 更改

- 新增异常：`ContentDecodingError`。代替 `urllib3` `DecodeError` 异常引发。

### 错误修复

- 避免由于 Python 2.6 中 OS X 上 `proxy_bypass` 的错误实现而引发的大量异常。
- 避免在尝试从 ~/.netrc 获取身份验证凭据时，在没有主目录的用户身份运行时崩溃。
- 对连接到代理的连接池使用正确的池大小。
- 修复 `CookieJar` 对象的迭代。
- 确保 cookie 在重定向时持久化。
- 切换回使用 chardet，因为它已与 charade 合并。

## 2.1.0 (2013-12-05)

- 更新了 CA 捆绑包，当然。
- 通过 `Session`（例如通过 `Session.get()`）在单个 Request 上设置的 Cookie 不再持久化到 `Session`。
- 在分块上传期间遇到问题时清理连接，而不是泄露它们。
- 分块上传成功时将连接返回到池中，而不是泄露它。
- 匹配 HTTPbis 对 HTTP 301 重定向的建议。
- 使用流式上传和摘要认证时，当收到 401 响应时，防止挂起。
- Requests 设置的头部值现在始终是本机字符串类型。
- 修复之前损坏的 SNI 支持。
- 修复使用代理认证访问 HTTP 代理的问题。
- 解码从 URL 提取的 HTTP Basic 用户名和密码。
- 支持 no_proxy 环境变量的 IP 地址范围。
- 当用户覆盖默认的 `Host:` 头部时，正确解析头部。
- 避免在区分大小写的服务器情况下混淆 URL。
- 针对非 HTTP/HTTPS URL 的更宽松 URL 处理。
- 接受 Python 2.6 和 2.7 中的 Unicode 方法。
- 更具弹性的 cookie 处理。
- 使 `Response` 对象可序列化。
- 实际上将 MD5-sess 添加到摘要认证中，而不是像上次那样假装。
- 更新了内部 urllib3。
- 修复了 @Lukasa 的品味问题。

## 2.0.1 (2013-10-24)

- 更新了包含的 CA 捆绑包，新增了不受信任的证书和未来的自动化流程。
- 将 MD5-sess 添加到摘要认证中。
- 接受多部分文件 POST 消息中的每个文件头部。
- 修复：不要在 CONNECT 消息中发送完整 URL。
- 修复：正确将重定向方案小写。
- 修复：通过函数式 API 设置的 Cookie 未持久化。
- 修复：将 urllib3 ProxyError 转换为继承自 ConnectionError 的 requests ProxyError。
- 更新了内部 urllib3 和 chardet。

## 2.0.0 (2013-09-24)

### API 更改

- Headers 字典中的键现在在所有 Python 版本中都是原生字符串，即 Python 2 中的字节字符串，Python 3 中的 Unicode 字符串。
- 代理 URL 现在**必须**有明确的方案。如果它们没有，将引发 `MissingSchema` 异常。
- 如果 `Stream=False`，超时现在适用于读取时间。
- `RequestException` 现在是 `IOError` 的子类，而不是 `RuntimeError`。
- 为 `PreparedRequest` 对象添加了新方法：`PreparedRequest.copy()`。
- 为 `Session` 对象添加了新方法：`Session.update_request()`。此方法使用 `Session` 上存储的数据（例如 cookie）更新 `Request` 对象。
- 为 `Session` 对象添加了新方法：`Session.prepare_request()`。此方法更新并准备 `Request` 对象，并返回相应的 `PreparedRequest` 对象。
- 为 `HTTPAdapter` 对象添加了新方法：`HTTPAdapter.proxy_headers()`。不应直接调用此方法，但它改进了子类接口。
- 由不正确的块编码导致的 `httplib.IncompleteRead` 异常现在将引发 Requests `ChunkedEncodingError`。
- 无效的百分比转义序列现在会导致引发 Requests `InvalidURL` 异常。
- HTTP 208 不再使用原因短语 `"im_used"`。正确使用 `"already_reported"`。
- 新增 HTTP 226 原因（`"im_used"`）。

### 错误修复

- 大幅改进了代理支持，包括 CONNECT 动词。特别感谢为这项改进做出贡献的许多贡献者。
- 当收到 401 身份验证响应时，现在正确管理 cookie。
- 分块编码修复。
- 支持大小写混合的方案。
- 更好地处理流式下载。
- 从更多位置检索环境变量代理。
- 次要 cookie 修复。
- 改进了重定向行为。
- 改进了流式传输行为，特别是对于压缩数据。
- Python 3 文本编码的各种小错误。
- `.netrc` 不再覆盖显式认证。
- 钩子设置的 cookie 现在正确地持久化到 Session 中。
- 修复了 cookie 在其主机字段中指定端口号的问题。
- `BytesIO` 可用于执行流式上传。
- 更宽松地解析 `no_proxy` 环境变量。
- 非字符串对象可以与文件一起作为数据值传递。

## 1.2.3 (2013-05-25)

- 简单的打包修复

## 1.2.2 (2013-05-23)

- 简单的打包修复

## 1.2.1 (2013-05-20)

- 301 和 302 重定向现在将所有动词（不限于 POST）更改为 GET，提高了浏览器兼容性。
- Python 3.3.2 兼容性
- 始终对 location 头部进行百分比编码
- 修复连接适配器匹配以首先匹配最具体的
- 默认连接适配器的新参数，用于传递 block 参数
- 防止在没有 link 头部时出现 KeyError

## 1.2.0 (2013-03-31)

- 修复了会话和请求上的 cookie
- 显著改变了钩子分派方式——现在钩子接收用户在发出请求时指定的所有参数，因此钩子可以使用相同的参数发出辅助请求。这对于身份验证处理程序作者来说尤其必要。
- 移除了 certifi 支持
- 修复了使用 OAuth 1 和 body `signature_type` 时未发送数据的错误
- 感谢 @Lukasa 的重大代理工作，包括从代理 URL 解析代理身份验证
- 修复了 DigestAuth 处理过多 401 的问题
- 更新了 vendored urllib3 以包含 SSL 错误修复
- 允许通过 `Response.json()` 方法将关键字参数传递给 `json.loads()`
- 默认情况下，不对 `GET` 或 `HEAD` 请求发送 `Content-Length` 头部
- 为 `Response` 对象添加 `elapsed` 属性，以记录请求所需时间。
- 修复 `RequestsCookieJar`
- 会话和适配器现在是可序列化的，即可以与 multiprocessing 库一起使用
- 更新 charade 至 1.0.3 版本

  钩子分派方式的改变可能会导致许多问题。

## 1.1.0 (2013-01-10)

- 分块请求
- 支持可迭代的响应体
- 假定服务器保留重定向参数
- 允许为文件数据指定显式内容类型
- 在查找键时，使 merge_kwargs 不区分大小写

## 1.0.3 (2012-12-18)

- 修复文件上传编码错误
- 修复 cookie 行为

## 1.0.2 (2012-12-17)

- HTTPAdapter 的代理修复。

## 1.0.1 (2012-12-17)

- 证书验证异常错误。
- HTTPAdapter 的代理修复。

## 1.0.0 (2012-12-17)

- 大规模重构和简化
- 切换到 Apache 2.0 许可
- 可交换的连接适配器
- 可挂载的连接适配器
- 可变的处理请求链
- /s/prefetch/stream
- 移除了所有配置
- 标准库日志记录
- 使 Response.json() 可调用，而不是属性。
- 使用新的 charade 项目，该项目同时提供 python 2 和 3 的 chardet。
- 移除了除 'response' 之外的所有钩子
- 移除了所有身份验证辅助工具（OAuth、Kerberos）

  这不是一个向后兼容的更改。

## 0.14.2 (2012-10-27)

- 改进了 MIME 兼容的 JSON 处理
- 代理修复
- 路径 hack 修复
- 不区分大小写的 Content-Encoding 头部
- 支持表单 POST 中的 CJK 参数

## 0.14.1 (2012-10-01)

- Python 3.3 兼容性
- 简化默认 accept-encoding
- 错误修复

## 0.14.0 (2012-09-02)

- 如果已下载，不再出现 iter_content 错误。

## 0.13.9 (2012-08-25)

- 修复 OAuth + POSTs 问题
- 从 dispatch_hook 中移除异常吞噬
- 一般错误修复

## 0.13.8 (2012-08-21)

- 令人难以置信的 Link 头部支持 :)

## 0.13.7 (2012-08-19)

- 随处支持 (key, value) 列表。
- 摘要认证改进。
- 确保代理排除功能正常工作。
- 更清晰的 UnicodeError 异常。
- URL 自动转换为字符串（fURL 等）
- 错误修复。

## 0.13.6 (2012-08-06)

- 期待已久的挂起连接修复！

## 0.13.5 (2012-07-27)

- 打包修复

## 0.13.4 (2012-07-27)

- GSSAPI/Kerberos 身份验证！
- App Engine 2.7 修复！
- 修复连接泄露（来自 urllib3 更新）
- OAuthlib 路径 hack 修复
- OAuthlib URL 参数修复。

## 0.13.3 (2012-07-12)

- 如果可用，使用 simplejson。
- 不应将 SSLErrors 隐藏在 Timeouts 之后。
- 修复了处理包含片段的 URL 参数的问题。
- User Agent 中的信息显著改进。
- 当 verify=False 时，客户端证书将被忽略

## 0.13.2 (2012-06-28)

- 零依赖（再次）！
- 新增：Response.reason
- 在 OAuth 1.0 中签名查询字符串参数
- 当 verify=False 时，客户端证书不再被忽略
- 新增 openSUSE 证书支持

## 0.13.1 (2012-06-07)

- 允许将文件或文件类对象作为数据传递。
- 允许钩子返回指示错误的响应。
- 修复了 Response.text 和 Response.json 对无主体响应的处理。

## 0.13.0 (2012-05-29)

- 移除了 Requests.async，转而使用 [grequests](https://github.com/kennethreitz/grequests)
- 允许禁用 cookie 持久性。
- safe_mode 的新实现
- cookies.get 现在支持 default 参数
- 当 Session.request 以 return_response=False 调用时，Session cookie 不会被保存
- Env：no_proxy 支持。
- RequestsCookieJar 改进。
- 各种错误修复。

## 0.12.1 (2012-05-08)

- 新增 `Response.json` 属性。
- 添加字符串文件上传的能力。
- 修复 iter_lines 的越界问题。
- 修复 iter_content 默认大小。
- 修复包含文件的 POST 重定向。

## 0.12.0 (2012-05-02)

- 实验性 OAuth 支持！
- 带有出色字典式接口的、由 CookieJar 支持的正确 cookie 接口。
- 针对非迭代内容块的速度修复。
- 将 `pre_request` 移动到更可用的位置。
- 新增 `pre_send` 钩子。
- 延迟编码数据、参数、文件。
- 如果 `certify` 不可用，则加载系统证书捆绑包。
- 清理和修复。

## 0.11.2 (2012-04-22)

- 如果 `certifi` 不可用，则尝试使用操作系统的证书捆绑包。
- 无限摘要认证重定向修复。
- 多部分文件上传改进。
- 修复 URL 中无效 %encodings 的解码问题。
- 如果响应中没有内容，则在第二次尝试读取内容时不再抛出错误。
- 在重定向时上传数据。

## 0.11.1 (2012-03-30)

- POST 重定向现在违反 RFC，以实现浏览器行为：后续使用 GET。
- 新的 `strict_mode` 配置，用于禁用新的重定向行为。

## 0.11.0 (2012-03-14)

- 私有 SSL 证书支持
- 从 Gevent monkeypatching 中移除 select.poll
- 移除分块传输编码的冗余生成器
- 修复：Response.ok 在 safe_mode 下引发 Timeout 异常

## 0.10.8 (2012-03-09)

- 修复分块 ValueError
- 环境变量配置代理
- iter_lines 简化。
- 新的 trust_env 配置，用于禁用系统/环境提示。
- 抑制 cookie 错误。

## 0.10.7 (2012-03-07)

- encode_uri = False

## 0.10.6 (2012-02-25)

- 允许 cookie 中包含 '='。

## 0.10.5 (2012-02-25)

- 内容长度为 0 的响应主体修复。
- 新增 async.imap。
- 不要在 netrc 上失败。

## 0.10.4 (2012-02-20)

- 尊重 netrc。

## 0.10.3 (2012-02-20)

- HEAD 请求不再跟随重定向。
- raise_for_status() 不再对 3xx 抛出异常。
- 使 Session 对象可序列化。
- 无效方案 URL 的 ValueError。

## 0.10.2 (2012-01-15)

- 大幅改进的 URL 引用。
- 额外允许的 cookie 键值。
- 尝试修复“文件打开过多”错误
- 第一次通过时替换 Unicode 错误，不需要第二次通过。
- 在查询插入之前，将 '/' 附加到裸域 URL。
- 异常现在继承自 RuntimeError。
- 二进制上传 + 认证修复。
- 错误修复。

## 0.10.1 (2012-01-23)

- Python 3 支持！
- 停止支持 2.5 版本。（*向后不兼容*）

## 0.10.0 (2012-01-21)

- `Response.content` 现在仅为字节。（*向后不兼容*）
- 新的 `Response.text` 仅为 Unicode。
- 如果未指定 `Response.encoding` 且 `chardet` 可用，`Response.text` 将猜测编码。
- 文本子类型默认采用 ISO-8859-1 (Western) 编码。
- 移除了 decode_unicode。（*向后不兼容*）
- 新的多钩子系统。
- 新的 `Response.register_hook` 用于在管道中注册钩子。
- `Response.url` 现在是 Unicode。

## 0.9.3 (2012-01-18)

- SSL verify=False 错误修复（在 Windows 机器上明显）。

## 0.9.2 (2012-01-18)

- 异步 async.send 方法。
- 支持带边界的正确分块流。
- Session 类的 session 参数。
- 打印完整的钩子回溯，而不仅仅是异常实例。
- 修复 response.iter_lines 的挂起下一行问题。
- 修复 HTTP-digest 认证中 URI 包含查询字符串的错误。
- 事件钩子部分的修复。
- Urllib3 更新。

## 0.9.1 (2012-01-06)

- danger_mode 用于自动 Response.raise_for_status()
- Response.iter_lines 重构

## 0.9.0 (2011-12-28)

- 默认验证 SSL。

## 0.8.9 (2011-12-28)

- 打包修复。

## 0.8.8 (2011-12-28)

- SSL 证书验证！
- 发布 Cerifi：Mozilla 的证书列表。
- SSL 请求的新“verify”参数。
- Urllib3 更新。

## 0.8.7 (2011-12-24)

- iter_lines 最后一行的截断修复
- 强制 async 请求使用 safe_mode
- 更一致地处理 safe_mode 异常
- 修复 safe_mode 中空响应的迭代问题

## 0.8.6 (2011-12-18)

- Socket 超时修复。
- 代理授权支持。

## 0.8.5 (2011-12-14)

- Response.iter_lines！

## 0.8.4 (2011-12-11)

- 预取错误修复。
- 已安装版本中添加了许可证。

## 0.8.3 (2011-11-27)

- 将认证系统转换为使用更简单的可调用对象。
- API 方法的新会话参数。
- 记录时显示完整 URL。

## 0.8.2 (2011-11-19)

- 基于可覆盖的 Response.encoding 的新 Unicode 解码系统。
- 正确的 URL 斜杠引用处理。
- 允许使用包含 `[`, `]`, 和 `_` 的 cookie。

## 0.8.1 (2011-11-15)

- URL 请求路径修复
- 代理修复。
- 超时修复。

## 0.8.0 (2011-11-13)

- 持久连接支持！
- 完全移除 Urllib2
- 完全移除 Poster
- 完全移除 CookieJars
- 新的 ConnectionError 抛出
- safe_mode 用于错误捕获
- 请求方法的 prefetch 参数
- OPTION 方法
- 异步池大小节流
- 文件上传发送真实名称
- 捆绑 urllib3

## 0.7.6 (2011-11-07)

- 摘要认证错误修复（将查询数据附加到路径）

## 0.7.5 (2011-11-04)

- 如果响应无效，则 Response.content = None。
- 重定向认证处理。

## 0.7.4 (2011-10-26)

- 会话钩子修复。

## 0.7.3 (2011-10-23)

- 摘要认证修复。

## 0.7.2 (2011-10-23)

- PATCH 修复。

## 0.7.1 (2011-10-23)

- 放弃 urllib2 身份验证处理。
- 完全移除 AuthManager、AuthObject 等。
- 新的基于元组的身份验证系统，带处理程序回调。

## 0.7.0 (2011-10-22)

- 会话现在是主要接口。
- Deprecated InvalidMethodException。
- PATCH 修复。
- 新的配置系统（不再有全局设置）。

## 0.6.6 (2011-10-19)

- 会话参数错误修复（参数合并）。

## 0.6.5 (2011-10-18)

- 离线（快速）测试套件。
- 会话字典参数合并。

## 0.6.4 (2011-10-13)

- 基于 HTTP 头部自动解码 Unicode。
- 新的 `decode_unicode` 设置。
- 移除了 `r.read/close` 方法。
- 新的 `r.faw` 接口，用于高级响应用法。
- 参数化头部的自动扩展。

## 0.6.3 (2011-10-13)

- 漂亮的 `requests.async` 模块，用于使用 gevent 进行异步请求。

## 0.6.2 (2011-10-09)

- GET/HEAD 遵循 allow_redirects=False。

## 0.6.1 (2011-08-20)

- 增强的状态码体验 `\o/`
- 设置最大重定向次数（`settings.max_redirects`）
- 全 Unicode URL 支持
- 支持无协议重定向。
- 允许任意请求类型。
- 错误修复

## 0.6.0 (2011-08-17)

- 新的回调钩子系统
- 新的持久会话对象和上下文管理器
- 透明的 Dict-cookie 处理
- 状态码参考对象
- 移除了 Response.cached
- 添加了 Response.request
- 所有参数均为 kwargs
- 相对重定向支持
- HTTPError 处理改进
- 改进了 https 测试
- 错误修复

## 0.5.1 (2011-07-23)

- 国际化域名支持！
- 无需获取整个主体即可访问头部（`read()`）
- 将列表用作字典来表示参数
- 添加强制基本身份验证
- 强制基本身份验证是默认身份验证类型
- `python-requests.org` 默认 User-Agent 头部
- CaseInsensitiveDict 小写缓存
- Response.history 错误修复

## 0.5.0 (2011-06-21)

- PATCH 支持
- 代理支持
- HTTPBin 测试套件
- 重定向修复
- settings.verbose 流写入
- 所有方法的查询字符串
- URLErrors（连接被拒绝、超时、无效 URL）被视为显式引发的错误 `r.requests.get('hwe://blah'); r.raise_for_status()`

## 0.4.1 (2011-05-22)

- 改进的重定向处理
- 新增 'allow_redirects' 参数用于跟随非 GET/HEAD 重定向
- Settings 模块重构

## 0.4.0 (2011-05-15)

- Response.history：重定向响应列表
- 不区分大小写的头部字典！
- Unicode URL

## 0.3.4 (2011-05-14)

- Urllib2 HTTPAuthentication 递归修复（Basic/Digest）
- 内部重构
- 字节数据上传错误修复

## 0.3.3 (2011-05-12)

- 请求超时
- Unicode URL 编码数据
- Settings 上下文管理器和模块

## 0.3.2 (2011-04-15)

- GZip 编码内容的自动解压缩
- 元组 HTTP Auth 的自动身份验证支持

## 0.3.1 (2011-04-01)

- Cookie 更改
- Response.read()
- Poster 修复

## 0.3.0 (2011-02-25)

- 自动身份验证 API 更改
- 更智能的查询 URL 参数化
- 允许文件上传和 POST 数据同时进行
- 新的身份验证管理器系统
  - 更简单的基本 HTTP 系统
  - 支持所有内置的 urllib2 Auths
  - 允许自定义 Auth 处理程序

## 0.2.4 (2011-02-19)

- Python 2.5 支持
- PyPy-c v1.4 支持
- 自动身份验证测试
- 改进的 Request 对象构造函数

## 0.2.3 (2011-02-15)

- 新的 HTTP 处理方法
  - Response.__nonzero__（如果 HTTP 状态不佳则为 false）
  - Response.ok（如果 HTTP 状态符合预期则为 True）
  - Response.error（如果 HTTP 状态不佳则记录 HTTPError）
  - Response.raise_for_status()（抛出存储的 HTTPError）

## 0.2.2 (2011-02-14)

- 在 HTTPError 发生时仍处理请求。（问题 #2）
- Eventlet 和 Gevent Monkeypatch 支持。
- Cookie 支持（问题 #1）

## 0.2.1 (2011-02-14)

- 为 POST 和 PUT 请求添加了文件属性，用于 multipart-encode 文件上传。
- 添加了 Request.url 属性，用于上下文和重定向

## 0.2.0 (2011-02-14)

- 诞生！

## 0.0.1 (2011-02-13)

- 挫折
- 构思

Requests 库的发布说明到此结束。有关详细使用说明和更多信息，请参阅本文档的其他部分。
