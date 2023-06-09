# 兰花教程 04 -链接所有的东西！

> 原文：<https://dev.to/cjbrooks12/orchid-tutorial-04---link-all-the-things-af3>

# 简介

我们的兰花站已经开始布置得很好了，也很容易维护，但是在它完全准备就绪之前，我们还有一些事情需要清理。最值得注意的是，在我们的位置索引页面中仍然有那些硬编码的链接。在本教程中，我将向您展示兰花解决这个问题的方式。

在继续之前，请确保您已经按照前面的教程进行了操作，并且已经使用`gradle orchidServe`启动了您的本地兰花服务器。在本教程中，我们将基于这个例子。

你可以自己跟随这个教程，或者在 [OrchidTutorials 库](https://github.com/JavaEden/OrchidTutorials/tree/master/04)中找到它的源代码。

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

# 制作动态链接

让我们首先回顾一下我们的`locations/index.md`页面中有哪些内容:

```
- [Houston](http://localhost:8080/locations/houston)
- [Dallas](http://localhost:8080/locations/dallas)
- [Austin](http://localhost:8080/locations/austin) 
```

Enter fullscreen mode Exit fullscreen mode

这有几个问题。第一个也是最明显的问题是，这些链接被硬编码为使用我们的开发基础 URL`http://localhost:8080`。当这个网站链接到开发 URL 时，我们不想让它上线，但是 Markdown 没有任何方法可以动态地改变这些链接。

但是还记得我们在上一个教程中是如何添加一个前置物质块来启用 Pebble 预编译的吗？即使我们真的没有任何数据放在 Matter 前面，我们还是要添加它，这样我就可以向你介绍一个重要的**全局变量**，你可以在 Orchid 中使用它，`site`。

## 站点全局变量

在 Orchid 中的每一页上都有`site`变量，它包含一些可能有用的属性，或者用于显示，或者用于控制流。一些值得注意的属性是:

*   关于你的站点的附加信息，如标题、头像、图标。另一个教程将教你如何定制这些值
*   `site.baseUrl` -您网站的基本网址
*   `site.debug` -如果兰花正在调试中运行，则为真
*   `site.orchidVersion` -兰花的当前版本
*   `site.version` -你的应用或网站的当前版本

这个房产看起来正是我们所需要的！我们可以用那个变量替换`locations/index.md`中硬编码的基本 URL，它会根据`build.gradle`中设置的`baseUrl`属性自动更新这些 URL。

```
--------
--------
- [Houston]({{ site.baseUrl }}/locations/houston)
- [Dallas]({{ site.baseUrl }}/locations/dallas)
- [Austin]({{ site.baseUrl }}/locations/austin) 
```

Enter fullscreen mode Exit fullscreen mode

*注意:Dev.to 上的代码渲染器有 bug，确保对你的前置物质块使用-而不是-。*

但是我们可以做得更好。这些网址的某些部分可能会在我们不知道的情况下被更改，这将导致链接断开。让我们看看如何避免这种情况。

# 链接功能

Orchid 附带了一些功能，可以用来智能地在您的 Orchid 站点中查找其他页面。一般来说，您可以使用这些函数根据标题定位任何页面，然后让 Orchid 为您生成完整的 URL。这就把打印出来的链接和你做链接背后的意图分开了，你的意图是去一个特定的、已知的页面。

## 链接功能

我们可以使用的第一个函数是`link()`。先说个例子:

```
--------
--------
- [Houston]({{ link('Houston') }})
- [Dallas]({{ link('Dallas') }})
- [Austin]({{ link('Austin') }}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们不再手动创建指向我们各个位置页面的链接，而是简单地将目标页面的*标题*传递给`link()`函数，该函数返回标题相同的页面的字符串 URL，这正是我们想要的！如果我们改变了位置页面的位置，比如说`/locations/tx`，只要这些页面的标题不变，我们就不必更新所有的链接。此外，这些链接使用相同的基本网址是在`site.baseUrl`，所以我们可以相信，这些链接将适应我们的开发和生产网站。

## 锚功能

`link()`函数很有用，但是还有一个问题。如果我们碰巧传递了一个不存在的页面的标题，它将简单地返回一个空字符串。但是 Markdown 仍然会创建一个 anchor 标签，它只是有一个空的`href`属性，这对 SEO 和用户的浏览体验都是不好的。我们真正想要的是，如果页面确实存在，只创建一个可点击的链接，否则只呈现文本。虽然这不是一个完美的解决方案，但至少你会有文本的视觉提示*而不是*是一个链接，这对于 SEO 来说更好。

大多数 SSG 和 CMSs 会让你检查返回的 URL 是否为空，并将链接包装在一个`if`语句中。但是这变得很乏味，使你的模板难以阅读，所以兰花提供了另一个标签，专门为智能生成可点击的锚链接，`anchor()`。通过`anchor()`功能，我们可以替换整个 Markdown 链接，让兰花替我们创建:

```
--------
--------
- {{ anchor('Houston') }}
- {{ anchor('Dallas') }}
- {{ anchor('Austin') }} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的模板不仅更容易阅读，而且如果页面不存在，它也不会生成链接，而是直接返回传递给函数的文本。默认情况下，传递给函数的文本被用作链接中的文本，但这可以通过向函数传递附加参数来更改。如果有两个参数，那么第一个是将在链接中显示的文本，而第二个用于查找页面。

```
--------
--------
- {{ anchor('Houston, TX', 'Houston') }}
- {{ anchor('Dallas, TX', 'Dallas') }}
- {{ anchor('Austin, TX', 'Austin') }} 
```

Enter fullscreen mode Exit fullscreen mode

# 生成页面列表

## 查找所有功能

我们的位置索引页面现在看起来非常好。我们所有的链接都是完全动态的，将自动更新，如果页面被移动，为我们不同的环境。但是我们的业务增长非常快，我们几乎每天都在增加新的地点！您不希望每次添加新位置时都必须更新这个列表，您真正想要的是根据每个位置的文件自动生成整个列表。

幸运的是，兰花也有一种方法来处理这一点，它的工作方式非常类似于仅仅链接到一个页面。有一个`findAll()`函数，它接受与`link()`和`anchor()`函数相同的输入，但是它不是返回指向那个页面的字符串 URL 或锚点，而是返回一个页面对象列表，然后您可以使用这个列表轻松地为自己建立链接。

以下面的片段为例。它是不完整的，因为我们还需要添加更多的函数参数，但是我一会儿会回到这个话题。现在，请注意我们可以迭代结果并手动构建链接。

```
{% for page in findAll(...) %}
- [{{page.title}}]({{page.link}})
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

## 过滤收藏

在我们的小企业位置的例子中，我们想要链接的所有页面都来自于`pages`插件，更具体地说，因为它们在`pages`中最顶层的文件夹是`locations/`，所以它们被认为是在`locations`页面组中。这两个属性合起来称为**集合类型**和**集合 id** 。

每个生成页面的插件，就像我们的静态页面插件一样，也会生成一个或多个页面集合。这些集合实际上是所有`link()`、`anchor()`和`findAll()`函数的基础，它们都允许你过滤与它们相应的`collectionType`和`collectionId`匹配的页面。具体如何格式化取决于插件，所以一定要查看插件的文档，但是一般来说，`collectionType`匹配它们来自的生成器的键，而`collectionId`是来自那个生成器的页面的特定子集。

当您将一个`collectionId`或`collectionType`传递给上述任何一个函数时，搜索的范围仅限于那些匹配的集合。`itemId`是我们一直作为标题传递的属性，对于`link()`和`anchor()`是必需的，但是对于`findAll()`是可选的。`itemId`过滤匹配集合中包含的页面。

有了这些知识，我们就可以完成我们的例子，不仅可以生成到我们位置页面的动态链接，还可以动态生成整个列表！

```
{% for page in findAll(collectionType='pages', collectionId='locations') %}
- [{{page.title}}]({{page.link}})
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

就这样，我们现在可以比以往任何时候都更容易地维护我们的兰花站！我们不仅能够一次更新所有位置页面的内容(正如我们在上一教程中了解到的那样)，还可以在索引页面上动态生成完整的位置列表，因此新位置会自动包含在该列表中。

让我们回顾一下我们在本教程中学到的所有单个作品。

1.  兰花提供了许多方法来生成内容之间的动态链接。第一种，也是最简单的方法，是使用`site.baseUrl`打印出你站点的基本 URL，你可以通过它手动建立到页面的链接。
2.  您可以使用`link()`函数向 Orchid 请求指向您站点中某个页面的字符串 URL，并使用它构建一个 Markdown 链接，而不是手动构建一个 URL。
3.  为了防止链接断开，您可以让 Orchid 使用`anchor()`函数构建一个完整的 HTML 锚。这使用了与`link()`函数相同的搜索查询，您可以选择将包含在链接中的文本作为第一个参数传递给这个方法。
4.  使用`findAll()`函数，生成单个链接的相同搜索也可以用来定位您的兰花站点的页面列表。您可以迭代这个列表的结果，并为列表中的每个项目构建到这些页面的链接，这允许您制作页面链接的动态列表。
5.  `link()`、`anchor()`和`findAll()`函数也接受可选的`collectionType`和`collectionId`参数，这些参数过滤结果，可以用来将搜索范围缩小到您想要的页面。

* * *

本教程最初发布在[官方兰花文档](https://orchid.netlify.com/wiki/learn/tutorials/link-all-the-things)中。