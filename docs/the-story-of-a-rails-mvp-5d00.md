# Rails MVP 的故事

> 原文：<https://dev.to/stevetwitte/the-story-of-a-rails-mvp-5d00>

[![alt text](img/9eaf5f51d7b570a2864f126977c93305.png "The Story of a Rails MVP")](https://res.cloudinary.com/practicaldev/image/fetch/s--exBw3D8D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i4vj5bjpdp4iuopwkift.jpg) 
我最近有一次很棒的经历，我抛弃了 API/客户端应用程序的麻烦，转而使用标准的 Rails web 应用程序。这是一个与 MyInvestmentTime.com 一起寻找阻力最小的道路的故事。

不久前的一个周五，我坐在办公桌前，完成了冲刺，看着一整天的投资时间，但不知道该做什么。我突然想到，可能有一个网站或论坛可以让我获得一些灵感。我以为已经有一个网站让人们谈论投资时间了。事实证明，这个网站并不存在，至少据我所知，所以我决定创建 MyInvestmentTime.com。

## 想法

这个想法是给用户一个类似 Twitter 的 feed 来检查社区在他们投资的时间里在做什么。我很快想到了我的团队，并希望添加一种方法来跟踪团队并更新任务或项目的状态。我已经尽量小心不去创建典型项目管理工具的替代品。我不想取代 GitHub 或 Trello。因此，相反，把 MyInvestmentTime.com 看作是你投资时间的象征，是在团队和更广泛的社区中分享投资时间的一种方式。

## 路径

我最初是一名 Ruby on Rails 开发人员，在我的公司，我们通常使用 Rails API 后端和 Angular 前端。这似乎也是我的应用程序的逻辑起点，但我已经知道 Angular，因为这是一个有趣的附带项目，我决定用一些新的东西。

在这个时候，Create React 应用程序刚刚发布。这似乎是学习 React 的最佳时机，所以我决定使用 Rails API 后端和 React 前端。我为此工作了几个月，周末和一个朋友一起，他也在学习 React，有一个非常基本的工作应用程序。

它允许你创建一个投资，并允许你查看其他人的投资。你可以登录、注册等等，但除此之外别无其他。此外，正如许多项目一样，当我的工作有起色时，这个项目就半途而废了。所以 Heroku 应用程序坐在那里，repos 没有得到任何提交，我的 URL 仍然指向任何东西。

## 更快捷的路径

一个月前，我坐在我的厨房里，查看我的 RSS 订阅，我有了一点空闲时间，并认为检查一下久违的应用程序并完成一些工作可能会很好。我对交付代码和从 idea 到 MVP 的工作的个人观点发生了很大的变化，在看到代码和它所做的事情后，我意识到很多事情都在进行，但收效甚微。

似乎没有任何理由这个应用程序不应该只在服务器端呈现，至少在大多数时候是这样。Rails 5.1 是在我开始使用时发布的，这为添加 React 组件提供了可能性，如果他们需要实时功能，或者在这一点上，我会选择 Vue.js，但为如此简单的事情设置客户端和 API 似乎很愚蠢。

我决定从一个基础的 Rails 应用程序开始，并使用所有的工具让开发变得更加舒适和快速。事实证明，用一些宝石，我可以在四天内完成第一个完整的 MVP，每天大约 5 个小时。

## 结构

我从 5.2 版本的“rails new”开始，并开始创建。该应用程序的结构很简单，如果你想查看，在文章底部有一个回购的链接。以下是一些亮点。

我使用通关([https://github.com/thoughtbot/clearance](https://github.com/thoughtbot/clearance))进行认证，这给了我一个真正的开端。gem 不像 Devise 那样著名或突出，但我更喜欢它的定制，它仍然能给你开箱所需的一切。我用它的生成器给了我一个用户模型和我所有的基本视图。然后我对他们的控制器进行了子类化，添加了一个用户名来注册并定义了我的路线。唯一剩下的就是造型了。

康康康舞([https://github.com/CanCanCommunity/cancancan](https://github.com/CanCanCommunity/cancancan))是另一个很好的补充，它帮助我清理了控制器，并大大简化了它们。它是一个授权和资源加载库，基于能力的概念工作。您为您的用户定义了一组能力，按型号、类型、角色进行分隔。然后在您的控制器中，您可以在动作之前自动授权和加载该资源。我的解释是对 gem 所能做的事情的极端简化，我鼓励你阅读许多优秀教程中的一个或者仅仅是康康康的 wiki，它也是非常有益的。

我不是设计师，虽然这个网站是功能性的，但我不认为它会赢得任何奖项。TailwindCSS[(](https://github.com/tailwindcss/tailwindcss)[https://github.com/tailwindcss/tailwindcss](https://github.com/tailwindcss/tailwindcss))确实给了我在浏览器中设计的能力，比我通常习惯的更灵活和更快。它可以让你专注于你的网站的结构，而不用考虑它应该是什么样子。我喜欢它，它提供了将 HTML 元素提取到类中所需的工具。它使用 PostCSS 和 Javascript，所以我在 Rails 中使用 web pack 集成了它。

总的来说，这些工具和 Rails 框架让我能够交付一个非常干净的代码库，拥有最少的依赖项和非常完美的体验。用 RSpec 测试从单元到视图的一切都轻而易举。模型验证自动延续到表单等等。在 API/SPA 世界中，我不得不从头开始构建或者下载一个库。

## 结论

很多时候这个项目设置是没有意义的。我们采用 API/客户机设计的一个重要原因是，有多个客户机都需要访问相同的数据。我可以看到许多应用程序需要更多的实时功能，或者客户需要直接与许多微服务对话的情况。

还有许多其他理由不使用 monolith Rails 应用程序，但没有一个适用于我的项目，我敢打赌其中很多也不适用于你的项目。除了 HTML 之外，我只需编写很少的代码，这给我留下了极其深刻的印象。它归结为配置和设置比什么都重要。

所以下次当你开始一个新项目时，不要试图去接触新的闪亮的东西，看看一些可靠的选择。您可能会对多年的开发和支持对框架或库的影响印象深刻。

[https://myinvestmenttime.com](https://myinvestmenttime.com)

[https://github.com/stevetwitte/my-investment-time](https://github.com/stevetwitte/my-investment-time)