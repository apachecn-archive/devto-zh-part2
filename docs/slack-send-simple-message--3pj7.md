# 懈怠。发送简单消息。

> 原文：<https://dev.to/phpprofi/slack-send-simple-message--3pj7>

茹:[http://phpprofi.ru/blogs/post/96](http://phpprofi.ru/blogs/post/96)

* * *

最近我为 Slack 写了一篇关于[流行 PHP 包轮回的帖子，里面说到了流行包的 fork 以及恢复对这个包的支持。但是没有关于它的用法。所以，今天我想讲讲如何用 PHP 快速实现向 Slack 发送消息。](https://dev.to/phpprofi/reincarnation-of-popular-php-package-for-slack-2p3i)

# 用 PHP 向 Slack 发送消息

首先，让我们看看一般的实现。我们需要做 3 件简单的小事来从 PHP 发送消息:

*   [在 Slack 管理区创建传入的 web hook](https://my.slack.com/services/new/incoming-webhook),&复制 url
*   通过 Composer 安装[包](https://packagist.org/packages/alek13/slack)
*   创建和发送消息的一些简单代码:
    *   通过传递挂钩 url 创建一个客户端(在 1 个步骤中复制)
    *   发送消息

其实就这些了。这些信息足以实现所构想的内容，无需进一步阅读，但让我们仔细研究每一点，并更详细地揭示它。

## 创建传入的 webhook

消息最初发送到 Slack 服务器。然后这些服务器将消息传递给最终用户。对于 Slack 服务器(以及最终用户)，您发送给这些服务器的消息是一个*传入的*。因此，它被称为*传入*，即使它在发送的那一刻对你来说是传出的。这里最重要的是不要混淆。从最终用户的角度来看，一切都井井有条。

要创建一个传入的网页挂钩，请前往[https://my.slack.com/services/new/incoming-webhook](https://my.slack.com/services/new/incoming-webhook)。您将被重定向到您的工作区-最后一个，您访问的管理面板。如果您没有访问该功能的权限，请让您的 slack 管理员创建一个传入的 webhook，并向您发送一个`url`到它。

如果您有多个工作区，请使用右上角的菜单转到所需的工作区:

[![](img/0de3eeb08ef6df93241a8cdc8cf6ef7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AeWBpL0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/fb7486dd-706c-493d-892e-d6bdb7c3f484.jpeg)

接下来，您将被要求选择消息将被发送到的渠道:

[![](img/25109b9b216a88c37a87254e02ac08b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wJVXu5rA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/7056f524-c970-4416-b7f5-5c84c823e0c2.jpeg)

如果您认为对于每个通道(您想要发送的通道)您需要创建一个钩子，那么这个想法是合理的，但不是必需的。从代码发送消息时，您可以指定消息应该传递到的通道。或者不指定通道，消息将被发送到您在此步骤中选择的默认通道。

之后，会创建一个新的 webhook。你将被转到一个带有设置的页面，页面上的信息包括如何使用它，如何生成消息，以及一个通过 curl 命令发送消息的简单例子。

[![](img/31075735720c64c4d59b07d930339b35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QrleEMGW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/c93a51b3-401c-4a7d-b96e-ae072fa841a1.jpeg)

在这里，您可以立即复制`Webhook URL`，关闭管理面板并继续安装软件包和代码。

如果你想增加一点美感，那么折叠这些说明或者向下滚动到挂钩设置:

[![](img/2256888333c30b775c10a7e12e30f599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V006epsn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/98ef6c6d-af88-431e-8b30-f426f7be2950.jpeg)

在这里，您可以更改:

*   默认频道，
*   重新生成`url`(比如说如果被妥协了，就像这个)，
*   设置“用户名”——更准确地说，是发送消息的应用程序的名称，
*   选择图标或徽标，
*   ...看看消息最终会是什么样子。

很多单词，但是两分钟就完成了。松弛管理区不再需要，我们可以关闭它。不要忘记**复制`hook_url`** 。

## 安装软件包

你可以通过我们最喜欢的[作曲家](http://getcomposer.org/) :
安装[软件包](https://packagist.org/packages/alek13/slack)

```
composer require alek13/slack 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/c0d798d7f716781ea9bd9869e09a635d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GjbW0jz---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/436657fd-5dfa-438e-ae89-52caf2d31b79.jpeg)

仅此而已！软件包已经安装好，可以使用了。

## 用 PHP 发送消息

首先，我们需要一个客户端实例。为了最小限度地创建它，需要的是传入 webhook 的`url`,它是我们在创建时复制的。

```
// get from your config
$hookUrl = 'https://hooks.slack.com/services/Txxxxxxxx/Bxxxxxxxx/xxxxxxxxxxxxxxxxxxxxxxxx';

$client = new Maknz\Slack\Client($hookUrl); 
```

Enter fullscreen mode Exit fullscreen mode

或者您可以更改默认频道、用户名、图标和其他一些设置:

```
// get from your config
$hookUrl  = 'https://hooks.slack.com/services/Txxxxxxxx/Bxxxxxxxx/xxxxxxxxxxxxxxxxxxxxxxxx';
$settings = [
    'username' => 'Php Profi Bot',
    'channel' => '#general',
];

$client = new Maknz\Slack\Client($hookUrl, $settings); 
```

Enter fullscreen mode Exit fullscreen mode

更多关于设置的信息，你可以[看这里](https://github.com/alek13/slack#settings)。

现在只需发送一条消息:

```
$client->send('Hello world!'); 
```

Enter fullscreen mode Exit fullscreen mode

**就这些！**

这里可以看到更多关于发送[的内容。](https://github.com/alek13/slack#sending-messages)

希望这篇文章能简化你的应用程序与 Slack 的集成。如果还有不清楚的地方或者你有问题，请在我们的[空闲频道](https://join.slack.com/t/php-slack/shared_invite/enQtMjk1OTExNDkzMjg1LTk5ODg3MGE1OThlYzZlM2U0N2I5Y2FhM2NiYmFlMjE1MDNiOWRjMjc1ZDIwNWZlNzBkZWQ2ZWM1NzdmMjM5YzQ)中写下。