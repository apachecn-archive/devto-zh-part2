# 做好代码审查的一些建议

> 原文：<https://dev.to/mindsers/some-advice-to-do-a-great-code-review-238i>

我使用 Git 和 Bitbucket、Gitlab、Github 等工具...我知道我的团队努力工作，遵循 Gitflow 开发工作流程。然而，仅仅阻止错误和其他倒退进入我们美丽的代码库是不够的。我们和我的团队讨论了这个问题，我们发现我们应该改进拉请求中的代码审查会话。

通常，失败的不是工具、工作流程或设计模式，而是我们作为一个与他人互动的人。这就是这篇文章的主题:我们如何改进我们自己的代码审查方式？我们如何更好地合作？

## 你的开发团队成员不应该查看样式指南

在很多项目中，大多数评论都是样式向导检查，比如“你应该使用空格而不是制表符”，“你在这里忘记了一个空格”，“在这个项目中，对象键必须是有序的”。这是无用的评论。

我同意在将代码合并到稳定的代码库之前应该检查 styleguide，但是我不明白为什么这么多团队手工做这件事。开发人员的大脑是一个非常强大的工具，为什么我们要用它来做基本的、重复的任务呢？有许多应用程序可以在您的服务器上推送一个 commit 后就自动为您完成这项工作。那么你的开发人员就可以把大部分时间花在编写机器无法完成的代码上。

使用特定应用程序来完成这些任务的另一个原因更多的是关于人际关系。当我发出一个 pull 请求，得到 25 条评论告诉我，我在一些行的末尾添加了一个尾随空格，这让我很生气。

> 为什么其他开发人员不就我代码的实际问题给我建议呢？这个尾随空格是我在代码中唯一要做的改进吗？也许他们只是讨厌我。

我知道这不是一个好的反应，但我不能采取不同的行动！为什么？因为留下这些评论的都是人。人类不是中立的，他们的反应是可以解释的。机器不是这样的。应用程序只做它被创建时要做的事情。没有感情。不做评判。

在我看来，开发人员应该关注最有价值的问题，自动化工具应该检查 styleguide 规则。开发人员的讨论和来自工具的 styleguide 警告应该会阻止合并。

## 检查每个拉取请求的单元测试

我认为评审人员应该在每次创建拉请求时检查测试是否通过。他们还应该检查作者是否为他的特性创建了新的测试。

测试的不好的一面是它们会花费大量的时间来执行，并且增加了评审的时间。审查必须拉动分支，他必须安装依赖项，他必须执行测试。当然，每当作者在分支上推出新代码时，他都必须这样做。

由于浪费时间，大多数开发人员在评审期间不这样做:错误就悄悄进来了。

为了更好地进行评审，应该运行测试，并且在不打扰开发人员的情况下，更好的方法是建立一个测试自动运行的 CI 环境。结果必须直接在 pull 请求中报告，以通知每个人不管代码有多好，测试都不会成功。

在我看来，如果单元测试(或者其他类型的测试)失败，CI 应该阻止合并。

## 为人和善

这份工作的另一个方面是人际关系可能变得更糟而不是更好:沟通。拉取请求完全是关于沟通的。代码评审是关于交流的。

一个开发人员要求其他开发人员进行评审，在合并之前对他的工作进行分析。在这一步之后，它将是团队的代码，但在合并之前，它仍然是开发人员的代码，他可能会对此感到敏感。

此外，还会撰写审核意见，删除除语言之外的所有其他交流形式。作者在读评论的时候听不到我们的声音，听不到我们的语气。作者看不到我们的手势或面部表情。沟通的大部分隐藏在屏幕后面。

因此，可能会出现误解。一些开发者会认为评论是侵略性的。其他开发者可能会认为同样的评论是正常的，一点也不咄咄逼人。有时你会认为一个评论是咄咄逼人的，仅仅是因为你今天过得不好。

我避免这种情况的诀窍？要善良。尽你所能让作者感受到你的善意:

*   将您和其他开发人员包括在评审中，以便更加中立和包容。使用代词，如*我们的*、*我们的*、*我们的*。
*   不要使用命令式风格。用*应该*，*可能*，*可能*，而不是*必须*，*必须*...
*   说明这是你的观点，而不是事实的最终来源。
*   如果作者必须遵循你的建议，你必须解释为什么。当其他人能够理解其中的原因时，一切都会变得更好。

## 诀窍:你的拉取请求应该尽可能小

作者可以根据他的拉取请求影响代码评审的方式。他怎么会？Git 用法。

想要收到有用的评审意见或者想要这些同事快速评审他的 pull 请求的开发人员，应该遵循这个小小的规则:一个特性=一个 pull 请求。

评审人员也是有任务要做的开发人员。查看拉取请求需要时间。作为一名审核者，当我看到一个包含许多功能的大拉请求时，我不想现在就审核，而是以后再审核，因为我知道我会在这个拉请求上花很多时间。

大的拉取请求的另一个副作用是它们经常被审查者催促。试着做两个拉请求，一大一小。在大多数情况下，小的会比大的得到更多的评论。为什么？因为开发人员很懒，这一点在他们审查拉取请求时不会改变！