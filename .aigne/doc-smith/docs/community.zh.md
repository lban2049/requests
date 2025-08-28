# 社区

Requests 是当今下载量最高的 Python 包之一，有超过 1,000,000 个代码库依赖它。这一成功建立在强大的开发者和用户社区之上。本节将深入介绍该项目的历史，向众多贡献者致敬，并说明你如何参与其中。

该项目由 Kenneth Reitz 创建，目前由 Python 软件基金会负责维护。

<x-cards>
  <x-card data-title="更新日志" data-icon="lucide:history" data-href="/community/changelog">
    探索 Requests 每个版本的详细历史，从最初版本到最新更新。了解功能如何演变以及 bug 如何被修复。
  </x-card>
  <x-card data-title="贡献者" data-icon="lucide:users" data-href="/community/contributors">
    认识 Requests 背后的贡献者。该项目是世界各地无数人贡献的结晶，由一个专注的团队进行维护。
  </x-card>
</x-cards>

## 如何贡献

我们欢迎所有人的贡献。无论是报告 bug、提出改进建议，还是提交拉取请求，你的帮助都非常有价值。

### 报告 Bug

当你遇到 bug 时，提供有关你环境的详细信息对于排查问题至关重要。Requests 包含一个内置的辅助脚本来收集和格式化这些信息。

要生成 bug 报告摘要，请运行以下命令：

```shell
python -m requests.help
```

该命令将输出一个 JSON 对象，其中包含有关你的 Python 环境、已安装依赖项和 SSL 版本的详细信息。请在你的 bug 报告中附上此输出。

### 设置开发环境

要参与 Requests 代码库的开发，你首先需要克隆该仓库。由于一个旧提交时间戳的已知问题，你可能需要添加一个标志以避免出错：

```shell
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

或者，你也可以将此设置应用于你的全局 Git 配置：

```shell
git config --global fetch.fsck.badTimezone ignore
```

克隆仓库后，你可以安装开发依赖项并运行测试套件来验证你的设置：

```shell
# 安装开发依赖项
python -m pip install -r requirements-dev.txt

# 运行测试
python -m pytest tests
```

Requests 的优势在于其社区。通过浏览 [更新日志](./community-changelog.md) 和 [贡献者](./community-contributors.md) 列表，你可以看到正是这种协作精神使得这个库强大而可靠。