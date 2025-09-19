# 贡献指南

我们欢迎并感谢社区的贡献！如果您希望帮助改进 Requests 库，本指南将引导您完成设置开发环境、运行测试和构建文档的过程。

## 克隆仓库

首先，您需要一份源代码的本地副本。克隆 Requests 仓库时，您可能会遇到与提交时间戳错误相关的问题。为避免此问题，您应使用一个特殊的 git 标志。

使用以下命令克隆仓库：

```shell 克隆仓库
$ git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

或者，您可以将此设置应用于您的全局 Git 配置，以避免在每次执行克隆命令时都指定它：

```shell 全局 Git 配置
$ git config --global fetch.fsck.badTimezone ignore
```

## 设置开发环境

获取代码后，您需要设置一个环境来安装开发和测试所需的依赖项。强烈建议为此使用 Python 虚拟环境。

1.  **创建并激活虚拟环境：**

    ```console 创建虚拟环境
    $ python -m venv venv
    $ source venv/bin/activate  # 在 Windows 上使用 `venv\Scripts\activate`
    ```

2.  **安装开发依赖项：**

    该项目包含一个 `requirements-dev.txt` 文件，其中列出了测试和开发所需的所有软件包，例如 `pytest`、`pytest-cov` 和 `pytest-httpbin`。您可以使用 pip 安装它们：

    ```console 安装依赖项
    $ python -m pip install -r requirements-dev.txt
    ```

## 运行测试

在提交任何更改之前，运行测试套件至关重要，以确保您的更改不会破坏现有功能。该项目使用 `pytest` 进行测试。

根据您的需求，有多种方法可以运行测试。

-   **运行标准测试套件：**

    ```console 运行基本测试
    $ python -m pytest tests
    ```

-   **生成测试覆盖率报告：**

    要检查代码库被测试覆盖的程度，请运行覆盖率命令。这将在终端输出一份报告，并创建一个 XML 文件。

    ```console 生成覆盖率报告
    $ python -m pytest --cov-config .coveragerc --verbose --cov-report term --cov-report xml --cov=src/requests tests
    ```

-   **为持续集成 (CI) 运行测试：**

    此命令会运行测试并生成一个 JUnit XML 格式的 `report.xml` 文件，该格式通常被 CI 系统使用。

    ```console 运行 CI 测试
    $ python -m pytest tests --junitxml=report.xml
    ```

## 构建文档

如果您的贡献涉及文档更改，您应在本地构建文档以预览更改并确保所有内容均正确呈现。

要构建 HTML 文档，请导航到 `docs` 目录并使用 `make`：

```console 构建文档
$ cd docs && make html
```

构建成功后，您可以通过在浏览器中打开以下文件来查看生成的文档：

`docs/_build/html/index.html`
