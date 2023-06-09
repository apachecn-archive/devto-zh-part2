# 一个聊天机器人！

> 原文：<https://dev.to/lloople/a-chatbot-for-ohdear-2kan>

有一天，我试着给一个同事看我的帖子，然后我意识到我的网站关闭了。如果那时候发生了，我没有任何警告，这有点尴尬。解决它就像登录 DigitalOcean 并重启 droplet 一样简单，但它看起来非常不专业。

> 我的网站关闭了多长时间？有多少人看不懂我的精彩帖子？

那时我决定，我需要某种自动监控我的网站运行时间。

我记得弗里克发了一条关于 T2 的微博，哦，天哪！他和[马蒂亚斯](https://twitter.com/mattiasgeniar)合作的新监听工具，我决定去看看。

文档看起来很棒，所以我加入了 10 天的免费试用，以检查它是否对我有用。结果证明这是我需要的完美解决方案。看了一下文档并学习如何使用它(确实很简单),我看到了 API 部分。它最终成为一个结构良好且简单的 API。在过去的 3 年里，我一直致力于构建 API 并使用它们，现在我想做一些新的东西。然后我想到了这个项目。

> 一个聊天机器人与这个 API 交互。检查您的网站状态，并在出现问题时接收消息。

到目前为止，我总是创建我的机器人来与 Telegram 交互，我知道它是如何工作的，并且很容易测试结果。这个项目可以在未来与 Facebook Messenger 一起工作，我不会放弃这个功能🤔

一旦我安装了一个新版本的[机器人](https://botman.io)并准备好应用程序，我意识到我从未测试过与不属于我的 API 交互的代码。在那个 API 开发者时期之后，我学习了 TDD 和单元测试。BotMan 提供了一个很棒的[测试解决方案](https://botman.io/2.0/testing)。事实上，开发带有测试的聊天机器人更容易，因为你可以立即检查结果。

然后我想起了在[亚当·瓦森](https://twitter.com/adamwathan)的课程[试驾拉腊维尔](https://course.testdrivenlaravel.com)关于这件事的几堂课。您创建一个类，它将返回与您想要集成的 API 相同的有效负载。通常你的`Fake`类会扩展当前的工作类来覆盖那些执行实际 API 调用的方法。这就是我为这个测试套件所做的[。这样我就可以不用查询 OhDear 的应用程序就能开发整个项目。](https://github.com/Lloople/bot-mr-dear/tree/master/tests/Fakes)

OhDear 提供了一个 [PHP 开源包](https://github.com/ohdearapp/ohdear-php-sdk)来与他们的 API 交互。我需要扩展一些类来适应我的需要，但是剩下的就很简单了。

他们也有一个与 [OhDear WebHooks](https://github.com/ohdearapp/laravel-ohdear-webhooks) 交互的包，但是这次我无法使用这个包。它是为使用保存在配置文件中的密钥而构建的。另外，我的应用程序可以同时与多个用户交互，每个用户都有不同的密钥。我正在把这些信息加密储存在数据库里。当收到 webhook 请求时，它已经包含了用户名。比如`https://deployedbot.com/freekmurze`。然后，我用用户的秘密密钥检查有效载荷，以便继续或返回一个授权错误。

我用[单动作控制器](https://laravel.com/docs/5.7/controllers#single-action-controllers)的方法构建了这个项目，我对结果非常满意，我肯定会在未来的项目中考虑这个问题。

在这个项目中，大多数时候每个名称空间只有一个控制器。但是在 routes 文件中好处也是显而易见的，因为它看起来很简洁，您可以使用 IDE 单击控制器名称并直接进入。

当您在想要测试的聊天机器人上工作时，您有两种选择:

1.  在控制器和测试中写消息两次，如果有修改，记得更新两次。
2.  将消息存储在控制器和测试中可以调用的地方。

我决定使用第二种方法，并决定将消息存储在 Laravel 翻译文件中。这个项目不是多语言的(目前😉)但我认为，有了这些信息，就很容易:

*   重构消息。
*   读代码，比看一整句话还容易`ohdear.token.stored`。
*   测试消息是否发送给用户。
*   在另一个对话中重复使用该消息。
*   将来翻译整个应用程序，因为你在一个地方有所有的文本。

这个机器人目前正在运行，你可以在这里或者通过在 Telegram 上搜索`Mr. Dear`与它对话。因为它是开源的，所以你也可以把它部署到你自己的服务器上。你可以在项目的 README.md 中找到关于如何做的说明。