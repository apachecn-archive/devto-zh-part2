# 为混乱的网页设计带来秩序(使用网页组件)

> 原文：<https://dev.to/thatjoemoore/bringing-order-to-web-design-chaos--3fhb>

## 或者说，我是如何学会不再担心并热爱 Web 组件的

在我作为一名软件工程师的成长过程中，我发现其他工程师遇到的问题和解决方案的故事是改进和学习的无价工具。所以，我要开始分享一些我的代码故事。这是(希望是)许多的第一个，而且不是一个短的。交叉发布自[我的博客](https://www.thatjoemoore.com/posts/2018-01/byu-theme-components/)T3】

这是我去年参与的一个有趣的工程项目的故事。对我来说，这是一个非常有趣的兼职项目(开始时每周约 20 小时，从那以后每周约 5 小时)，这让我可以尝试一些有趣的新技术，实现一些很酷的东西。它集中于一个大型的跨学科团队的努力，为我们所有的许多许多可以在任何 CMS 或前端框架之间共享代码的 web 属性带来一个简单、统一的主题。

# 我们从哪里开始

我在杨百翰大学工作，这是一所大型私立大学。

大学是一个独特的环境。在像我们这样的大学里，你有许多大的学院和学校，它们都有很大的自由去做它们想做的事情。从历史上看，这种自由的一种表现方式是在网页设计中，这导致了一些非常...不一致的设计。

例如，去年的这个时候，我的一位合作设计师查看了我们所有顶级学院和学校的主页，发现它们都有完全不同的设计。他们使用了不同的标志、布局和我们学校的颜色(深蓝色和白色)，在一个极端的情况下，根本没有使用我们学校的颜色。其中许多页面大致基于 2013 年发布的一个设计。这种特殊的设计从来没有得到很好的记录，参考实现也不能跨平台移植，所以随着时间的推移，我们看到了很多分歧和变化。

我不想羞辱任何特定的学校，因为他们的大多数设计实际上看起来相当不错，但这里有一张~~恐怖~~有趣的图片来说明我在说什么:

[![Selection of inconsistent BYU pages](img/3afd9f6b2a1a9a40ed27b823daa34957.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vlGbZ_C6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.thatjoemoore.com/posts/2018-01/byu-theme-components/byu-sites.png)

在聘请了一家外部公司对我们的网站进行审计后，我们决定是时候尝试给大学带来一些品牌一致性了。我们的网络社区发起了一项倡议，旨在设计和实现一个简单、现代、不引人注目的标准主题，并鼓励整个校园的开发人员和设计人员汇聚到这个主题上。

设计部分我就不说了，因为我不是设计师。虽然我被要求参加设计会议，但我是作为一名工程师被要求开发主题的参考实现。但是，我应该注意到，我们决定只需要一个小标题(包括搜索和用户登录)、简单的导航和一个页脚。其他的一切都取决于各个网站的设计，尽管我们也一直在努力为他们提供现成的风格和组件，这样每个网站就不必重新设计公共元素。

# 我们的目标

一旦我们的设计团队敲定了一个每个人都可以使用的设计，就到了我们的工程团队实施的时候了。在设计团队工作的时候，我们有一个部门大踏步前进，开发了一个 Drupal 模块，允许他们在几个网站上快速迭代设计，这样设计师就可以获得反馈，知道什么在现实世界中可行，什么不可行。然而，为了长期使用，我们决定我们需要一个解决方案，它能给我们带来三样东西:

*   [ ]跨平台
*   [ ]简单
*   [ ]可修复

## 跨平台

我们在校园中有各种各样的 web 托管平台，从简单的静态网站到 Drupal 和 Wordpress 这样的 CMSes，再到用 Angular、React 和其他框架编写的复杂的单页应用程序。我们需要一个解决方案，允许一个小团队让我们的主题在所有平台上一致地工作。理想情况下，这意味着每件事都有一个单一的代码库。

## 简单

我们在校园里有各种各样的技能组合，从经验丰富的 30 岁老兵到兼职维护部门网站的 CS 学生。我们需要一个简单易用的解决方案，即使开发人员并不了解正在发生的一切——如果您不能可靠地从示例中复制和粘贴，那就太复杂了。

## 可修复

我们过去遇到的一个问题是，随着主题的改进，只有新的站点会采用这些改进。因为每个站点都有自己的主题代码副本，所以没有好的方法来发布更新和修复。

# Web 组件救援

我们小组中的一些人一直在玩 Web 组件，规范的 v1 版本刚刚在 Chrome 中实现。在看了几个关于如何达到我们的目标的提议后，我们决定 Web 组件将会给我们很多我们正在寻找的东西。

Web 组件解决了我们的许多问题。因为它们是平台级的原语，所以我们不必太担心集成——它们可以(理论上)与任何可以使用 DOM 的东西集成。

为了客观地看待这个问题，花了大约 30 分钟的时间，几个没有看过我们代码的学生程序员实现了一个基于我们 Web 组件的 Wordpress 主题。我最喜欢的关于我们可以轻松集成的演示是在我为其工作的校园组织做主题演示时。作为一个现场演示，我拿了一个非常旧的，令人讨厌的页面，并对其进行了改造，以使用新的主题，就在大家面前。大概花了 10 分钟。当然，我之前已经练习过了，但是展示它有多简单还是很有趣的。

除了将它们写出到 DOM 的能力之外，Shadow DOM 给了我们一些非常好的风格隔离，所以我们可以确定站点的风格不会意外地弄乱我们的主题组件，我们的内部风格也不会泄露给外界。

在这个过程中出现了一些小问题，但我们的 CMSes 有一个非常简单的集成故事，我们已经有了使用 React (16+)、Angular (2+)、VueJS 等的单页面应用程序。有一些 SaaS 工具对 DOM 做了一些真正可怕的事情，我们还没有开始工作，但在大多数情况下，它是相当平静的。

**目标签到:**

*   [x]跨平台
*   [ ]简单
*   [ ]可修复

Web 组件也承诺解决我们的一些复杂性问题。如果你熟悉 Bootstrap，你会知道为了使用他们的风格，你必须以正确的方式组织你的标记，用许多嵌套的`div`和特殊的类。人们很容易迷失在所有这些结构中，特别是因为我们得到的设计在移动和桌面视图之间有很大的布局差异。Shadow DOM 中的插槽概念为我们提供了一种非常简单的方法，向消费者隐藏标记的复杂性，同时仍然允许他们插入所有特定于站点的内容。

(如果您不熟悉 Shadow DOM，那么插槽很像是用 React 中的`children`或 Angular 中的`<ng-content>`进行组件合成)

我们最终的标题标记看起来像这样:

*~~(注意字体不是实际的字体，因为那些只能在*.byu.edu 网站上加载。打开完整的代码笔查看桌面视图。)~~这不再是真的了——我们交换了字体，我根本不需要改变密码笔！#获胜*

[https://codepen.io/ThatJoeMoore/embed/jZyXwb?height=600&default-tab=result&embed-version=2](https://codepen.io/ThatJoeMoore/embed/jZyXwb?height=600&default-tab=result&embed-version=2)

乍一看，这里发生了很多事情。然而，大部分的结构复杂性是隐藏的，只有特定于给定站点的内容才被包含在内。

为了让您了解有多少结构被遗漏了，下面是 Shady DOM polyfill 渲染后的样子，它将所有内容合并到一个 DOM 树中:

```
<byu-header mobile-max-width="1023px" max-width="1200px" class="byu-component-rendered" left-align="">
    <div id="header" class="byu-header-root">
        <div class="byu-header-content needs-width-setting stretches">
            <div class="byu-header-primary">
                <a class="byu-logo" id="home-url" name="home-url" href="https://byu.edu/">
                    <img class="byu-logo" alt="BYU" src="https://cdn.byu.edu/shared-icons/latest/logos/monogram-white.svg">
                </a>
                <div class="byu-header-title">
                    <a slot="site-title" href="some-site.byu.edu" class="">Site Title</a>
                </div>
            </div>
            <div id="secondary" class="byu-header-secondary">
                <div class="byu-header-user">
                    <byu-user-info slot="user" class="byu-component-rendered" has-user="" role="button">
                        <div class="byu-user-wrapper">
                            <div class="no-user slot-wrapper">
                                <div class="user-info-image" aria-label="User Icon"> </div>
                                <span class="text slot-wrapper">
                                    <a slot="login" href="#login">Sign In</a>
                                </span>
                            </div>
                            <div class="has-user">
                                <span class="name slot-wrapper">
                                    <span slot="user-name">Joe Student</span>
                                </span>
                                <div class="user-info-image" aria-label="User Icon"> </div>
                                <span class="logout slot-wrapper">
                                    <a slot="logout" href="#logout">Sign Out</a>
                                </span>
                            </div>
                        </div>
                    </byu-user-info>
                </div>
                <div class="byu-header-search">
                    <byu-search slot="search" action="submit-form" class="byu-component-rendered">
                        <div id="search-form" class="">
                            <div id="search-container" class="">
                                <form action="search.php">
                                    <input name="search" class="__byu-search-selected-input" placeholder="Search" title="Search" type="search">
                                </form>
                            </div>
                            <button id="search-button" type="submit" class="">
                                <img id="search-icon" src="https://cdn.byu.edu/shared-icons/latest/fontawesome/search-white.svg"
                                    alt="Run Search" class=""> </button>
                        </div>
                    </byu-search>
                </div>
            </div>
        </div>
    </div>
    <div class="menu-outer-wrapper">
        <div class="menu-inner-wrapper slot-wrapper needs-width-setting" style="max-width: 1200px;">
            <byu-menu slot="nav" class="byu-component-rendered">
                <nav class="outer-nav slot-container needs-width-setting">
                    <a href="#link1">Link 1</a>
                    <a href="#link2">Link 2</a>
                    <a href="#link3">Link 3</a>
                    <a href="#link4">Link 4</a>
                </nav>
            </byu-menu>
        </div>
    </div>
</byu-header> 
```

我实际上已经简化了一点。这并不漂亮，Web 组件给了我们这个漂亮的小包装，我们可以将所有这些复杂性隐藏起来。

**目标签到:**

*   [x]跨平台
*   [x]简单
*   [ ]可修复

# 动态分配

(这是我真正引以为豪的部分)

一旦我们有了一套可工作的组件，我们需要某种方式将它们分发给校园内的开发人员。我们考虑过使用某种包管理器，比如 NPM，但是我们很快发现它不适合我们的需要。虽然它会给我们一个简单的方法来推出更新，但没有办法保证网站会看到更新。我们有几年看不到更新的网站，而且，如果出现重要的 bug，我们希望这些网站也能得到更新。此外，由于我们需要支持各种各样的平台，很难将每个人都容易使用的单一包管理器融合在一起。

我们达成一致的解决方案是建立一个中央 CDN，让所有站点从中加载资源。这被证明是一个伟大的想法。我们构建了一个流程，可以响应来自 Github 的 webhook 通知，并将代码更改推送到 CDN。我的办公室一直在积极寻求向亚马逊网络服务的转移，所以我们使用亚马逊 S3、Cloudfront 和 CodeBuild 来构建 CDN。

我们 CDN 的秘制酱就是它的汇编器。当我们对任何 Github 库进行任何推送时，AWS Lambda 函数都会收到一个 webhook 通知，并启动一个在 AWS CodeBuild 上运行的构建。我们使用 CodeBuild 是因为 a)我们不需要为它运行任何服务器，b)它允许我们基本上执行任何可以在 docker 容器中运行的东西。汇编器查看包含在 CDN 中的存储库列表，然后使用 Github API 查找自上次汇编器运行以来已经更改或创建的所有分支和标签。它下载必要的文件，然后将所需的文件复制到我们的 S3 存储桶中。

为了提供自动更新，我们在 CDN 中加入了“别名”的概念。别名使用 Git 标签的 semver 解析来创建别名，如`latest`(总是最高的版本号)、`1.x.x`(获取所有次要版本和错误修复版本)和`1.1.x`(获取 1.1 主要版本的所有错误修复版本)。我们还包括一个`unstable`别名，它指向 master 上当前的任何内容。用户还可以通过提供明确的版本号来坚持使用特定的版本。这使我们能够为大多数网站提供自动更新，同时给那些需要的人更多的控制权。

当消费者想要使用我们的 CDN 中的某些内容时，他们会创建一个这样的 URL:

```
https://cdn.byu.edu/{library name}/{version or alias}/{file name} 
```

所以，我们的主题组件的主 Javascript 的 URL 是`https://cdn.byu.edu/byu-theme-components/latest/byu-theme-components.min.js`。获取当前最新版本并关闭自动更新会有一个 URL`https://cdn.byu.edu/byu-theme-components/1.2.3/byu-theme-components.min.js`。

我们希望最大化我们所有资产的可缓存性，但是这会使新版本的快速发布变得复杂。我们已经找到了一个很好的折衷方案，即假设标签永远不会改变(这通常是正确的)。因此，我们提供来自特定标签的任何内容，这些标签带有缓存头，表明它可以永久缓存(`Cache-Control: immutable, public, max-age=31557600, s-maxage=31557600`)。然后，当用户请求一个别名时(比如`latest`，我们向特定的标签发回一个 302 重定向，并将重定向的缓存过期时间设置为 1 小时。这让我们在经常更新和在用户缓存中保存有用的东西之间取得了良好的平衡。重定向发生得足够快，向用户传输的数据也足够少，不会增加太多的开销，而且我们将绝大多数资源永久缓存在浏览器中(大多数浏览器是一年，但也可能是永久的，对吧？我的意思是，它比大多数 Javascript 框架的生命周期都长)。

**目标签到:**

*   [x]跨平台
*   [x]简单
*   [x]可修复

# 附加挑战

在一个项目中，总有一些你没有预见到的问题(甚至有些你预见到了，但决定以后再去解决)。以下是我们面临的一些挑战。

## 退出一个框架

当我们一年前开始这项工作时， [Polymer 2](https://www.polymer-project.org/) 离发布还有几个月，我们并不迷恋 Polymer 坚持使用永远不会实现的 HTML 导入规范。 [SkateJS](http://skatejs.netlify.com/) 是一个选项，但是我们真的不喜欢 Skate 处理模板的方式。

面对这些选择，我们很早就决定不需要 Web 组件框架。我们的组件将是相当静态的——标记 DOM 一次，允许一些小的动态的东西，然后就完成了。因此，我们认为，使用原始的定制元素 API 并没有那么糟糕，只需增加一个小的帮助器函数集合就可以做一些普通的事情(比如给影子 DOM 添加一个模板)。

如果我们现在开始这个项目，我想我们会使用某种形式的框架，可能是 ~~Polymer 3(尽管它还没有发布)或 SkateJS 5(带有它漂亮的新的可插拔呈现器)~~ LitElement。虽然我们的设置并不复杂，但我们仍然在许多方面重新发明了轮子。

> ![Joseph Moore profile image](img/fa9f311a0a4153d9b64ea9855d35f666.png)约瑟夫·摩尔@ thatjoemore![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)[@ justinfagnani](https://twitter.com/justinfagnani)已经开发了一堆非聚合物组件，我相信每一个“香草”设置要么会开发 Polymer-lite，要么希望他们已经开发了。03:02AM-05 Sep 2017[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=904902463588839424)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=904902463588839424)3[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=904902463588839424)8

## 唱着加载时间蓝调

这仍然留下了加载我们的元素的问题。使用 HTML Imports 规范，您可以在一个 HTML 文件中定义您的元素，该文件包含您的标记、样式和 JS 代码，所有这些都在一个非常好的文件中。您在页面中弹出一个`<link rel="import">`,您的组件就会神奇地加载它们需要的一切。

然而，我们在校园里有许多胆小的开发人员，我们不想向他们介绍许多新概念(是的，有些人会被一种简单的新型`<link>`标记吓跑)。我们希望能够坚持使用简单的`<script>`标签进行加载。此外，除了 Chrome，没有人想实现 HTML 导入，而 Polymer 1 和 2 的加载方式看起来相当糟糕。(旁注:我仍然渴望有一个[更好的解决方案](https://github.com/w3c/webcomponents/issues/645)来导入 HTML，而不仅仅是“全部用 Javascript 实现”)。

我们也希望我们的开发体验是美好的。如果我们不能仅仅引入一个嵌入了 CSS 和 JS 的 HTML 文档，我们希望能够将 CSS、JS 和 HTML 保存在单独的文件中，但仍然可以用一个简单的脚本标签导入它们。

首先，我们编写了一个简单的 Gulp 模块，它可以为我们组装东西，但是它非常麻烦，并且依赖于以正确的方式格式化的注释。这时我们发现了 Webpack，它为我们解决了这个问题。现在，我们可以有任意的依赖图，使用来自 NPM 的模块，不需要写我们自己的捆绑器就可以完成一般的事情。这对我们来说非常有效，因此，导入我们的主题组件包非常简单:

```
<script async src="https://cdn.byu.edu/byu-theme-components/latest/byu-theme-components.min.js"></script> 
```

## 造型不可造型

Shadow DOM 对于保持我们的模板 CSS 是很棒的，但是如何对消费者在我们的插槽中交给我们的片段进行样式化呢？这个项目的重点是造型！

Shadow DOM 确实为样式化分槽内容提供了一个答案，但它是有限的。我可以利用`::slotted`伪元素来设计它们的样式:

```
<style>
  .my-slot-wrapper ::slotted(*) {
    color: navy;
  }
</style>
<div class="my-slot-wrapper">
    <slot></slot>
</div> 
```

您会注意到`::slotted`接受了另一个选择器。该选择器可以是任何 CSS 选择器，但有一个限制:它只能是一级深度。不能有`::slotted(div > a)`这样的选择器，只能是一级深。

大多数时候，这种限制不是问题，但是我们很快遇到了两个主要问题:样式链接和样式输入。

我们一发布主题组件的测试版，我们就看到人们添加链接到嵌套在`<h1>` :
中的站点标题

```
<byu-header>
  <h1 slot="site-title"><a href="my-site.byu.edu">My Site</a></h1>
</byu-header> 
```

通常情况下，嵌套的文本元素不会给我们带来任何问题——我们在父元素上设置了`color`、`font-size`和`font-family`,然后它向下级联。然而，链接是特殊的。我见过的每个用户代理样式表都将自己的`color`和`text-decoration`应用于链接，而不是从父样式表继承这些样式。如果`<a>`是具有`slot`属性的元素，这不是问题——样式直接应用于它。然而，当链接嵌套在另一个元素中时，我们不能在 Shadow DOM 中设置它的样式。

我们遇到的另一个问题是设计搜索输入的样式。我们的目标是广泛兼容各种框架和 CMSes，每一个都有自己的包装输入的方式，这是我们无法摆脱的。然而，我们需要确保他们的输入得到适当的样式。一种解决方案可能是呈现我们自己的样式输入，并使用 Javascript 来保持两者相互更新，但是我们觉得这种方法很可能会让开发人员感到困惑。例如，它会使实现像自动完成弹出窗口这样的东西变得非常非常困难。

因此，我们需要能够拥有这个:

```
<byu-search>
  <div class="my-cms-wrapper">
    <input type="search" />
  </div>
</byu-search> 
```

造型如下:

```
<byu-search>
  <input type="search" />
</byu-search> 
```

这两个问题的解决方案是我们已经熟悉的东西:外部 CSS 样式表。我们添加了一个包含特殊选择器的样式表(我们称之为“额外”样式)来处理这些和其他极端情况，我们让开发人员在他们的页面中包含样式表和 Javascript:

```
<link rel="stylesheet" href="https://cdn.byu.edu/byu-theme-components/latest/byu-theme-components.min.css">
<script async src="https://cdn.byu.edu/byu-theme-components/latest/byu-theme-components.min.js"></script> 
```

开发人员已经习惯了包含成对的 CSS 和 Javascript 文件(就像 jQuery UI)，所以我们没有向他们介绍任何新的东西。

因为我们使用了定制的元素名称，所以‘extra’样式表中的选择器可以非常简单，但却很具体:

```
byu-header > [slot="site-title"] a {
    text-decoration: none;
    color: white;
} 
```

虽然我们尽量避免深度超过两层的选择器，但是最外层的两层(`byu-header > [slot="site-title"]`)为我们提供了很好的范围，所以我们的样式不会溢出并影响页面上的样式。仍然有可能页面后面的一些样式表会做类似于
的事情

```
a {
    color: green;
} 
```

我们决定，既然我们不能解决一切，我们就不去尝试解决这个问题。毕竟，这只是 CSS 行为符合预期的一个实例，所以开发人员应该能够自己弄清楚为什么网站标题突然变绿了(至少在浏览器的 devtools 的帮助下)。

## <闪现>被认为有害的*(未样式化内容的闪现)*

使用 JS 来呈现部分 UI 的一个缺陷是会闪现无样式的内容。这是整个前端框架的一个普遍问题，并不是特定于 Web 组件的，尽管我希望像[声明性阴影 DOM](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Declarative-Shadow-DOM.md) 这样的提议可以帮助减少 it 在使用 Web 组件时的影响。

通常，克服这一点的策略是在初始 HTML 中嵌入一些 CSS，这些 CSS 只在框架加载并开始呈现之前适用。我们没有一个好的方法让人们嵌入样式，同时仍然保持我们的目标，即轻松地推出更新，所以我们妥协了，在我们的“额外”样式中嵌入了无样式内容样式的 Flash。这个 CSS 文件比我们的 JS 小得多，解析速度也快得多，所以我们可以从中获得非常好的体验。一旦组件标记了它们的模板，它们就将`byu-component-rendered`类应用到自己身上，所以我们在样式中需要做的就是寻找没有
类的组件

```
byu-header:not(.byu-component-rendered) {
    /* nasty fallback styles here */
} 
```

由于我们的 CSS 是同步解析的，这使得我们的页面在等待 JS 完成它的工作时看起来几乎是正确的。这导致了几乎无缝的页面转换，尤其是在浏览器缓存了 CSS 之后。

# 未来的工作

我们希望在未来增加的一项功能是客户端分析。虽然我们可以从我们的服务器日志中收集大量信息，但我们看不到一些关键的东西，比如有多少站点正在使用不同的配置选项和组件组合。我们正在考虑使用`navigator.sendBeacon`将轻量级分析信息发送回我们的服务器，这些信息包括已经加载的代码的构建、实际使用的组件、正在使用的每个组件的不同特性等。我们不想依赖外部分析网站，因为我们不想给我们所有的网站增加加载时间和隐私方面的担忧。如果个别网站想使用全功能的分析工具，他们可以选择，我们不会干预。

我们想添加的另一件事是自动化测试。我们可以做的逻辑测试不多，因为我们的组件中没有太多的逻辑，但我们真的很期待添加使用视觉差异工具的测试，如 [Pixelmatch](https://github.com/mapbox/pixelmatch) 来检测我们何时做出了改变元素样式的更改。我们的目标是在我们的 CDN 汇编器中有一个运行测试的步骤，如果测试失败，不部署新代码。

# 总结

这是一个非常有趣的项目，迫使我们应对许多有趣的挑战。我真的为我们提出的解决方案感到骄傲，也为来自不同部门和不同技能水平的一组开发人员聚在一起提出适合所有人的解决方案而感到骄傲。虽然我们的代码并不完美，而且我们还没有真正有机会让它符合一个单一的风格，但我觉得我们已经做了相当不错的工作。

对于那些想看看我们代码的人来说，这些代码都是 BYU Web Github 组织的一部分，并获得了 Apache 2.0 的许可。

几个快速链接:

*   [我们的主要主题组件](https://github.com/byuweb/byu-theme-components)
    *   [主题组件图库](http://2017-components-demo.cdn.byu.edu/)
*   [我们的 CDN 来源](https://github.com/byuweb/web-cdn)
*   利用 Web 组件的 Drupal 和 [Wordpress](https://github.com/byuweb/WordPress) CMS 主题。