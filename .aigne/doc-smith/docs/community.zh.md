# 社区

Requests 是一个社区驱动的项目，由 Kenneth Reitz 精心创建，并由一个专注的志愿者团队进行维护。它已成为下载量最大的 Python 包之一，每周下载量约为 3000 万次，并且是 GitHub 上超过一百万个仓库的依赖项。这一成功证明了支持并为其发展做出贡献的充满活力的社区。本节将深入介绍该项目的历史、贡献者以及如何参与其中。

## 项目历史与贡献者

该项目有着悠久而丰富的演变历史，从最初的构想到如今成为 Python 生态系统的核心部分。其开发由“Keepers of the Crystals”指导，其成功建立在世界各地开发者的无数贡献之上。

要了解该项目的历程并向其背后的人们致敬，请访问以下专属页面：

<x-cards>
  <x-card data-title="更新日志" data-icon="lucide:history" data-href="/community/changelog">
    Requests 库每个版本的所有变更、改进和错误修复的详细日志。
  </x-card>
  <x-card data-title="贡献者" data-icon="lucide:users" data-href="/community/contributors">
    为 Requests 库的开发和成功做出贡献的所有人员的列表。
  </x-card>
</x-cards>

## 如何参与

我们随时欢迎各种贡献，无论是报告错误、改进文档还是提交补丁。以下是您的入门方法。

### 报告错误

如果您遇到错误，可以通过提供详细报告来帮助我们。Requests 包含一个有用的工具，可以生成系统配置摘要，这对于调试非常有价值。

请运行以下命令，并将输出包含在您的错误报告中：

```shell title="生成系统信息"
python -m requests.help
```

### 贡献代码和文档

如果您想直接为项目做出贡献，可以从设置本地开发环境开始。

1.  **克隆仓库**

    由于提交时间戳存在已知问题，建议使用以下命令克隆仓库：

    ```shell title="克隆仓库"
    git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
    cd requests
    ```

2.  **安装开发依赖项**

    使用 pip 安装测试和开发所需的包。

    ```shell title="安装依赖项"
    python -m pip install -r requirements-dev.txt
    ```

3.  **运行测试**

    在进行任何更改之前，请确保所有现有测试都能通过。

    ```shell title="运行测试"
    python -m pytest tests
    ```

4.  **构建文档**

    如果您要对文档进行更改，可以在本地构建它以预览您的更改。

    ```shell title="构建文档"
    make docs
    ```

在完成更改并添加相关测试后，您可以在 GitHub 上提交拉取请求以供审核。