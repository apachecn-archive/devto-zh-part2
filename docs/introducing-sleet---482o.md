# 介绍雨夹雪☁️ ❄️

> 原文：<https://dev.to/coreyja/introducing-sleet---482o>

**原帖[coreyja.com](https://coreyja.com/introducing-sleet/)2018-01-15**

我非常兴奋地向大家介绍我正在开发的新宝石，名为 [Sleet](https://github.com/coreyja/sleet) ！它在很大程度上仍处于 alpha/beta 阶段(目前版本为 0.3.1)，但目前已经可以使用了！

## 什么是雨夹雪？

如果你使用 Rspec 和 CircleCI，Sleet 是一个加速 Ruby 工作流的工具。它允许您运行一个简单的命令`sleet`，然后只运行在您最近的 CircleCI 构建中失败的例子！

这是通过从单个 CircleCI 容器中下载持久化文件，然后将它们组合起来，这样您就可以在本地拥有一个持久化文件。

## 为什么叫雨夹雪？

`Sleet`因思考沉淀而产生。像降水一样，它包括从云中带走许多小东西，并在它们落下时将它们组合成更大的东西。我开始喜欢降水的比喻，所以看了看有什么名字。没有任何现存的名为`Sleet`的宝石，所以我选择了它！

## 为什么被创造出来

CI 是一个神奇的工具！但是我总是发现，当我想在本地运行它们时，不得不从 CI 环境中复制并粘贴那些失败的规范是不方便的。当我听说 Rspec 持久性文件时，我知道应该有一种方法将它们结合在一起！从 CI 上传 Rspec 持久性文件几乎可以让您一路到达那里，如果您在单线程环境中运行 CI，那么您最终得到的是一个持久性文件，这可能就足够了！在我看来，CircleCI 最好的特性之一是其并行规范的能力。但是这有一个不幸的副作用，就是创建多个持久性文件，而且我经常发现我想要的失败跨越多个文件。

我也喜欢自动化，这似乎是一个可以解决的问题！幸运的是，CircleCI 有一个 API，可以提供所有的构建和工件信息，所以我只需将所有的东西粘在一起。

## 入门

这假设您已经将 Rspec 持久性文件设置为在 CircleCI 中上传，并设置为在本地使用。关于这方面的指南，请查看[自述文件](https://github.com/coreyja/sleet#getting-started)！

先装宝石。
(Sleet 目前依赖`rugged`进行 Git 交互，这需要`cmake`来安装。在 OSX 你可以用`brew install cmake` )
安装

```
gem install sleet 
```

然后简单地 cd 到您的项目目录，并检查一个已经在 CircleCI 中运行测试的分支。那就干脆跑雨夹雪！

```
sleet 
```

如果您的 Rspec 持久性文件在本地和循环中都被命名为`.rspec_example_statuses`，这应该就是您所需要的！如果你在 CircleCI 中使用工作流，也有对它的支持！查看[工作流程选项](https://github.com/coreyja/sleet#workflows)。

我很快肯定会写更多关于 Sleet 的东西，但是鉴于我今天写了大部分 README 和这篇文章，我今天已经写完了。我希望你给雨夹雪一个眼神！如果你有任何问题或意见，可以在 GitHub 上发表，或者发邮件到 coreyja@gmail.com 给我。