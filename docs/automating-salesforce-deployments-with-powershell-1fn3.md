# 使用 PowerShell 自动化 Salesforce 部署

> 原文：<https://dev.to/manzanit0/automating-salesforce-deployments-with-powershell-1fn3>

最近，我一直在学习一点点 [PowerShell](https://docs.microsoft.com/en-us/powershell/) ，最初是为了自动化一些我在工作中不得不做的繁琐任务，比如调用 REST 端点 10-20 次来填充一些 DB 进行测试，或者简单地用管道传输一些 CLI 命令。

问题是，随着我对 PS 越来越熟悉，我开始意识到脚本可以做大量的事情，让我们的日常生活变得更加轻松。我想到的最大的一个问题是销售人员部署。

通常，一旦我们在 Salesforce 中完成了某个功能的开发，我们就运行所有测试，从我们开发的环境中提取所有更改(在 Salesforce 中，*您可以远程开发，而不是本地开发*)，部署到一个试运行环境中，并在那里再次运行测试。

问题是这个过程涉及许多手动步骤。所以我决定尝试自动化它。为此，我研究了一下 [SFDX CLI](https://developer.salesforce.com/tools/sfdxcli) ，并尝试重用它的大部分特性:身份验证命令、调用部署端点的实际命令等。

最终，几个小时后，结果是这样的: