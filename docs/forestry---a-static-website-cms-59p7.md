# 林业-静态网站 CMS

> 原文：<https://dev.to/fbnlsr/forestry---a-static-website-cms-59p7>

静态网站越来越受欢迎。虽然有些人可能会贬低这种“追根溯源”的方法，但这些系统有许多优点。它们通常更快，因为它们仅限于服务简单的网页，服务器上几乎没有任何工作负载。逻辑由客户端处理，主要是通过使用 API，将资源放在缓存中并在部署期间使用 CDN 要容易得多。此外，这些被认为是原子的，因为它们允许即时缓存失效。因此，网站的可送达性是 100%有保证的。静态网站通常也更安全，因为它们不允许信息直接发送到服务器。

也就是说，静态网站的更新和部署必须在配备了合适开发环境的机器上进行。一个终端，以及 Git、Node、NPM、Ruby 等工具往往是必不可少的。事实上，网站必须事先编译，或者通过 GitHub Pages 或 Netlify 这样的自动化过程编译。

## 上需要一个静态 CMS

因此，出于多种原因，对静态 CMS 的需求是有意义的。首先，它允许克服对开发环境的需求。就博客而言，文章的创作变得更加流畅，能够摆脱这种技术环境可以为内容创作过程提供更好的工作流程。其次，它允许非技术用户管理他们的网站，让他们更自由地创建和管理编辑内容。

今天我尝试了 Forestry，这是一个托管服务，它利用 GitHub API 来远程管理静态网站。

[![Forestry logo](img/97feab3977f907da3584f3cfd787400b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2gLR6UTX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffuqgypztxq9my0ch19b.jpg)

## 安装

CMS 的安装非常简单:我只需要创建一个关于林业的帐户，将它与我的 GitHub 帐户相链接，并选择相关网站的存储库。然后，我必须指定网站是由 Hugo(这是我的例子)还是 Jekyll 生成的。快速提示:我必须说明我正在使用的 Hugo 的版本，Forestry 给出的版本不一定与你的一致。这对我来说不是问题，因为我已经将 Forestry 配置为不生成我的网站，但这是需要考虑的事情。

## 配置

林业的配置也一样简单。它的系统将浏览您的存储库，并根据找到的内容生成内容类型。总的来说，我只花了五分钟就有了一个可以工作的后台，而且我马上就可以高效工作了。

甚至有可能在你自己的网站中注入内容管理系统，并通过你指定的网址(例如[www.example.com/admin](http://www.example.com/admin))，这样你就不会离开自己的网站。

## 用法

林业通过使用 GitHub、Gitlab 或 Bitbucket 的 API 来工作。这是一个相当简单的 CMS，界面简洁。在左边，一个面板列出了所有的内容类型，允许访问网站的不同类别，在右边，一个类似 Wordpress 的面板显示在前面的主题字段和主文本编辑器。

[![The admin panel](img/e2a36f21885b8a4fc42ad4a06d6f33ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hHo-dm3Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fu9b2ryfxrjpl93sh7qf.JPG)

就我而言，Forestry 在找到我所有的博客文章和项目方面做得非常出色，但在页面内容方面却有点受限。因此，不知什么原因，系统认为我的主页和“关于”页面是不同的，并把它们放在两个不同的部分。

我惊讶地发现，尽管 Forestry 使用 GitHub 的 API，但它还不能访问内容历史。

然而，为了从 Hugo 的多语言支持中受益，可以重命名文件(由 CMS 自动生成)。因此，只需在现有的 slug 中添加扩展`.fr`,我就可以让 Hugo 自动链接两篇博客文章，并向访问者推荐翻译后的版本。

[![Renaming a file](img/ddf27e08a726d6154223d3185135c30f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JR5D9AWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/st7la0bhoqduo0cfq5ox.JPG)

在我的情况下，一个“项目”由于前面的事情而被专门配置。通过修改配置文件中的几行代码，我可以摆脱文本编辑器，只需要一个项目的配置字段。对于 Wordpress 用户来说，这相当于通过高级定制字段插件构建了一个页面，但是通过 YAML 文件生成了一个页面。

[![Editing a project](img/d149d945116f520dfd97087fedcaae77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OuRzO5HI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i3zm1kdop45piuoj6823.JPG)

## CMS 的利弊

使用这种类型的 CMS 的第一个明显的缺点是“技术”方面，即使存在图形界面，这一点仍然非常突出。为了让 Hugo 处理文件，知道如何命名文件是必须的，界面的某些方面可能会吓到一些用户。

由于配置文件中的 slug 系统，允许以隐藏的自动方式命名内容文件，并由于选择输入而改变语言，这将是明智的。

添加具有受限权限的“用户”保留给“业务”计划，每个用户每月起价 9 美元。在其免费版本中，可以添加几乎拥有网站所有权利的“客人”，这可能会变得相当有问题。

例如，您应该注意到，当涉及 Hugo 站点时，Forestry 对前台事务和配置文件强制使用 TOML 格式。在我的例子中，Forestry 检测到了我的项目的“菜单”,这些菜单在任何地方都没有使用。在尝试测试这个功能和改变这个菜单的顺序时，Forestry 决定重写 47 个文件，包括 Hugo 的 config(在一个 YAML 文件中编写 TOML 语法)，这完全破坏了我的构建系统。我不得不使用 Git 来撤销这些修改，并返回到以前的工作版本。

另一方面，需要考虑的是，Forestry 位于 Git 中，因此会生成大量的提交，这当然不会遵守您的提交约定。这是个小问题，但是值得思考。

## 包装完毕

写完这些之后，有人可能会觉得我并不喜欢在林业上的时光，但事实远非如此。虽然它有一些缺陷，但它背后的团队一直在努力改进它。也就是说，当考虑到安装和使用这个系统有多容易时，我会说这是一个非常可靠的静态 CMS 选择。它允许在管理网站时找到一定的灵活性，同时仍然受益于静态网站生成器的速度、安全性和优化的优势。