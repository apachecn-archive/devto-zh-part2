# 兰花教程 02 -主页和静态页面

> 原文：<https://dev.to/cjbrooks12/orchid-homepages-and-static-pages-3965>

# 简介

在上一个教程中，我们学习了如何创建一个新的兰花项目，以及如何从命令行启动它。在本教程中，我们将开始添加我们的第一个内容到我们的主页，并了解最基本的，但也是最灵活的插件之一:静态页面。

在继续之前，请确保您已经完成了第一个教程，并使用`gradle orchidServe`启动了您的本地兰花服务器。在本教程中，我们将基于这个例子。

你可以自己跟随这个教程，或者在 [OrchidTutorials 库](https://github.com/JavaEden/OrchidTutorials/tree/master/02)中找到它的源代码。

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

# 您的主页

看看你的第一个兰花站，你会发现它有点平淡无奇。这只是一个没有菜单，没有内容，一点都不有趣的空白页面。但是让我们改变这一点！

每个兰花网站都包括一个主页。这是访问者进入你的网站根目录时会看到的第一页，即使你没有在你的兰花版本中包含任何其他插件，它也会被创建。让我们从在`src/orchid/resources/homepage.md`向您的项目中添加一个新文件开始，并向其中添加以下内容。

```
## Hello, Orchid
 > You are beautiful, and so is your website.

Let's build something _beautiful and unique_, **together**. 
```

Enter fullscreen mode Exit fullscreen mode

好了，看起来好多了。我们现在有一个 Markdown 文件，你在这个文件中写的任何东西都会自动转换成 HTML 并嵌入到你的主题中。

但是这个文件不一定要降价。兰花知道很多不同的语言，如果你愿意，你可以自由地使用其他语言，比如 Asciidoc。通过将文件扩展名从`.md`更改为`.ad`，您将指示兰花将该文件作为 Asciidoc 而不是 Markdown 来处理。

让我们现在做那件事。将`src/orchid/resources/homepage.md`重命名为`src/orchid/resources/homepage.ad`，并将其内容更改为 Asciidoc 中的等效内容。

```
== Hello, Orchid

____
You are beautiful, and so is your website.
____

Let's build something _beautiful and unique_, *together*. 
```

Enter fullscreen mode Exit fullscreen mode

# 添加静态页面

## 示例:小型企业

在你的网站上有一个主页是很棒的，但是你可能有更多的内容想要展示。有很多方法可以给你的站点添加额外的页面，但是最简单的方法是使用静态页面插件。

假设您正在经营一家小型企业，它在德克萨斯州有多个地点:一个在休斯顿，一个在达拉斯，一个在奥斯汀。每个地点应该有自己的网页，列出其名称，地址，电话号码和营业时间。您希望这些页面在`/locations/{locationName}`出现在您的网站上，并且有一个显示所有位置的“索引”页面，这样每个页面都可以很容易地找到。因此，您希望您的网站具有以下页面:

*   `/`
*   `/locations`
*   `/locations/houston`
*   `/locations/dallas`
*   `/locations/austin`

让我们看看如何使用静态页面插件来创建这个网站。

## 添加位置页面

我们已经在`/`设置了我们的主页，所以让我们先设置我们的休斯顿位置页面。我们将从在`src/orchid/resources/pages/locations/houston.md`创建一个新文件开始。这个文件和你主页上的文件一样。你可以在里面写任何你想写的内容，它会被转换成 HTML 并嵌入到你的主题中。同样，就像主页一样，如果需要，您可以更改它的文件扩展名，让 Orchid 将它作为另一种语言来处理。

所以我们给`src/orchid/resources/pages/locations/houston.md`补充以下内容。

```
## Location

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

现在，让我们继续为我们的达拉斯和奥斯汀工厂做类似的事情。将`houston.md`文件复制到`dallas.md`和`austin.md`，更改这些文件中的内容，很快兰花将重建您的网站，并为我们添加这些新页面。

您现在可以在[http://localhost:8080/locations/Houston](http://localhost:8080/locations/houston)、[http://localhost:8080/locations/Dallas](http://localhost:8080/locations/dallas)和[http://localhost:8080/locations/Austin](http://localhost:8080/locations/austin)查看每个业务地点的页面！

## 添加位置索引页面

现在，我们每个地点都有一个独特的页面，让我们继续创建一个索引页面，列出我们所有的单独地点。您可能已经猜到了，我们需要在`src/orchid/resources/pages/locations.md`创建一个新文件，并在那里添加内容。这是因为静态页面插件通过获取`src/orchid/resources/pages/`目录中的*所有*文件，并将它们复制到同一路径的最终站点中。

然而，如果我们要用静态页面构建一个非常大的站点，将位置索引页面放在与其他位置页面不同的文件夹中可能会有点混乱。所以让我们把`src/orchid/resources/pages/locations.md`移到`src/orchid/resources/pages/locations/index.md`。静态页面插件会直接复制任何文件，但对于名为`index`的文件，它会将它们保存在该文件夹的根目录下，而不是在该文件夹中创建子页面。所以`src/orchid/resources/pages/locations/index.md`会变成`/locations`，而不是像其他页面那样变成`/locations/index`。

我们需要尽快推出这个网站，这样我们就可以开始让人们访问所有地点，所以现在让我们只是硬编码链接到每个页面。将以下内容添加到`locations/index.md` :

```
## Our Locations
 - [Houston](http://localhost:8080/locations/houston)
- [Dallas](http://localhost:8080/locations/dallas)
- [Austin](http://localhost:8080/locations/austin) 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

你的兰花站开始看起来不错，但是让我们回顾一下我们做了什么:

1.  兰花的大部分文件都保存在`src/orchid/resources/`里。可以在那个文件夹中添加一个特殊的文件，`homepage.md`,这样兰花就可以用它来创建它的首页。
2.  你可以添加任何你想要的文件到`src/orchid/resources/pages`，兰花会将它们复制到你的最终网站在相同的结构。您还可以在这里将特殊文件命名为`index`，它们将保存在最终站点中该文件夹的索引中，而不是保存在名为 index 的子目录中，index 可用于创建特殊的登录页面。
3.  主页和所有静态页面都根据其文件扩展名进行处理。使用`.md`扩展名将文件处理为 Markdown，使用`.ad`将其处理为 Asciidoc。其实你会发现*任何*页面从*任何*插件都会以类似的方式处理，这样使用所有插件都会感觉很相似。

请继续关注更多关于如何使用兰花的教程。在下一篇教程中，我将向您展示如何通过使链接动态化、添加面包屑和自定义位置页面内容来提高整个站点的一致性并使其更易于导航，从而改进这种小型业务。

* * *

本教程最初发布在[官方兰花文档](https://orchid.netlify.com/wiki/learn/tutorials/homepage-and-static-pages)中。