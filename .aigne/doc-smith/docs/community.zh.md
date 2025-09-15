# 社区

Requests 是当今下载量最高的 Python 包之一，每周下载量约为 **3000 万次**。根据 GitHub 的数据，目前有超过 **1,000,000** 个代码库依赖它。这一成功证明了其简洁、优雅的设计以及支持它的充满活力的社区。

该项目由 Kenneth Reitz 创建，现在由一个名为“Keepers of the Crystals”的专门团队进行维护，确保其稳定性和持续发展。

<x-cards>
  <x-card data-title="更新日志" data-icon="lucide:list-ordered" data-href="/community/changelog" >
    浏览 Requests 每个版本的完整变更历史，包括新功能、错误修复和弃用项。
  </x-card>
  <x-card data-title="贡献者" data-icon="lucide:users" data-href="/community/contributors" >
    查看为 Requests 贡献时间和专业知识，使其成为如今强大工具的所有人员的完整列表。
  </x-card>
</x-cards>

## 如何贡献

我们欢迎并感谢各种形式的贡献，从错误报告、文档改进到新功能建议。以下是入门方法。

### 设置你的环境

首先，你需要克隆代码库。由于一个历史提交，你可能需要添加一个特殊的配置标志以避免错误：

```shell 克隆代码库 icon=lucide:git-branch
# 使用必要的配置标志克隆
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git

# 或者，将该设置应用到你的全局 Git 配置
git config --global fetch.fsck.badTimezone ignore
```

获取代码后，你可以安装开发依赖项并运行测试套件，以确保一切正常。

```shell 运行测试 icon=lucide:beaker
# 进入克隆的目录
cd requests

# 安装开发依赖项
python -m pip install -r requirements-dev.txt

# 运行测试套件
python -m pytest tests
```

### 报告错误

一份好的错误报告对维护者有很大帮助。为确保我们拥有关于你环境的所有必要信息，Requests 包含一个内置的辅助模块。

在创建 issue 之前，请运行以下命令并将其输出包含在你的报告中：

```shell 生成错误报告 icon=lucide:bug
python -m requests.help
```

该命令将打印一个 JSON 对象，其中包含有关你的 Python 版本、系统以及 `urllib3`、`idna` 和 `OpenSSL` 等关键依赖项版本的信息，这对调试非常有价值。

## 后续步骤

在浏览我们的社区资源后，你可能希望更深入地了解该库的功能。

<x-cards>
  <x-card data-title="用户指南" data-icon="lucide:book-open" data-href="/user-guide" >
    通过实用的、代码优先的示例学习 Requests 的核心功能。
  </x-card>
  <x-card data-title="高级用法" data-icon="lucide:rocket" data-href="/advanced-usage" >
    探索自定义适配器、SSL 验证和代理等高级功能。
  </x-card>
</x-cards>