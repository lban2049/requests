# 安装

本指南将引导您完成 Requests 库的安装，并介绍其支持的 Python 版本和依赖项。

## 标准安装

Requests 已发布在 PyPI (Python Package Index)上。推荐使用 Python 的包安装工具 `pip` 来进行安装。打开您的终端并运行以下命令：

```console Installing Requests icon=logos:python
$ python -m pip install requests
```

该命令将下载最新官方版本的 Requests 并将其安装到您的 Python 环境中。

## 支持的 Python 版本

Requests 官方支持 **Python 3.9+**。虽然它可能在其他版本上运行，但只有以下版本经过了积极测试并保证可以正常工作。

| Python 版本 | 状态    |
| :------------- | :-------- |
| 3.9            | 支持 |
| 3.10           | 支持 |
| 3.11           | 支持 |
| 3.12           | 支持 |
| 3.13           | 支持 |
| 3.14           | 支持 |

如果您使用的是旧版 Python，则需要安装旧版 Requests (`<2.32.0`)。

## 依赖项

当您安装 Requests 时，它所依赖的一些核心库会自动安装。您无需手动安装这些库，但了解它们是什么会很有帮助。

| 包              | 版本约束   |
| :------------------- | :------------------- |
| `charset_normalizer` | `>=2,<4`             |
| `idna`               | `>=2.5,<4`           |
| `urllib3`            | `>=1.21.1,<3`        |
| `certifi`            | `>=2017.4.17`        |

### 可选功能

Requests 还包含一些需要额外依赖项的可选功能。这些功能可以通过“extras”来安装。例如，要添加 SOCKS 代理支持，您可以安装 `socks` extra：

```console Installing with SOCKS support icon=logos:python
$ python -m pip install requests[socks]
```

这将在安装 Requests 的同时安装 `PySocks` 包。

---

成功安装 Requests 后，您就可以开始发起 HTTP 请求了。让我们继续阅读 [核心用法](./core-usage.md) 来了解其工作原理。