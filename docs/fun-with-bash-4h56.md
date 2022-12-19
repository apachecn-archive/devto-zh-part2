# Bash 的乐趣

> 原文：<https://dev.to/quii/fun-with-bash-4h56>

[这是我不久前在博客上发布的](http://www.quii.co.uk/Fun_with_Bash)

我在工作中被赋予了一项相当平凡的任务，当这种情况发生时，我会努力想办法让它变得有趣。我通常会找到一种方法来学习新的东西来完成任务，这样我总能从我的工作中获得一些东西，即使它很枯燥。

我很想在命令行上更好地使用我可以使用的传奇工具。在那里做文本处理，我从来没有感到*超级*自信，经常放弃，只是去找我的编辑器。

我不会详尽地记录我所做的每一个命令，如果你对细节感兴趣，可以像我一样咨询`man $command`！这篇文章将展示如何通过做一点研究，你可以快速地做一些 rad 文本处理。

## 任务

我正在尝试分析我们提供了多少内存，与我们一些服务的实际使用情况相比，看看我们的 AWS 账单是否可以减少一点。

## 数据

我以为会很好玩(？？)来使用 CSV，因为感觉像 CSV 一样的数据。这是我采集的数据样本。

```
name, usage (mb), allocated (mb), CPU %, containers
dispatcher, 150, 512, 40, 10
assembler, 175, 512, 75, 10
matcher, 85, 512, 15, 10
user-profile, 128, 512, 40, 5
profile-search, 220, 512, 80, 10
reporter, 90, 512, 40, 10 
mailgun-listener, 90, 512, 10, 5
unsubscribe, 64, 512, 3, 5
bounce, 8, 128, 0.5, 3
legacy-reporting, 30, 512, 15, 3
content-store, 80, 256, 30, 10
legacy-alert-poller, 64, 256, 1, 1
migrator, 80, 256, 10, 5
entitlements-update, 150, 256, 70, 3

```

## 显示它漂亮

这又好又简单`column -s, -t < data.csv`

`-t`确定输入包含的列数，并与`-s`结合指定一组字符作为分隔符。如果你没有指定一个`-s`，它默认使用空格。

```
name                  usage (mb)   allocated (mb)   CPU %   containers
dispatcher            150          512              40      10
assembler             175          512              75      10
matcher               85           512              15      10
user-profile          128          512              40      5
profile-search        220          512              80      10
reporter              90           512              40      10 
mailgun-listener      90           512              10      5
unsubscribe           64           512              3       5
bounce                8            128              0.5     3
legacy-reporting      30           512              15      3
content-store         80           256              30      10
legacy-alert-poller   64           256              1       1
migrator              80           256              10      5
entitlements-update   150          256              70      3

```

## 按用法排序

`cat data.csv | sort -n --field-separator=',' --key=2 | column -s, -t`

```
name                  usage (mb)   allocated (mb)   CPU %   containers
bounce                8            128              0.5     3
legacy-reporting      30           512              15      3
legacy-alert-poller   64           256              1       1
unsubscribe           64           512              3       5
content-store         80           256              30      10
migrator              80           256              10      5
matcher               85           512              15      10
mailgun-listener      90           512              10      5
reporter              90           512              40      10 
user-profile          128          512              40      5
dispatcher            150          512              40      10
entitlements-update   150          256              70      3
assembler             175          512              75      10
profile-search        220          512              80      10

```

`--key=2`表示按第二列排序

## 用 awk 算出内存差异

我们真正感兴趣的是调配的内存量与使用量之间的差异。

`awk -F , '{print $1, $3-$2}' data.csv`

让我们再把它输入到列中

`awk -F , '{print $1, $3-$2}' data.csv | column -t`

```
name                 0
dispatcher           362
assembler            337
matcher              427
user-profile         384
profile-search       292
reporter             422
mailgun-listener     422
unsubscribe          448
bounce               120
legacy-reporting     482
content-store        176
legacy-alert-poller  192
migrator             176
entitlements-update  106

```

这很好，但最好忽略第一行。

`awk -F , '{print $1, $3-$2}' data.csv | tail -n +2`

`tail -n X`打印最后的`X`行，加号将其反转，因此它是第一个`X`行。

## 排序 mk 2

现在我们有了一些内存差异，对它们进行排序会很方便，这样我们可以首先解决最低效的配置

`awk -F , '{print $1, $3-$2}' data.csv | tail -n +2 | sort -r -n --key=2`

当然，再次使用列来使它看起来更漂亮

`awk -F , '{print $1, $3-$2}' data.csv | tail -n +2 | sort -r -n --key=2 | column -t`

```
legacy-reporting     482
unsubscribe          448
matcher              427
reporter             422
mailgun-listener     422
user-profile         384
dispatcher           362
assembler            337
profile-search       292
legacy-alert-poller  192
migrator             176
content-store        176
bounce               120
entitlements-update  106

```

## WTF

在那里！有人在 6 个月后读到完全无法理解的 bash 命令，不禁挠头。事实上，自从我写了第一份草稿已经有两个星期了，我看着最后的命令流泪了。

当您看到一个没有意义的 shell 脚本时，您很容易就放弃了，但是您可以做一些事情。

请记住，这个过程通常会像我们这里这样从小处开始，从一个命令开始，通过管道传递到另一个命令，再传递到另一个命令。这让像我这样懒惰的开发人员感觉这是一个复杂的命令，但它实际上只是处理一些数据的一组步骤。因此，如果你在挣扎，你可以通过删除一些步骤来为自己回溯一些步骤，看看会发生什么。

如果它是一个需要长期理解的重要业务流程，那么你最好用一种可以围绕它编写一些自动化测试的语言来编写它。

但是我们做的很多工作确实涉及到做一些特别的事情，而不是驻留在“代码库中”，在那里你可以很容易地进入 TDD 节奏来完成一些闪亮的东西。通常你不得不做一些有点无聊的事情，有时你电脑上可用的工具真的可以帮你。它们是如此古老和完善，你可以找到大量的文档和技巧，所以开始吧！

如果您需要更多了解 shell 的理由，请阅读[命令行工具如何比您的 hadoop 集群快 235 倍](https://aadrake.com/command-line-tools-can-be-235x-faster-than-your-hadoop-cluster.html)