# 又一次网站重建

> 原文：<https://dev.to/jackmarchant/yet-another-website-rebuild-1e43>

我一直习惯于用不同的语言、不同的技术重建我自己的个人网站[jackmarchant.com](https://www.jackmarchant.com)，而不是拘泥于一个应用程序的想法本身，但在大多数情况下，结果是一样的。

有时重新构建同样的东西会有点无聊，但它让我专注于我试图学习或变得更熟悉的技术。这是一个非常简单的网站，我从来没有偏离博客太远，也许这里或那里有一些额外的内容或花哨的前端功能。

我用以下材料建造了[jackmarchant.com](https://www.jackmarchant.com):

*   通过 Cloudfront 提供单个 HTML 文件的 S3 桶
*   [Silverstripe CMS 和框架](https://github.com/jackmarchant/jackmarchant_com)
*   [Ruby on Rails](https://github.com/jackmarchant/website-rails)
*   [节点(快递)](https://github.com/jackmarchant/website)
*   流星和反应
*   [前端带有 React 和 Relay 的 Elixir graph QL API](https://github.com/jackmarchant/jackmarchant-react-relay)
*   最近，一个 [Phoenix 应用程序，仙丹的 web 框架](https://github.com/jackmarchant/jackmarchant)。

我不会逐一介绍每一个版本，但回顾所有不同的版本，我有机会找出作为这些项目的维护者和用户，我喜欢什么和不喜欢什么，所以在周末，我决定全部使用 [Phoenix](http://phoenixframework.org) (网站上甚至还没有加载 JavaScript)。

这次我把几个目标放在一起，这对我来说是非常罕见的，因为通常我会很乐意很快地把一些东西放在一起，并把它放在那里。

## 目标:

*   鼓励自己多写点东西
*   让发布变得简单快捷
*   支持 markdown，使交叉发布更容易。
*   实现我的文章和读者之间的进一步互动(使用类似订阅表单的东西——剧透:这是一个订阅表单)
*   首先为移动设备构建。我在手机上做了很多阅读，所以我很欣赏其他能让这种体验变得很棒的网站。

根据我设定的目标，我将向您详细介绍我是如何开发这款应用的:

## 托管

这些网站中的大多数都部署在 Heroku 上，这有利于快速部署小型应用程序(而不是更多)。

幸运的是，我已经做了很多重新构建它的练习，所以很容易使用 Elixir 和 Phoenix 构建包重新调整现有 Heroku 应用程序的用途，复制一些配置并进行一些更新，以便 Phoenix 可以连接到 Postgres 数据库。

## 语言/框架

我创建了一个新的 Phoenix 应用程序，并保留了许多默认设置，因为我想看看开发人员使用 Phoenix 的体验。事实证明它真的很好，并且鼓励了很多好的实践，比如在表单 post 请求中内置对 CSRF 令牌的支持，组织应用程序域逻辑，与数据库交互等等。

Phoenix 文档谈了很多关于基于[上下文](https://hexdocs.pm/phoenix/contexts.html)的结构化方面的建议，但最终它仍然足够灵活，你可以做任何你想做的事情。

我创建了一个简单的[任务](https://hexdocs.pm/elixir/Task.html)，它将在应用程序启动时运行一次。这将把我的减价帖子翻译成 HTML，这样它们就可以显示在页面上了。

## 写减价

我想在写作时摆脱自己的方式，并以最简单的方式让我的 markdown 文件显示在页面上。因此，这个名为 [PostReader](https://github.com/jackmarchant/jackmarchant/blob/master/lib/jack_marchant/post_reader.ex) 的[任务](https://hexdocs.pm/elixir/Task.html)在应用程序启动时扫描文件夹中的降价文件，读取它们的内容和元数据，然后通过名为[圈存](https://github.com/pragdave/earmark/blob/master/README.md)的仙丹库将其转换为 HTML。然后我用 HTML 对数据库进行插入(插入或更新),这就是页面上显示的内容。升级允许我对现有的降价文件进行修改，并在网站上更新。

## 前端

这可能是我在前端花费的最少的时间，因为我想基本上有足够的时间让它看起来足够好，但更重要的是文章可读，代码示例显示清楚。只有简单的 CSS，组织得非常简单，也没有 JavaScript。
有了 Phoenix 的代码重载标准，快速更改和更新变得轻而易举。
前端没有什么复杂的，尽管它给了我一个机会在我通常会用到 React 的地方更彻底地使用 [EEx](https://hexdocs.pm/eex/EEx.html) (Elixir 编译的模板)。

## 订阅表单

subscribe 表单接收一个电子邮件地址，并在数据库中创建一个新的订户记录。我将使用 Heroku 的数据剪辑来维护订阅者的记录，随着数量的增加，我可能会升级它，以与第三方电子邮件提供商集成。哦，顺便说一下，输入你的电子邮件地址，点击按钮，我会满足你三个愿望。

## 这次我学到了什么？

*   在开始一个项目之前，我应该确定几个高层次的目标，否则会让人觉得你永远不会真正“完成”。当我不得不试着向别人解释事情时，我学得最好，所以我发现写作是我这样做的一种方式。所以，希望我会知道，最好花更多的时间写作，而不是花更少的时间反复重建我的网站！
*   我学会了欣赏不要把事情复杂化，保持简单，苏丹。
*   我学会了关注网络上最重要的东西，用户体验。
*   我意识到我需要在周末找到另一个爱好。