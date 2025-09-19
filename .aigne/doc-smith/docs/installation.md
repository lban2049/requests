# Installation

This guide will walk you through installing the Requests library, its supported Python versions, and its dependencies.

## Standard Installation

Requests is available on PyPI (the Python Package Index). The recommended way to install it is using `pip`, Python's package installer. Open your terminal and run the following command:

```console Installing Requests icon=logos:python
$ python -m pip install requests
```

This command will download the latest official version of Requests and install it in your Python environment.

## Supported Python Versions

Requests officially supports **Python 3.9+**. While it might run on other versions, only the following versions are actively tested and guaranteed to work.

| Python Version | Status    |
| :------------- | :-------- |
| 3.9            | Supported |
| 3.10           | Supported |
| 3.11           | Supported |
| 3.12           | Supported |
| 3.13           | Supported |
| 3.14           | Supported |

If you are using an older version of Python, you will need to install an older version of Requests (`<2.32.0`).

## Dependencies

When you install Requests, a few core libraries that it depends on are automatically installed. You don't need to install these manually, but it can be helpful to know what they are.

| Package              | Version Constraint   |
| :------------------- | :------------------- |
| `charset_normalizer` | `>=2,<4`             |
| `idna`               | `>=2.5,<4`           |
| `urllib3`            | `>=1.21.1,<3`        |
| `certifi`            | `>=2017.4.17`        |

### Optional Features

Requests also includes optional features that require additional dependencies. These can be installed using "extras". For example, to add SOCKS proxy support, you can install the `socks` extra:

```console Installing with SOCKS support icon=logos:python
$ python -m pip install requests[socks]
```

This will install the `PySocks` package alongside Requests.

---

With Requests successfully installed, you're ready to start making HTTP requests. Let's move on to [Core Usage](./core-usage.md) to see how it works.