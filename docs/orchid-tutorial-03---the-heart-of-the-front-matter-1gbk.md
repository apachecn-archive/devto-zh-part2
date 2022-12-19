# 兰花教程 03 -物质的核心

> 原文：<https://dev.to/cjbrooks12/orchid-tutorial-03---the-heart-of-the-front-matter-1gbk>

# 简介

上一个教程我们开始尝试在我们的网站上添加页面并定制它们的内容。在本教程中，我将向您介绍 Front Matter 和 Page 模板，并向您展示如何使用它们来提高您的兰花站点中页面的一致性。

在继续之前，请确保您已经按照前面的教程进行了操作，并且已经使用`gradle orchidServe`启动了您的本地兰花服务器。在本教程中，我们将基于这个例子。

你可以自己跟随这个教程，或者在 [OrchidTutorials 库](https://github.com/JavaEden/OrchidTutorials/tree/master/03)中找到它的源代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [兰花](https://github.com/orchidhq) / [兰花](https://github.com/orchidhq/Orchid)

### 构建和部署与您一起成长的漂亮的文档站点

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Orchid](img/eb7f166eb445fa57d6c2364a5a005cc8.png "Orchid")](https://orchid.run/) 
**构建和部署与您一起成长的精美文档网站**

* * *

[![Current Version](img/0c72744f2b1b9c09e81058ea16d017a3.png "Current Version")](https://bintray.com/javaeden/Orchid/OrchidCore/_latestVersion)[![License: LGPL-3.0](img/0561c8fa72f81c47a6b5ec394037be00.png "License: LGPL-3.0")](https://www.gnu.org/licenses/lgpl-3.0)[![Linux and Mac Build Status](img/1a217af32dacd351a6eb6e5929c3a579.png "Linux and Mac Build Status")](https://travis-ci.org/orchidhq/orchid)[![Windows Build status](img/4c3d58f4c9f5f0e4e69e504c56766560.png "Windows Build status")](https://ci.appveyor.com/project/cjbrooks12/orchid/branch/dev)[![Code Coverage](img/7d3a3ce870c280f4289105e25d15df83.png "Code Coverage")](https://www.codacy.com/app/cjbrooks12/Orchid?utm_source=github.com&utm_medium=referral&utm_content=orchidhq/orchid&utm_campaign=Badge_Coverage)

[快速入门](https://orchid.run/wiki/user-manual/getting-started/quickstart)[文档](https://orchid.run/wiki/user-manual/getting-started)[教程](https://orchid.run/wiki/learn)[展示](https://orchid.run/showcase)[支持](https://gitter.im/JavaEden/Orchid)

## 快速启动

*   [![Gradle](img/d7b7758247fd5d9a702ccc8fef4a8a78.png "Gradle")T2】](https://orchid.run/wiki/user-manual/getting-started/quickstart#gradle)
*   [![Maven](img/333008a337166354ec2e3aae317f2446.png "Maven")T2】](https://orchid.run/wiki/user-manual/getting-started/quickstart#maven)
*   [![SBT](img/f083f972225ab4cd2ca281f78814ec5b.png "SBT")T2】](https://orchid.run/wiki/user-manual/getting-started/quickstart#sbt)
*   [![Deploy to Netlify](img/9df7dbf561b0947dd4e9a437be4988c8.png "Deploy to Netlify")T2】](https://app.netlify.com/start/deploy?repository=https://github.com/orchidhq/OrchidStarter)

## 证明文件

兰花的用户手册将带您了解兰花的主要功能，并让您更深入地了解每个主题和功能。

[文档](https://orchid.run/wiki/user-manual/getting-started)

## 教程

有几个教程旨在引导您从头开始构建一个兰花站点。所有教程的源代码也可以在[或教程库](https://github.com/orchidhq/OrchidTutorials)中找到。

[教程](https://orchid.run/wiki/learn)

## 显示优点的东西

查看陈列柜，看看兰花的行动。

[展示区](https://orchid.run/showcase)

## 支持

### 贡献者

这个项目的存在要感谢所有做出贡献的人。要参与，请见[投稿. md](https://github.com/orchidhq/orchid/blob/dev/.github/CONTRIBUTING.md)

[![Contributors](img/73f9b90f5c85df8ee8758e01a52152ba.png "Contributors")](https://github.com/orchidhq/orchid/graphs/contributors) 

### 巴克斯

感谢我们所有的支持者！<g-emoji class="g-emoji" alias="pray" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64f.png">🙏</g-emoji>

[![Backers](img/4b5ae13c41799627fe67f964c4f987f9.png "Backers")](https://opencollective.com/orchidssg#backers) 

### 赞助商

通过成为赞助商来支持这个项目。您的徽标将显示在此处，并带有指向您网站的链接。

[![Sponsor 1](img/5855ef0cb57006e34da0600a502f1e4a.png "Sponsor 1") ](https://opencollective.com/orchidssg/sponsor/0/website) [ ![Sponsor 2](img/9637b83e9d1de20c4a680c1c7ae05b5d.png "Sponsor 2") ](https://opencollective.com/orchidssg/sponsor/1/website) [ ![Sponsor 3](img/d044bb6c0851431fc8586688f1f5cf55.png "Sponsor 3") ](https://opencollective.com/orchidssg/sponsor/2/website) [ ![Sponsor 4](img/ffe27bf4033460ee03426c33a400e613.png "Sponsor 4") ](https://opencollective.com/orchidssg/sponsor/3/website) [ ![Sponsor 5](img/1feb04f8ae16bff9e8a659140cc468a6.png "Sponsor 5") ](https://opencollective.com/orchidssg/sponsor/4/website) [ ![Sponsor 6](img/ee6b82364437fe7a96935dbeae058db3.png "Sponsor 6") ](https://opencollective.com/orchidssg/sponsor/5/website) [ ![Sponsor 7](img/05a9d69c04ac76393e885fa6a1eb327a.png "Sponsor 7") ](https://opencollective.com/orchidssg/sponsor/6/website) [ ![Sponsor 8](img/eac5c511c942df18d370017a0b432d09.png "Sponsor 8") ](https://opencollective.com/orchidssg/sponsor/7/website) [ ![Sponsor 9](img/78e4d5e5cec08c9057a72dba80203d6f.png "Sponsor 9") ](https://opencollective.com/orchidssg/sponsor/8/website) [![Sponsor 10](img/e723a91c6047bd10234de9ce09470c39.png "Sponsor 10")](https://opencollective.com/orchidssg/sponsor/9/website) 

### 许可证

Orchid 是在 GNU 宽松通用公共许可证(LGPL-3.0)下许可的开源软件。请看 [License.md](https://github.com/orchidhq/orchid/blob/dev/License.md)

### 接触

…

</article>

[View on GitHub](https://github.com/orchidhq/Orchid)

# 身前要紧

如果您曾经使用过其他静态站点生成器，您可能非常熟悉 Front Matter。兰花像其他工具一样使用前端物质，但是兰花对前端物质有一些特殊的处理，所以不要跳过前面！

## 什么是前方物质？

Front Matter 是一种向单个页面添加配置值和其他数据的方法。它是一页最开始的一对三点划线之间的一块 YAML。让我们看看如何使用 Front Matter 定制我们的`houston.md`页面的标题。继续将`pages/locations/houston.md`的内容更改为以下内容:

```
--------
title: 'H-Town'
-------- ## Location

Houston, TX

## Address

1234, Example Dr.
Houston, TX, 12345

## Phone

(123) 456-7890

## Business Hours

M-F: 6am - 9pm
Sa: 6am - 10pm
Su: Closed 
```

Enter fullscreen mode Exit fullscreen mode

*注意:Dev.to 上的代码渲染器有一个 bug，请确保使用`---`作为你的前置物质块，而不是`--------`* 。

当兰花重建您的网站，刷新页面，你会注意到它的标题现在显示为“H-Town”而不是“休斯顿”。您还会注意到，前面的内容已经从实际的页面内容中删除，因此它不会与页面中的其他文本一起嵌入到您的主题中。

每当 Orchid 看到一个页面有首页内容块时，它就会从页面内容中删除该块，将首页内容中的文本解析为 YAML，并使用它来配置页面。有许多配置值可以在前面的内容中使用，后面的教程将向您展示如何找到所有这些值，但您并不仅限于这些配置值。你可以把你想要的任何类型的数据放在你的封面上，它将同样可供使用。

## 预编译

现在我们已经在页面顶部添加了 Front Matter，兰花将开始做一些真正酷的事情。对于我们的`houston.md`页面，在作为 Markdown 处理之前，它将被*预编译*为 Pebble。Pebble 是兰花使用的主要模板语言，它的语法非常类似于 Liquid 或 Twig，这意味着它可以呈现任何想要的文本内容。

这对我们来说意味着我们可以使用 Pebble 将动态数据添加到我们的 Markdown 中，这些数据随后可以作为 Markdown 进行处理。

要了解这是如何工作的，让我们将休斯顿业务位置的信息移动到它的 Front Matter 中，然后使用 Pebble 将该数据注入回我们的 Markdown 中。

```
--------
city: 'Houston'
state: 'TX'
postal_code: '12345'
address: '1234, Example Dr.'
phone: '(123) 456-7890'
business_hours:
 - 'M-F: 6am - 9pm'
 - 'Sa: 6am - 10pm'
 - 'Su: Closed'
-------- ## Location

{{ city }}, {{ state }}

## Address

{{ address }}
{{ city }}, {{ state }} {{ postal_code }}

## Phone

{{ phone }}

## Business Hours

{% for hours in business_hours %}
- {{ hours }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

现在这一页上的内容应该很明显了。所有看起来像`{{ city }}`的标签会在页面的那个位置打印这个变量。还有*标签*，比如`{% for %}`和`{% if %}`标签，它们将遍历数据或者控制显示哪些代码段。这使得使用 Pebble 成为可能——有点像一种完整的编程语言，并且因为它在页面被转换为 Markdown 之前运行，所以您可以使用它更简单地构建更复杂和强大的 Markdown 页面。

这个预编译步骤只对有前端模块的页面执行，它完全是可选的。但是，在某些情况下，您可能会发现 Pebble 使底层页面格式不可解析，并且可能希望禁用它。如果你需要前置，但不想预编译页面的其余部分，你可以在前置中设置`precompile`为`false`，或者你可以通过设置`precompileAs`为期望的文件扩展名来预编译为不同的语言。

# 页面模板

好了，现在我们已经用 Front Matter 设置了我们的休斯顿页面，并预编译为 Pebble，我们可能也应该对我们的其他位置页面做同样的事情。

```
--------
city: 'Austin'
state: 'TX'
postal_code: '12345'
address: '1234, Example Dr.'
phone: '(123) 456-7890'
business_hours:
 - 'M-F: 6am - 9pm'
 - 'Sa: 6am - 10pm'
 - 'Su: Closed'
-------- ## Location

{{ city }}, {{ state }}

## Address

{{ address }}
{{ city }}, {{ state }} {{ postal_code }}

## Phone

{{ phone }}

## Business Hours

{% for hours in business_hours %}
- {{ hours }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

```
--------
city: 'Dallas'
state: 'TX'
postal_code: '12345'
address: '1234, Example Dr.'
phone: '(123) 456-7890'
business_hours:
 - 'M-F: 6am - 9pm'
 - 'Sa: 6am - 10pm'
 - 'Su: Closed'
-------- ## Location

{{ city }}, {{ state }}

## Address

{{ address }}
{{ city }}, {{ state }} {{ postal_code }}

## Phone

{{ phone }}

## Business Hours

{% for hours in business_hours %}
- {{ hours }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在你会注意到我们三个页面的主要内容是完全相同的！我们已经设法将所有数据移到我们的前台，使其更容易管理，但如果我们必须在需要更新时更改每个页面，那么试图保持所有位置页面的外观一致将会很困难。

大多数 SSG 和 CMS 会用主题布局来解决这个问题。您可以为这些位置页面添加一个新的布局，在该布局中，您可以放置我们当前在页面内容中拥有的所有结构化数据。虽然兰花确实有主题布局(我们将在另一个教程中讨论)，但这可能会导致一个问题，因为它们不太可重用，并且您最终会得到许多单次使用的布局，这些布局很难维护或进行站点范围的更改。

为了解决这个问题，Orchid 引入了几个新的管理页面内容的机制，这些机制是可维护的、可重用的和可组合的。我们将在本教程的剩余部分讨论的技术被称为**页面模板**。

## 什么是页面模板？

简而言之，页面模板有点像你的主题布局，除了它们只与显示页面内容的一小部分布局有关。通常，一个完整的布局还包括菜单、侧栏、小部件区域，并且还呈现完整的 HTML 结构，但是一个*页面模板*仅仅构造了*页面内容*。虽然一个普通的 CMS 会让你创建`page-single.peb`和`page-sidebar.peb`，但你现在可以只为你的页面内容创建一个`pages/page.peb`模板，然后将它嵌入到`layouts/single.peb`或`layouts/sidebar.peb`主题布局中。

在这个小例子中，这种不同可能看起来无关紧要，但是对于有许多不同类型的页面和布局风格的站点，你实际上使用的模板比备选方案要少得多。考虑到一个有 10 种页面类型和 3 种不同布局的网站在传统方式下需要 **30 个模板**，但是在兰花模式下只需要 13 个！对于更大的站点，节省的成本甚至更大，维护负担也将显著降低。

## 创建页面模板

我们需要做的第一件事是为自己制作一个新的页面模板，然后我们会告诉 Orchid 使用那个模板而不是默认的模板。所以在`templates/pages/location.peb`里做一页，加上下面的内容。

```
{% filter compileAs('md') %}
## Location

{{ city }}, {{ state }}

## Address

{{ address }}
{{ city }}, {{ state }} {{ postal_code }}

## Phone

{{ phone }}

## Business Hours

{% for hours in business_hours %}
- {{ hours }}
{% endfor %}

--------
{% endfilter %}

{{ page.content | raw }} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这个新文件的扩展名为`.peb`，这意味着它将被编译为 Pebble。与我们的 Markdown 页面不同，页面模板应该使用一种*模板化*语言的文件扩展名，比如`.peb`，并且它不支持预编译。这是因为页面模板实际上被认为是*主题*的一部分，而不是*页面内容*的一部分。

因此，在设置页面模板时，你应该把它设计成主题的一部分，并在模板的适当位置包含`{{ page.content | raw }}`。`page.content`实际上是预编译和渲染你的页面的 Markdown，由于 Pebble 是一种*安全的*模板语言，它将自动转义 HTML 页面内容以防止跨站点脚本(XSS ),除非你在打印时使用`| raw`过滤器。

## 使用您自定义的页面模板

要使用我们的定制页面模板，我们必须在每个页面的前端添加一个属性，告诉 Orchid 我们想要使用哪个模板。`templates`属性接受一个字符串或一个数组，兰花找到的第一个模板将被加载并用作页面模板。

```
 --------
city: 'Houston'
state: 'TX'
postal_code: '12345'
address: '1234, Example Dr.'
phone: '(123) 456-7890'
business_hours:
 - 'M-F: 6am - 9pm'
 - 'Sa: 6am - 10pm'
 - 'Su: Closed'
templates: 'location'
-------- 
Houston, TX location coming soon. 
```

Enter fullscreen mode Exit fullscreen mode

兰花将在几个地方寻找页面模板。首先，它会在`templates/`中寻找一个与文件路径和文件名完全匹配的模板。如果不存在，它将在`templates/pages/`目录中查找任何具有该文件名的文件。在这两种情况下，如果没有提供文件扩展名，它将采用`.peb`。所以通过传递`location`作为模板，我们在`templates/pages/location.peb`的自定义页面模板将被使用。

无论何时指定自定义模板，它都将优先于该页面类型的默认值。具有特殊页面类型的插件可以设置特殊格式来查找页面模板，而不需要指定它。例如，OrchidPosts 插件将默认在退回到`page.peb`之前寻找`post-[category].peb`或`post.peb`。如果插件或用户没有指定，所有页面都将使用自动为您提供的`page.peb`。

# 结论

让我们回顾一下我们在本教程中学到的内容。

1.  Orchid 可以自己很好地呈现 Markdown 和其他文件，但是您可以选择在页面开始处添加一个 Front Matter 块来配置它或添加自定义数据。
2.  当页面中出现 Front Matter 块时，它将首先被预编译为 Pebble 模板。您可以使用任何 Pebble 标签，或者在内容被降价处理之前，在内容中注入任何页面或站点变量。
3.  为了提高一致性并减轻大型网站的维护负担，除了主题布局之外，还可以使用页面模板来为页面内容提供结构。页面模板可以与主题布局混合搭配，使用比布局更少的模板就可以提供更大的灵活性。
4.  您可以在页面首页选择带有`templates`属性的页面模板。这可以是一个字符串或数组，模板通常保存在`templates/pages/`目录中。
5.  插件可能会提供自己的格式来查找页面模板，但是自己设置一个格式会覆盖插件的偏好。

* * *

本教程最初发布在[官方兰花文档](https://orchid.netlify.com/wiki/learn/tutorials/heart-of-the-front-matter)中。