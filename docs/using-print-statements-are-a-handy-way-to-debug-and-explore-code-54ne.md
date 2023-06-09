# 使用 Print 语句是调试和研究代码的一种便捷方式

> 原文：<https://dev.to/nickjj/using-print-statements-are-a-handy-way-to-debug-and-explore-code-54ne>

**本文最初发布于 2018 年 9 月 9 日:[https://nickjanetakis . com/blog/using-print-statements-are-a-handy-way-to-debug-and-explore-code](https://nickjanetakis.com/blog/using-print-statements-are-a-handy-way-to-debug-and-explore-code)**

* * *

过去我写过关于[橡皮鸭调试](https://nickjanetakis.com/blog/solve-programming-problems-with-rubber-duck-debugging)的文章，这是帮助解决问题的一种方法。橡皮鸭调试很方便，因为它迫使你详细解释问题，让你[分解它](https://nickjanetakis.com/blog/breaking-down-problems-is-the-number-1-software-developer-skill)。

但是现在让我们假设你知道你想要做什么，并且你正在努力实现它。使用 print 语句可以帮助您非常快速地从非工作代码转换到工作代码。

## 为什么要费心使用打印报表？

写软件时，知识就是力量。你对正在发生的事情了解得越多越好。放入打印报表是一种快速查看发生了什么的方法。这甚至不仅限于调试问题。这对一般的代码洞察很有用。

这就是为什么当我写的代码第一次尝试就不成功的时候，我会跳过逞英雄，而开始到处添加打印语句。我越快发现问题所在，就能越快进入下一步。

## 使用打印报表什么时候有用？

以下是我发现自己使用打印报表的三种情况:

*   当你想快速看到某物的价值时
*   尝试在较长的函数中使用分而治之的方法
*   探索新的代码库，并且您需要一些帮助来跟踪代码库

#### 迅速看出某物的价值

这些年来，我学到的最大的教训之一就是不要假设任何事情。如果你对某样东西可能是什么有哪怕一点点怀疑，那么就把它打印出来，以便确认。

只需要几秒钟就可以打印出来并确认。这可以节省你很多很多分钟的猜测时间(这会因为在 Youtube 上分心而导致几个小时)。

#### 各个击破

在之前一篇关于微服务的文章中，我谈到了代码抽象的层次。

当处理一个相当复杂的逻辑时，我倾向于将大部分代码内联到一个函数中，直到我解决了逻辑。我只是觉得这种风格最容易处理。

这个问题已经够难的了，为什么还要在我理解问题或编写解决方案之前，担心命名函数和重构函数，从而使问题复杂化呢？

这通常意味着，我将从很长的函数开始。第一次编写 30 或 40 行的函数并不少见。

有时，您甚至不确定某个条件的某些行或某段代码是否正在执行，但是放入 print 语句是确定正在执行的代码路径的一种非常快速的方法。

它可以简单到把`print("Is this being executed?")`放在某个地方。

#### 探索新的代码库

作为一名自由开发人员，我经常被要求对现有的代码库做一些事情。这可能是添加新功能、修复错误或增加测试。什么都有可能。

这些应用程序有很大的复杂性。其中一些是 100 行 Flask 应用程序，而另一些是 100，000+行 Rails 应用程序，但我发现一个共同的趋势是我依赖打印东西来快速跟踪新的代码库。

一旦你对你所选择的 web 框架足够熟悉，你就有了一个大致的概念，当代码基于它存在的地方运行时，但是，这仍然会根据你正在做的事情而发生很大的变化，特别是如果有很多元编程要展开的话。

##### 分而治之的更好例子:

你不能控制不是你写的代码的质量。

有时候，你最终会被迫弄清楚一个 400 行的函数到底在做什么，当然，编写它的人早就不在了。

在这些地方，我喜欢做的是在尽可能短的时间内，从等式中删除尽可能多的代码，然后将其分块。

例如，如果您在第 200 行放入一个 print 语句，并看到它被输出，那么您肯定知道至少前 199 行正在运行。您可能希望在第 200 行输出某个变量的值。

然后，您可以在第 100 行再次输出该变量，如果该变量的值在第 100 行是正确的，但在第 200 行是不正确的，您可以有把握地得出结论，在第 101 行和第 199 行之间出现了问题。

现在，您可以在第 150 行插入一个 print 语句并检查变量。如果它是正确的，那么您知道问题在第 151 行和第 199 行之间，或者如果它是不正确的，那么您知道问题在第 101 行和第 149 行之间。

这种使用调试的形式被称为二分法搜索，或二分搜索法，这取决于您是从哪里学来的。以二分法搜索的方式使用 print 语句可以让您 100%地忽略代码，从而快速找到问题的根源。

## 快速查找您的打印报表

许多 web 框架在开发过程中都有非常详细的日志记录，这很好。正如我们之前谈到的，在某些情况下，拥有更多信息会更好。

但这也意味着很难找到你的打印报表。以下是我目前正在开发的一个 Dockerized Phoenix 应用程序的一些日志输出:

```
web_1       | [info] GET /
web_1       | [debug] QUERY OK source="users" db=1.7ms
web_1       | SELECT u0."id", u0."admin", u0."confirmed_email_at", u0."deactivated_at", u0."email", u0."email_auth_token", u0."name", u0."profile_hexcolor", u0."professional_title", u0."signed_in_at", u0."profile_photo", u0."username", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [33]
web_1       | [debug] Processing with LMSWeb.PageController.index/2
web_1       |   Parameters: %{}
web_1       |   Pipelines: [:browser]
web_1       | [info] Sent 200 in 3ms 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么太疯狂的，这只是呈现主页，但是假设我们有一个 print 语句被放入其中:

```
web_1       | [info] GET /
web_1       | [debug] QUERY OK source="users" db=1.7ms
web_1       | SELECT u0."id", u0."admin", u0."confirmed_email_at", u0."deactivated_at", u0."email", u0."email_auth_token", u0."name", u0."profile_hexcolor", u0."professional_title", u0."signed_in_at", u0."profile_photo", u0."username", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [33]
web_1       | Hi
web_1       | [debug] Processing with LMSWeb.PageController.index/2
web_1       |   Parameters: %{}
web_1       |   Pipelines: [:browser]
web_1       | [info] Sent 200 in 3ms 
```

Enter fullscreen mode Exit fullscreen mode

快速浏览一下，在其中找到我们的打印消息并不容易，我们在这里只处理几行日志输出。想象一下，如果有 5 倍的行占据了整个终端缓冲区。

与之相比:

```
web_1       | [info] GET /
web_1       | [debug] QUERY OK source="users" db=1.7ms
web_1       | SELECT u0."id", u0."admin", u0."confirmed_email_at", u0."deactivated_at", u0."email", u0."email_auth_token", u0."name", u0."profile_hexcolor", u0."professional_title", u0."signed_in_at", u0."profile_photo", u0."username", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [33]
web_1       | ------------------------------------------------------- Hi
web_1       | [debug] Processing with LMSWeb.PageController.index/2
web_1       |   Parameters: %{}
web_1       |   Pipelines: [:browser]
web_1       | [info] Sent 200 in 3ms 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以很容易地在一秒钟内看到“嗨”的信息。我们的眼睛立刻被它吸引住了。

对于单行输出，我倾向于在消息前面加上一串字符。我不总是用`-`。真的，只要能看到就无所谓。

有时如果我输出几个变量，我会对每个变量使用不同的字符。也许是`---------------`和`~~~~~~~~~~~~~~~`。如果在多行中是同一个变量，我会加入一个`--------------- L150:`或者任何有意义的东西。

如果我打算输出几行文本，我有时会选通它们，比如:

```
web_1       | [info] GET /
web_1       | [debug] QUERY OK source="users" db=1.7ms
web_1       | SELECT u0."id", u0."admin", u0."confirmed_email_at", u0."deactivated_at", u0."email", u0."email_auth_token", u0."name", u0."profile_hexcolor", u0."professional_title", u0."signed_in_at", u0."profile_photo", u0."username", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [33]
web_1       | -------------------------------------------------------
web_1       | Name: Foo
web_1       | Email: foo@bar.com
web_1       | -------------------------------------------------------
web_1       | [debug] Processing with LMSWeb.PageController.index/2
web_1       |   Parameters: %{}
web_1       |   Pipelines: [:browser]
web_1       | [info] Sent 200 in 3ms 
```

Enter fullscreen mode Exit fullscreen mode

这就是我如何处理基于打印语句的调试。在我的编程工具箱中，这是一个很常用的工具。

**用打印语句调试有什么小技巧？下面让我知道！**