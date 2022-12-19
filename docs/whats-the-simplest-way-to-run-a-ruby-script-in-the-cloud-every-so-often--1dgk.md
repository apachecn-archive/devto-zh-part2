# 偶尔在云中运行 Ruby 脚本的最简单方法是什么？

> 原文：<https://dev.to/ben/whats-the-simplest-way-to-run-a-ruby-script-in-the-cloud-every-so-often--1dgk>

我偶尔会遇到想要重新运行 Ruby 脚本的情况。我想知道在云中运行它的最简单的方法。它需要做的就是每 x 秒或每分钟执行一个脚本，等等。它应该能够通过一个`Gemfile`安装宝石，就是这样。如果任何东西需要任何 Ruby 之外的特殊构建，我不需要它。

在过去，我有一些方法可以做到这一点，但总是有一些额外的开销和样板文件，我觉得我可以不这样做。我所寻求的是尽可能最好的开发者体验。

对于我还没有真正尝试过的事情，AWS Lambda + [预定事件](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/RunLambdaSchedule.html)似乎是一个不错的选择。目前我还不清楚能否在 Lambda 中运行带有 gem 依赖的 Ruby。我使用过这项服务，但没有和 Ruby 一起使用。

我也不拘泥于 Ruby，但它是我简单脚本的首选语言，如果我能同样简单地部署它们，我会很高兴。所有的输入都是受欢迎的，包括真正实现简单性目标的非 Ruby 内容。