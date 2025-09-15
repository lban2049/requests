# 社区

Requests 是一个充满活力的社区驱动项目，由 Kenneth Reitz 倾心创建，现在由 Python 软件基金会负责维护。其成功和稳健性，离不开世界各地数百名开发者的贡献。

<div style="display: flex; gap: 1rem; align-items: center;">
  <img src="../../../ext/kr.png" alt="Kenneth Reitz" width="150"/>
  <img src="../../../ext/psf.png" alt="Python 软件基金会" width="150"/>
</div>

该项目因协作而蓬勃发展，并始终欢迎新的贡献者。无论是修复错误、改进文档，还是提出新功能建议，你的贡献都至关重要。你可以在下方找到更多关于项目历史及其背后贡献者的信息。

<x-cards>
  <x-card data-title="更新日志" data-href="/community/changelog" data-icon="lucide:history">
    通过详细的日志了解项目的演变过程，其中记录了库每个版本的所有变更、改进和错误修复。
  </x-card>
  <x-card data-title="贡献者" data-href="/community/contributors" data-icon="lucide:users">
    查看所有慷慨奉献时间和专业知识，共同造就了如今 Requests 的个人贡献者完整列表。
  </x-card>
</x-cards>

## 如何贡献

为 Requests 做出贡献最有效的方式之一是报告错误。一份记录详尽的错误报告有助于维护者快速定位并修复问题。为了简化此流程，Requests 内置了一个辅助工具，用于收集相关的系统信息。

### 报告错误

在提交 issue 之前，请在终端中运行以下命令。该命令会收集你环境的基本信息，例如 Python 版本、操作系统以及 `urllib3` 和 `idna` 等关键依赖项的版本。

```bash
python -m requests.help
```

该命令将输出一个包含必要诊断信息的 JSON 对象。请将此输出内容包含在你的错误报告中。

**输出示例：**

```json
{
  "chardet": {
    "version": "5.2.0"
  },
  "charset_normalizer": {
    "version": null
  },
  "cryptography": {
    "version": "42.0.5"
  },
  "idna": {
    "version": "3.7"
  },
  "implementation": {
    "name": "CPython",
    "version": "3.12.3"
  },
  "platform": {
    "release": "23.5.0",
    "system": "Darwin"
  },
  "pyOpenSSL": {
    "openssl_version": "1111014f",
    "version": "24.1.0"
  },
  "requests": {
    "version": "2.32.3"
  },
  "system_ssl": {
    "version": "1111014f"
  },
  "urllib3": {
    "version": "2.2.1"
  },
  "using_pyopenssl": true,
  "using_charset_normalizer": true
}
```

提供这些数据有助于我们减少询问基本信息的时间，从而将更多精力投入到解决问题上。感谢你帮助我们共同维护 Requests 的可靠性和稳健性。
