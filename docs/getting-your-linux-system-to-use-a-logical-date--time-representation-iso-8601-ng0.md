# 让您的 Linux 系统使用逻辑日期和时间表示(ISO 8601)

> 原文：<https://dev.to/mayeu/getting-your-linux-system-to-use-a-logical-date--time-representation-iso-8601-ng0>

在我的许多 Linux 重新配置中的一次，我想知道是否有一种方法可以让它按照 T2 的 ISO 8601 标准显示日期和时间。如果你不知道，那就是将日期格式定义为`2018-08-07`的标准(以及其他东西，比如 24 小时格式等等。).

在 Linux 中配置这些东西的方式是通过 [locale](https://en.wikipedia.org/wiki/Locale_(computer_software)) 系统。大多数时候，我们只是配置`LANG`变量来匹配我们自己的语言(比如`LANG=en_US.UTF-8`)并在那里停止配置。但是还有更多的变数(比如`LC_NUMERIC`、`LC_MONETARY`、`LC_PAPER`等等)，今天让我们感兴趣的是`LC_TIME`。

原来有一个遵循 ISO 8601 标准的`en_DK`语言环境(实际上是 haked 语言环境，因为英语不是丹麦的语言)!

因此，您可以使用`LC_TIME=en_DK.UTF-8`设置您的时间区域，并到达日期时间天堂🙏

*本文最初发表于 [mayeu.me](https://mayeu.me/blog/getting-linux-to-use-a-logical-date-time-representation-iso-8601/) 。*