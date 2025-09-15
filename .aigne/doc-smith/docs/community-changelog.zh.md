# 更新日志

此页面提供了 Requests 库的详细历史记录，记录了每个版本的所有重要变更、改进、错误修复和弃用。它可作为开发人员跟踪项目演进的全面日志。

## 未发布

- [非重大变更的简短描述。]

### 弃用

- 新增对 Python 3.14 的支持。
- 鉴于 Python 3.8 支持已结束，移除对其的支持。

## 2.32.4 (2025-06-10)

### 安全性

- **CVE-2024-47081**：修复了在恶意构造的 URL 和受信任的环境下，会从 netrc 文件中检索到错误主机名/机器凭据的问题。

### 改进

- 大量文档改进

### 弃用

- 新增对 Linux 和 macOS 上 pypy 3.11 的支持。
- 鉴于 pypy 3.9 支持已结束，移除对其的支持。

## 2.32.3 (2024-05-29)

### 错误修复

- 修复了在 `HTTPAdapter` 的子类中无法指定自定义 SSLContexts 的问题。(#6716)
- 修复了 Requests 在未使用 `ssl` 模块编译的 Python 版本上无法运行的问题。(#6724)

## 2.32.2 (2024-05-21)

### 弃用

- 为了给受 2.32.0 版本中 CVE 变更影响的自定义 `HTTPAdapter` 提供更稳定的迁移路径，我们将 `_get_connection` 重命名为一个新的公共 API `get_connection_with_tls_context`。现有的自定义 `HTTPAdapter` 需要迁移其代码以使用此新 API。在所有 Requests >= 2.32.0 的版本中，`get_connection` 均被视为已弃用。

  在关联的 PR 中提供了一个最小（2 行）示例以便于迁移，但我们强烈建议用户评估其自定义适配器是否会受到 CVE-2024-35195 中描述的相同问题的影响。(#6710)

## 2.32.1 (2024-05-20)

### 错误修复

- 将缺失的测试证书添加到 PyPI 上分发的 sdist 中。

## 2.32.0 (2024-05-20)

### 安全性

- 修复了在 `Session` 的第一个请求中设置 `verify=False` 会导致后续对*同一来源*的请求也忽略证书验证的问题，无论 `verify` 的值如何。(GHSA-9wx4-h78v-vm56)

### 改进

- `verify=True` 现在会重用一个全局 SSLContext，这应能改善首次请求与后续请求之间的时间差异。当使用基于 OpenSSL 3.x 构建的 Python 版本时，它还应能最大限度地减少 Windows 系统上的证书加载时间。(#6667)
- 当重新打包或 vendoring 时，Requests 现在支持可选地使用字符检测（`chardet` 或 `charset_normalizer`）。这使得 `pip` 和其他项目能够最大限度地减小其 vendoring 范围。如果这两个库都不存在，`Response.text()` 和 `apparent_encoding` API 将默认使用 `utf-8`。(#6702)

### 错误修复

- 修复了长度检测中的一个错误，该错误导致请求内容长度中 emoji 的长度计算不正确。(#6589)
- 修复了 `JSONDecodeError` 中的反序列化错误。(#6629)
- 修复了多余的前导 `/`（路径分隔符）可能导致 urllib3 不必要地重新解析请求 URI 的问题。(#6644)

### 弃用

- Requests 已正式新增对 CPython 3.12 的支持 (#6503)
- Requests 已正式新增对 PyPy 3.9 和 3.10 的支持 (#6641)
- Requests 已正式移除对 CPython 3.7 的支持 (#6642)
- Requests 已正式移除对 PyPy 3.7 和 3.8 的支持 (#6641)

### 文档

- 修复了各种拼写错误并改进了文档。

### 打包

- Requests 已开始采用一些现代打包实践。项目的源文件（以前是 `requests`）现在位于 Requests sdist 中的 `src/requests` 目录。(#6506)
- 从 Requests 2.33.0 开始，Requests 将迁移到使用 `hatchling` 的 PEP 517 构建系统。这应该不会影响普通用户，但极旧版本的打包工具可能会在新打包格式上遇到问题。

## 2.31.0 (2023-05-22)

### 安全性

- **CVE-2023-32681**：v2.3.0 到 v2.30.0 之间的 Requests 版本在处理 HTTPS 重定向时，容易将 `Proxy-Authorization` 标头转发到目标服务器。

  当代理使用用户信息（`https://user:pass@proxy:8080`）定义时，Requests 会构造一个 `Proxy-Authorization` 标头并附加到请求中，用于向代理进行身份验证。

  在 Requests 收到重定向响应的情况下，它之前会错误地重新附加 `Proxy-Authorization` 标头，导致该值通过隧道连接发送到目标服务器。*强烈*建议依赖在 URL 中定义代理凭据的用户升级到 Requests 2.31.0+，以防止凭据意外泄露，并在变更完全部署后轮换其代理凭据。

  不使用代理或不通过代理 URL 的用户信息部分提供代理凭据的用户不受此漏洞影响。

  完整详情可在我们的 [Github 安全公告](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) 中阅读。

## 2.30.0 (2023-05-03)

### 依赖项

- ⚠️ 新增对 urllib3 2.0 的支持。⚠️

  这可能包含微小的破坏性变更，因此我们建议在升级前仔细测试并查阅 [urllib3 v2 迁移指南](https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html)。

  希望继续使用 urllib3 1.x 的用户可以固定版本为 `urllib3<2`。

## 2.29.0 (2023-04-26)

### 改进

- Requests 现在将分块请求交由 urllib3 实现处理，以提高标准化程度。(#6226)
- Requests 放宽了标头组件的要求，以支持 bytes/str 子类。(#6356)

## 2.28.2 (2023-01-12)

### 依赖项

- Requests 现在支持 `charset_normalizer` 3.x。(#6261)

### 错误修复

- 更新了 `MissingSchema` 异常，建议使用 `https` 协议而不是 `http`。(#6188)

## 2.28.1 (2022-06-29)

### 改进

- 通过过渡到 `yield from`，优化了 `iter_content` 的速度。(#6170)

### 依赖项

- 新增对 chardet 5.0.0 的支持 (#6179)
- 新增对 charset-normalizer 2.1.0 的支持 (#6169)

## 2.28.0 (2022-06-09)

### 弃用

- ⚠️ Requests 已正式移除对 Python 2.7 的支持。⚠️ (#6091)
- Requests 已正式移除对 Python 3.6（包括 pypy3.6）的支持。(#6091)

### 改进

- 对于没有编码的有效负载，将 JSON 解析问题包装在 Request 的 `JSONDecodeError` 中，以使 `json()` API 保持一致。(#6097)
- 一致地解析标头组件，在所有无效情况下引发 `InvalidHeader` 错误。(#6154)
- 基于当前的 beta 构建，新增了对 3.11 的临时支持。(#6155)
- Requests 进行了改造，我们决定使用 black 格式化工具。(#6095)

### 错误修复

- 修复了将 `CURL_CA_BUNDLE` 设置为空字符串会导致证书验证禁用的问题。所有 2.28.0 之前的 Requests 2.x 版本都受此影响。(#6074)
- 修复了 urllib3 异常泄漏问题，将 `content` 和 `iter_content` 中的 `urllib3.exceptions.SSLError` 包装为 `requests.exceptions.SSLError`。(#6057)
- 修复了无效的 Windows 注册表项导致代理解析引发异常而不是忽略该条目的问题。(#6149)
- 修复了 `JSONDecodeError` 的错误消息中可能包含整个有效负载的问题。(#6036)

## 2.27.1 (2022-01-05)

### 错误修复

- 修复了导致代理 URL 中 `auth` 组件被丢弃的解析问题。(#6028)

## 2.27.0 (2022-01-03)

### 改进

- 正式新增对 Python 3.10 的支持。(#5928)
- 新增了 `requests.exceptions.JSONDecodeError` 以统一 Python 2 和 3 之间的 JSON 异常。此异常在 `response.json()` 方法中引发，并且由于它继承自先前抛出的异常，因此是向后兼容的。也可以通过 `requests.exceptions.RequestException` 捕获。(#5856)
- 改进了命名错误的 `InvalidSchema` 和 `MissingSchema` 异常的错误文本。这是一个临时修复，直到异常可以被重命名（Schema->Scheme）。(#6017)
- 改进了对缺少协议的代理 URL 的解析。这将解决 Python 3.9+ 中 `urlparse` 的近期变更。(#5917)

### 错误修复

- 修复了 `extract_zipped_paths` 中的一个缺陷，该缺陷可能导致某些路径出现无限循环。(#5851)
- 修复了在计算通过 `Tarfile.extractfile()` 获取的文件长度时对 `AttributeError` 的处理。(#5239)
- 修复了 urllib3 异常泄漏问题，将 `urllib3.exceptions.InvalidHeader` 包装为 `requests.exceptions.InvalidHeader`。(#5914)
- 修复了分块请求会发送两个 Host 标头的问题。(#5391)
- 修复了 Requests 2.26.0 中的一个回归问题，即 `Proxy-Authorization` 会被从所有使用 `Session.send` 发送的请求中错误地移除。(#5924)
- 修复了 2.26.0 版本中，对于环境中存在大量可用代理的主机时的性能回归问题。(#5924)
- 修复了 idna 异常泄漏问题，将域名中带前导点 (.) 的 URL 引发的 `UnicodeError` 包装为 `requests.exceptions.InvalidURL`。(#5414)

### 弃用

- Requests 对 Python 2.7 和 3.6 的支持将于 2022 年结束。虽然没有确切日期，但 Requests 2.27.x 很可能是提供支持的最后一个版本系列。

## 2.26.0 (2021-07-13)

### 改进

- 如果安装了 `brotli` 或 `brotlicffi` 包，Requests 现在支持 Brotli 压缩。(#5783)
- `Session.send` 现在可以正确地从 Session 和 Request 中解析代理配置。其行为现在与 `Session.request` 一致。(#5681)

### 错误修复

- 修复了从 zip 存档并行使用 Requests 时在 zip 提取中出现的竞争条件问题。(#5707)

### 依赖项

- 对于 Python 3，使用 MIT 许可的 `charset_normalizer` 代替 `chardet`，以消除捆绑 requests 的项目的许可证歧义。如果你的机器上已安装 `chardet`，则会使用它而不是 `charset_normalizer` 以保持向后兼容性。(#5797)

你也可以在安装 requests 时通过指定 `[use_chardet_on_py3]` extra 来安装 `chardet`，如下所示：

```shell
pip install "requests[use_chardet_on_py3]"
```

Python 2 仍然依赖 `chardet` 模块。

- Requests 现在在 Python 3 上支持 `idna` 3.x。在 Python 2 安装中将继续使用 `idna` 2.x。(#5711)

### 弃用

- `requests[security]` extra 已变为空操作安装。PyOpenSSL 不再是 Requests 推荐的安全选项。(#5867)
- Requests 已正式移除对 Python 3.5 的支持。(#5867)

## 2.25.1 (2020-12-16)

### 错误修复

- Requests 现在默认将 `application/json` 视为 `utf8`。解决了 `r.text` 和 `r.json` 输出之间的不一致问题。(#5673)

### 依赖项

- Requests 现在支持 chardet v4.x。

## 2.25.0 (2020-11-11)

### 改进

- 新增对 `NETRC` 环境变量的支持。(#5643)

### 依赖项

- Requests 现在支持 urllib3 v1.26。

### 弃用

- Requests v2.25.x 将是最后一个支持 Python 3.5 的版本系列。
- `requests[security]` extra 已被正式弃用，并将在 Requests v2.26.0 中移除。

... 以此类推，适用于所有以前的版本。