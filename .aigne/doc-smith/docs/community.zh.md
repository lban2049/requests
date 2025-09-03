# 社区

Requests 是一个由社区驱动的项目。作为当今下载量最大的 Python 包之一，它每周的下载量约为 3000 万次，并被超过一百万个代码仓库所依赖。如此广泛的应用，充分证明了使用、支持和为该库做出贡献的开发者社区的活力。

Requests 最初由 Kenneth Reitz 创建，现在由 Python 软件基金会 (PSF) 负责维护，以确保其在未来的持续发展和稳定。

![Python 软件基金会](https://raw.githubusercontent.com/psf/requests/main/ext/psf.png)

## 探索社区

Requests 社区由开发者、贡献者和维护者组成。你可以通过以下方式了解更多关于该项目的历史及其幕后人员的信息。

<x-cards data-columns="2">
  <x-card data-title="更新日志" data-icon="lucide:list-checks" data-href="/community/changelog" data-cta="查看更新日志">
    及时了解最新的开发进展、错误修复和功能增强。我们详细的更新日志提供了 Requests 每个版本的完整历史记录。
  </x-card>
  <x-card data-title="贡献者" data-icon="lucide:users" data-href="/community/contributors" data-cta="查看贡献者">
    Requests 的成功离不开众多贡献出时间和专业知识的个人。查看完整列表，了解是哪些维护者和贡献者塑造了该项目。
  </x-card>
</x-cards>

## 参与贡献

无论是报告错误还是贡献代码，我们都欢迎你的参与。

### 报告错误

如果你遇到错误，提供详细的报告是最有效的帮助方式。Requests 包含一个内置的辅助工具，可以生成你的系统配置摘要，帮助我们更快地诊断问题。

要使用它，请在你的终端中运行以下命令：

```shell
python -m requests.help
```

这条命令将输出一个包含 Requests 及其依赖项版本信息的 JSON 对象，你可以将其附在错误报告中。

### 贡献代码

准备好贡献代码了吗？首先需要设置本地开发环境。你需要先克隆代码仓库。由于历史提交时间戳的问题，你可能需要添加一个特定的 git 配置标志以避免出错：

```shell
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

或者，你可以将此设置应用于你的全局 Git 配置：

```shell
git config --global fetch.fsck.badTimezone ignore
```

克隆仓库后，你可以安装开发依赖项并运行测试套件，以确保一切设置正确。

---

社区是 Requests 的支柱。我们鼓励你参与进来，帮助我们继续为所有人构建一个简单而优雅的 HTTP 库。要了解有关如何使用该库的更多信息，请前往[用户指南](./user-guide.md)。