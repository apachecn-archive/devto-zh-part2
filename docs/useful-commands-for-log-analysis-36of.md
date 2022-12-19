# 用于日志分析的有用命令

> 原文：<https://dev.to/chapindb/useful-commands-for-log-analysis-36of>

在过去的几个月里，我在 Linux 环境中执行了越来越多的分析，并有机会改进我的定位命令，获得了一些新的(对我来说)技巧。在这篇文章中，我将分享我喜欢使用的一些技术，并鼓励您分享您在 Linux 系统上执行分析时使用的其他技巧/诀窍，无论是作为您的分析环境还是作为您的目标证据(或者两者都是)！

<figure>[![](img/32343525caeed1115bf2439a75580744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BF5vRn4e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A2yo2yxGMeNb8JZKW) 

<figcaption>照片由[孙浩](https://unsplash.com/@danielleone?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

这是在入门级别编写的，旨在帮助那些以前没有在 bash/sh/zsh(或其他)命令行环境中执行分析的人。

***警告:本帖不含 Python***

剧透——这个周末我在这篇文章上没有得到我想要的，所以我将保持这篇文章的简短，并很快发表另一篇文章，提供更多的提示&技巧。

### 为那些较新来的命令行

如果您是 bash 命令行的新手，请使用手册页获取文档。需要一点时间来理解它们是如何写的，以及你要寻找的细节在哪里，所以最好早点开始使用它们。下面是一个使用 man 命令了解有关 ls 的更多信息的示例: