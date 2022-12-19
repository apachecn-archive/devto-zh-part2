# 您使用什么工具来验证 Jenkins 管道语法

> 原文：<https://dev.to/ferricoxide/what-tools-do-you-use-to-validate-jenkins-pipeline-syntax-5bii>

我为之工作的一个客户最近决定，除了提供针对他们想要的云托管服务的本地部署自动化代码之外，他们还希望我们为这些代码提供 Jenkins 管道包装器。

在这个客户请求之前，我们总是使用 TravisCI 来描述交付的代码。对于这个项目，我们主要交付 BASH 脚本和 CloudFormation 模板。为了进行基本的语法验证，我们使用:

*   用于 BASH 脚本的 ShellCheck——过去是我们必须下载的模块，作为`.travis.yml`的一部分，但是现在似乎可以作为 Travis 容器标准工具的内置模块
*   `jq`或者甚至只是 python 的`json.tool`——前者的错误输出更具描述性。

现在我们正在添加 Jenkins jobs，为管道定义寻找类似的语法验证器。我想我应该在这里张贴，看看是否有人知道什么。使用 Jenkins pipeline guide 中的[命令行 Pipeline Linter](https://dev.toCommand-line%20Pipeline%20Linter) 对于一个由 GitHub 托管的公共项目来说似乎并不实际。