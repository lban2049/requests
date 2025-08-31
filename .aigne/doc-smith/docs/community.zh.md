# 社区

Requests 是当今下载量最大的 Python 包之一，有超过 1,000,000 个代码库依赖它。这一成功建立在强大的开发者和用户社区之上。该项目由 Kenneth Reitz 创建，目前由 Python 软件基金会维护。

本节将深入介绍该项目的历史，向塑造了它的众多贡献者致谢，并说明您如何参与其中。

<x-cards>
  <x-card data-title="更新日志" data-icon="lucide:history" data-href="/community/changelog">
    探索 Requests 每个版本的详细历史，从初始版本到最新更新。了解功能如何演变以及漏洞如何随时间修复。
  </x-card>
  <x-card data-title="贡献者" data-icon="lucide:users" data-href="/community/contributors">
    认识 Requests 背后的贡献者。该项目是世界各地无数个人贡献的结晶，由一个专注的团队进行维护。
  </x-card>
</x-cards>

## 如何贡献

我们欢迎所有人的贡献。无论是报告漏洞、提出改进建议，还是提交拉取请求，您的帮助都很有价值。典型的贡献工作流程如下：

```d2
direction: down

find_issue: "发现漏洞或有新想法"
open_issue: "在 GitHub 上提交问题"
discuss: "与维护者讨论"
fork: "Fork 仓库"
branch: "创建新分支"
code: "编写代码并添加测试"
run_tests: "在本地运行测试"
pr: "提交拉取请求"
review: "代码审查"
merge: "合并到主分支" {
  shape: circle
}

find_issue -> open_issue
open_issue -> discuss
discuss -> fork
fork -> branch
branch -> code
code -> run_tests: "python -m pytest tests"
run_tests -> pr
pr -> review
review -> merge
```

### 报告漏洞

当您遇到漏洞时，提供有关您环境的详细信息对于故障排查至关重要。Requests 包含一个内置的辅助脚本来收集和格式化这些信息。

要生成漏洞报告摘要，请运行以下命令：

```shell
python -m requests.help
```

该命令将输出一个 JSON 对象，其中包含有关您的 Python 环境、已安装的依赖项和 SSL 版本的详细信息。请在您的漏洞报告中包含此输出。

### 设置开发环境

要参与 Requests 代码库的开发，您首先需要克隆该仓库。由于一个旧提交时间戳的已知问题，您可能需要添加一个标志以避免错误：

```shell
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

或者，您可以将此设置应用于您的全局 Git 配置：

```shell
git config --global fetch.fsck.badTimezone ignore
```

克隆后，您可以安装开发依赖项并运行测试套件以验证您的设置：

```shell
# 安装开发依赖项
python -m pip install -r requirements-dev.txt

# 运行测试
python -m pytest tests
```

Requests 的优势在于其社区。通过浏览[更新日志](./community-changelog.md)和[贡献者](./community-contributors.md)列表，您可以看到使这个库强大而可靠的协作成果。