# 用 PlantUML 标记本地图

> 原文：<https://dev.to/anoff/markdown-native-diagrams-with-plantuml-ik4>

*这篇文章最初发表在我的博客上，署名为[https://anoff.io/blog/2018-07-31-diagrams-with-plantuml/](https://anoff.io/blog/2018-07-31-diagrams-with-plantuml/)，交叉发表于*💖

> 这篇文章将涵盖 PlantUML 基础知识，以及如何在 GitLab 或 GitHub 项目中使用它，以及使用 Visual Studio 代码的无缝本地开发环境。

这是我几个月来一直想写的一篇文章。最近，我在工作中广泛使用 PlantUML，在我的私人项目中也是如此。你可以看到它被用于我在 GitHub 上的 [plantbuddy](https://github.com/anoff/plantbuddy#main-features) 和 [techradar](https://github.com/anoff/techradar#design) 项目。在不同的地方，出于不同的目的使用它，我遇到了一些问题，我想在这篇文章中分享。

# PlantUML 基础知识👨‍🎨

对于那些不知道 [PlatUML](http://plantuml.com/) 的人:它是一个开源工具，允许你用纯文本定义 UML 图。有不同的[图类型](http://plantuml.com/sitemap-language-specification)可用自定义语法描述，但遵循一个共同的方案。这篇文章不会详细讨论每一种图类型，因为 PlantUML 网站在描述[序列](http://plantuml.com/sequence-diagram)、[组件](http://plantuml.com/component-diagram)、[活动](http://plantuml.com/activity-diagram-beta)和其他图类型方面做得很好。

显示数据流的基本组件图可以使用以下标记来构建:

```
@startuml component
actor client
node app
database db

db -> app
app -> client
@enduml 
```

Enter fullscreen mode Exit fullscreen mode

[![basic component diagram showing data flowing from a database via an app to a client](../Images/ceb6c43833e354298016e9823abfa308.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mvpt6QlT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/plantuml/diagrams/dist/component.svg)

## 爱上 PlantUML 的理由🤗

### 版本化📦

开发软件和编写文档的一个非常重要的方面是保持同步。一部分是如果代码本身更新了，就更新文档。另一个重要的部分是版本控制——通常软件是使用`git`或类似的系统进行版本控制的。通过将文档与代码放在同一个存储库中，您可以确保始终查看文档在相应时间点的正确状态。

出于这个原因，我喜欢把我所有的文档作为注释放在源代码中，或者作为源代码旁边的 Markdown 文件。这种方法中我一直缺少的一点是可视化的东西。放置 PowerPoint/Keynote/Visio/企业架构师..文件放入存储库中确实可以确保您的图总是与代码版本一致——但是它们在 Web UIs 中是不可浏览的。让 PlantUML 和 GitLab 渲染来拯救你:GitLab 允许你[将 PlantUML 图](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/administration/integration/plantuml.md)直接嵌入到你的 Markdown 文件中，当你在浏览器中查看这些文件时，它们会被动态渲染。

PlantUML 优于上述工具的另一个好处是，通过以纯文本方式定义您的图，您可以使它们在拉请求中有所不同。审阅者总是可以看到已经做了哪些更改，并且很容易将对图的更改与代码内部的更改进行比较。

### 语法🐟

PlantUML 的基本语法非常简洁，为不同的图类型打下了良好的基础。它还非常聪明，允许用不同的风格来编写图，例如，你可以在顶部声明/实例化所有节点，但是如果你不声明它们，它们将被自动推断出来。同样的道理也适用于[宏和定义](http://plantuml.com/preprocessing)，它们允许你为你的团队组成更大的图表或公共库。

我最近为许多有用的技巧创建了一个[PlantUML cheat sheet](http://anoff.io/blog/img/puml-cheatsheet.pdf)——然而它并没有涵盖 plant UML 语法的基础。你可以在 GitHub 上浏览[最新版本](https://github.com/anoff/blog/raw/master/img/assets/plantuml/puml-cheatsheet.pdf)或者 [LaTeX 源代码](https://github.com/anoff/blog/blob/master/img/assets/plantuml/puml-cheatsheet.tex)。

### 布局🏗

与 WYSIWYG 编辑器相比，PlantUML 图只定义了组件及其关系，而没有定义图的实际布局。相反，该图是在呈现过程中通过确定性算法推断出来的。这在指定图表时是有益的，因为您只关注内容——类似于编写 LaTeX 文档。遗憾的是，布局引擎并不像你有时希望的那样好，尤其是在有 10 个以上节点的组件图中，你可能会花费大量时间来手动执行特定的布局。

对于序列图和活动图，即使对于非常大的图，自动布局也非常有效。在您构建了一些图表并注意到上下移动代码行并让代码中的变化立即反映在文档中是多么容易之后，您将会爱上自动布局。

### 随处分享📱

如果您想*冻结*一个图表版本并将其发送给组织外的某个人，您可以简单地向他们发送一个非常长的 url(例如[http://www . plant UML . com/plant UML/png/5s on 3g 8n 34 r xtbfyb 1 gig 9m 6h 25 gsnh 9 p 8 sggsgslrpxzfilchovse-yyw 8 qdljl 2v-n 93 rj BG 4 edls blg 0 pn 6 wdiu 5 nidcau 6 pijztgkn 5 nnpu 044 您也可以将这个 URL 嵌入到 HTML `<img>`标签中。如果有人需要处理图像，你所要做的就是将`/plantuml/png`切换到`/plantuml/uml`，你就会看到图表的](http://www.plantuml.com/plantuml/png/5Son3G8n34RXtbFyb1GiG9MM6H25XGsnH9p8SGgslrpxzFILcHovse-yYw8QdlJl2v--N93rJ2Bg4EDlSBlG0pn6wDiu5NiDcAU6piJzTgKN5NNPu040)[定义](http://www.plantuml.com/plantuml/uml/5Son3G8n34RXtbFyb1GiG9MM6H25XGsnH9p8SGgslrpxzFILcHovse-yYw8QdlJl2v--N93rJ2Bg4EDlSBlG0pn6wDiu5NiDcAU6piJzTgKN5NNPu040)。

这给了整个 PlantUML toolstack 一种非常通用的传递信息和可视图像的方式。

# 局部发展💻

开始创建 PlantUML 图的最快、不受平台限制且最简单的方法是使用他们的在线编辑器。您可以使用 [plantuml-server Docker 映像](https://hub.docker.com/r/plantuml/plantuml-server/)轻松地在 prem 上托管它。这对于创建只有几个节点的简单图来说很好，但是更大的图需要大量的*预览*，这在在线编辑器中很烦人。

*如果您有任何其他本地设置，请通过[Twitter](https://twitter.com/an0xff)T3】告诉我*

## Visual Studio 代码

> 如果你已经使用了 VS 代码，这是一个很容易的设置。否则，您可能会认真考虑使用它来编辑 PlantUML 图(在 Markdown 中),因为它是一种超级流畅的体验。

你所需要做的就是获得 [PlantUML 扩展](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)来启用代码本地 [Markdown 预览特性](https://code.visualstudio.com/docs/languages/markdown)来解析内嵌图。

[![Screenshot of Visual Studio Code showing rendered PlantUML diagram in Markdown preview](../Images/46ca517c54431f84cf2b1b758ad0e39d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ez59kPHX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/plantuml/code-rendering.png)

默认情况下，插件需要运行一个本地 PlantUML 进程并接受渲染请求。我推荐切换成使用服务器进行渲染；这可能是官方的 plantuml.com 服务器、本地实例或本地运行的[容器](https://hub.docker.com/r/plantuml/plantuml-server/)。安装插件后，进入 VS 代码选项(`ctrl/⌘ + ,`)并更改`plantuml.render`属性。

```
// PlantUMLServer: Render diagrams by server which is specified with "plantuml.server". It's much faster, but requires a server.
// Local is the default configuration.
"plantuml.render": "PlantUMLServer",

// Plantuml server to generate UML diagrams on-the-fly.
"plantuml.server": "http://www.plantuml.com/plantuml", 
```

Enter fullscreen mode Exit fullscreen mode

如果你离开了网络，还想工作，记得在你还有网络连接的时候。图片是`~250MB`下载的。然后将`plantuml.server`设置为`http://localhost:8080/`，你就可以开始离线冒险了。

在我的 MacBook 上，我有时会从运行的容器中体验到大量的 CPU 消耗——即使在不主动渲染时也是如此。重启容器会有所帮助🤷‍

## [渲染为 SVG/PDF](#render-to-svgpdf)

> 只有在文件中明确定义了图，并且没有内联到 Markdown 中时，这种方法才有效。

为了写这篇博文和构建，我尝试了将 PlantUML 图渲染成图像的非实时方法。您可以使用 [Makefile](https://github.com/anoff/blog/blob/master/img/assets/plantuml/Makefile) 和 [Shell 脚本](https://github.com/anoff/blog/blob/master/img/assets/plantuml/diagrams/convert.sh)将带有`.puml`扩展名的 PlantUML 图的整个[文件夹](https://github.com/anoff/blog/tree/master/img/assets/plantuml/diagrams)转换为`.svg`和`.pdf` [文件](https://github.com/anoff/blog/tree/master/img/assets/plantuml/diagrams/dist)。

该脚本实际上是通过 docker 化的 PlantUML 流程运行图定义，该流程输出一个`.svg`，然后使用 Inkscape 创建一个`.pdf`文件，用于将其导入到 LaTeX 文档中。

```
#!/bin/sh
# converts all puml files to svg

BASEDIR=$(dirname "$0")
mkdir -p $BASEDIR/dist
rm $BASEDIR/dist/*
for FILE in $BASEDIR/*.puml; do echo Converting $FILE..
  FILE_SVG=${FILE//puml/svg}
  FILE_PDF=${FILE//puml/pdf}
  cat $FILE | docker run --rm -i think/plantuml > $FILE_SVG
  docker run --rm -v $PWD:/diagrams productionwentdown/ubuntu-inkscape inkscape /diagrams/$FILE_SVG --export-area-page --without-gui --export-pdf=/diagrams/$FILE_PDF &> /dev/null
done mv $BASEDIR/*.svg $BASEDIR/dist/
mv $BASEDIR/*.pdf $BASEDIR/dist/
echo Done 
```

Enter fullscreen mode Exit fullscreen mode

# GitLab 集成

> 该功能目前仅在 GitLab 的本地安装中可用，在 gitlab.com 上启用该功能[是一个未解决的问题](https://gitlab.com/gitlab-com/infrastructure/issues/2163)。请参见 GitHub 集成以获得解决方法。

在 GitLab 中使用 PlantUML 非常有趣。你所要做的就是[设置](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/administration/integration/plantuml.md)一个可以使用的渲染服务器，你只需提交内嵌 PlantUML 图的 Markdown 文件，它们就会为每个访问 GitLab web UI 的人进行渲染。

最棒的是，这不仅适用于提交到 git 存储库中的 markdown 文件，还适用于 GitLab 中呈现 Markdown 的所有其他字段——几乎所有内容。你也可以用小图表来帮助说明问题。

[![Screenshot of PlantUML syntax in a GitLab issue](../Images/5204e6ba7dd98f8f40621eab938762f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wSWARklz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/plantuml/puml-issue.png)

[![Rendered PlantUML diagram in a GitLab issue](../Images/fe39493a610cf5d35a9b6e3b4865ec46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--stOvpDRX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/plantuml/puml-issue-rendered.png)

# GitHub 集成

GitHub 和 gitlab.com 没有本地的 PlantUML 集成。为了保持上面列出的优点，仅仅在本地呈现文件并将它们提交给 git 显然不是一个有效的解决方法。

相反，使用 PlantUML [代理服务](http://plantuml.com/server)，如[堆栈流讨论](https://stackoverflow.com/questions/32203610/how-to-integrate-uml-diagrams-into-gitlab-or-github)中所述。其工作方式是，不是将 URL 中的图表内容传递给 PlantUML 服务器，而是定义一个*远程 URL* ，可以从其中获取内容，例如`http://www.plantuml.com/plantuml/proxy?src=https://raw.github.com/plantuml/plantuml-server/master/src/main/webapp/resource/test2diagrams.txt`。这个 URL 可以嵌入到一个 HTML `<img>`标签或者 Markdown 图像语法`![]()`中。要在使用 GitHub 时利用这个特性，只需将*远程 URL* 指向存储库中 PlantUML 图的原始链接。

你可以在我的 GitHub 账户的 [plantbuddy](https://github.com/anoff/plantbuddy#main-features) 和 [techradar](https://github.com/anoff/techradar#design) 项目中看到这种方法的应用。

下图显示了当您打开 GitHub 上包含此类链接的降价页面时会发生什么:

[![sequence diagram showing how PlantUML proxy service works](../Images/8aa7e855bac4aae18fd7e0a3495f11a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0-fq-CvJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/plantuml/diagrams/dist/plantuml-proxy.svg)

[这个例子](https://github.com/anoff/plantbuddy/blame/master/readme.md#L12)表明添加一个`?cache=no`可能是个好主意，因为 GitHubs Camo [缓存策略](http://forum.plantuml.net/7163/githubs-aggressive-caching-prevent-diagrams-updated-markdown)会阻止你的图像在你改变源代码时更新。

这种方法的缺点是，即使您浏览的是旧版本，它也总是在您的存储库中呈现最新的 commmit。如果在使用与 GitHub 的集成时，浏览旧版本对您来说是一个强烈的需求，那么您可能需要构建自己的插件/渲染器或优化本地开发环境，因为毕竟正确的图版本将总是与您签出的源代码在一起。

要使用代理服务集成，只需使用:

```
![cached image](http://www.plantuml.com/plantuml/proxy?src=https://raw.github.com/plantuml/plantuml-server/master/src/main/webapp/resource/test2diagrams.txt)

![uncached image](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/plantuml/plantuml-server/master/src/main/webapp/resource/test2diagrams.txt) 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

保存 PlantUML 图有两种基本方法

1.  嵌入到降价中
2.  保存为单独的`.puml`文件

根据您的工具，其中之一应该是您在存储库中处理图的首选。强烈建议将图尽可能地靠近代码，不要创建人工的文档库。

这篇文章讲述了如何使用 **Docker** 容器在 **VS 代码**中本地编写和呈现文件，以及如何集成到 **GitLab on prem** 以及 publich **GitHub** 和 **GitLab** 实例中。

关于 PlantUML 还有很多要讲的，但是我希望这篇文章给了你足够的信息，让你可以在你正在使用的任何平台上开始。我推荐这个 [PlantUML 备忘单](http://anoff.io/blog/img/puml-cheatsheet.pdf)，它将帮助你覆盖更广泛的用例。

在 Twitter 上告诉我您使用 PlantUML 或替代集成的经历🐦