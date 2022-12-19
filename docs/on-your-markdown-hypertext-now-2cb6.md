# 在你的降价上，超文本，现在！

> 原文：<https://dev.to/weiji14/on-your-markdown-hypertext-now-2cb6>

*最初发布于[letsjustwander . now . sh](https://letsjustwander.now.sh/on-your-markdown-hypertext-now!/)T3】*

我将[从为什么](https://dev.to/weiji14/developing-behaviours-by-starting-with-why-4n78)开始。

```
 # language: en
    Feature: Free and minimal static blog
      In order to have a long-lasting blog
      As a blogger,
      I want a system that is portable

      Scenario: Blog host goes down
        Given a backup copy of my blog's content
         When my old blog host becomes unviable
         Then I can rehost my blog and content somewhere else 
```

Enter fullscreen mode Exit fullscreen mode

一点背景，我从十几年前十几岁的时候就开始断断续续地写博客了。当我还是个孩子的时候，在主机和域名上花钱是我避之不及的事情，保持免费仍然是我痴迷的事情。

我面临的一个问题是保证我的博客内容的安全，防止博客平台关闭、过时或被公司锁定的平台吸收。我对我的第一篇 Windows Live Spaces 文章存放在哪里只有一个模糊的概念，这些文章以某种我可能永远无法解锁的专有格式存储。我的黄金时期(2000 年代末到 2010 年代初)的备份同样停留在稍微“好”一点的 mysql 数据库中。

大约在 2014 年毕业后(实际上是在智利旅行时)，我用测试版的 [Ghost](https://en.m.wikipedia.org/wiki/Ghost_(blogging_platform)) 重新开始写博客。它是一座桥梁，将我从以前使用的基于 PHP 的内容管理系统连接到类似于“现代”JAMstack 的东西。

但是当 Ghost 1.0 发布时，我并没有费心去移植到新的 API。从技术上来说，这些帖子存储在 JSON 中，如果我想的话，我*可以*解析它。阅读:要沿着记忆的小路走一趟还需要很多努力。

真的，这一切似乎太难维持了。是的，我*可以*付钱，让别人管理主机、升级、备份等等。

但是固执的我认为世界一定会变得更好。天啊，已经 2018 年了！仅仅在云中拥有几千字节或几兆字节来托管一些博客是否太多了？

## **核灾难证明博客**

好吧，我可能夸张了。如果你读到这里，你可能希望我已经找到了解决方案。

我不是网络开发人员，但我会编程，能按照说明操作。下面的博客工作流程不会是 Wordpress WYSISYG 友好的，也不会依赖于一些在未来 5 年内可能存在也可能不存在的 Javascript 框架。

因为我很偏执，应该有**两个**和至少**两个**的方式来做下面管道的任何部分。

[![Writing text, Text to hypertext, Publishing](../Images/46c384bdb6c7a934ccfb209662a30f3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GjVh9R6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yuml.me/diagram/scruffy/class/%255BWriting-text%255D-%253E%255BText-to-hypertext%255D%2C%255BText-to-hypertext%255D-%253E%255BPublishing%255D)

那我们用什么呢？

## 静态明文

一个词-纯文本。

我不会把我的话塞进某个数据库，不管它有多开源。我也不打算把它转换成 JSON、YAML 或其他四个字母的缩写格式。

每个帖子应该**独立**为一个文件。作为最低要求，博客文件夹将有一个索引页和一个存放所有个人文章的文件夹。

*   _ 帖子/
    *   hello-world.md
    *   post-two.md
*   index.md

我在 [markdown](https://en.wikipedia.org/wiki/Markdown) 中写文章，虽然有一百种风格，但我仍然可以在十年后打开一个. md 文件阅读。如果我 10 年前在 markdown 上写了我的帖子，我现在还能读到它。句号。你可以使用一些其他的标记语言(LaTeX 有人吗？)如果你喜欢的话，但是我强烈推荐 markdown。

为了存储一篇文章的所有元数据(例如日期、标题、标签等)，我把它放入一个简短的 YAML frontmatter 中，如下所示:

```
--------
date: 2018-10-31T12:00:00+12:00
title: Hello World
-------- 
```

Enter fullscreen mode Exit fullscreen mode

这遵循了杰基尔前线组织的惯例。但是我们不会用 Jekyll(喘气！)，尽管它*是*一个流行的静态站点生成器。我觉得有太多的样板文件，[太多的文件夹](https://jekyllrb.com/docs/structure/)存放 css 文件、插件等等。

大多数其他[静态现场发电机](https://www.staticgen.com/)也是如此。每个人对什么更好、更快、更时尚都有自己的看法。如果我们想要长寿，受欢迎就毫无意义，还记得地理城市吗？相反，我们要追求一些固执己见的东西。

## 文本到超文本

我们需要这个文本到超文本的步骤的唯一原因是为了让你的博客看起来不像 git 仓库。或者，是的，你可以把你所有的文件上传到一个 FTP 站点，然后就可以收工了。如果是你，跳到下面的发布/托管部分。

今天，我们努力追求极简主义，但我们不会在质量上妥协！

我就直接切入正题，推荐不废话[十一](https://github.com/11ty/eleventy)。或者，直接跳到 B 计划——纯脚本！

### 计划一——使用十一

[在您的终端中安装带有 npm 的 elevent](https://www.11ty.io/docs/getting-started/)，如下所示:

```
 npm install -g @11ty/eleventy 
```

Enter fullscreen mode Exit fullscreen mode

现在转到博客的顶层目录，输入这个:

```
 eleventy 
```

Enter fullscreen mode Exit fullscreen mode

十一号会处理你的。md markdown 文件放入。html，默认情况下保存到 _site 文件夹中。我们的文件夹现在应该看起来像这样:

*   _ 帖子/
    *   hello-world.md
    *   post-two.md
*   _site/
    *   hello-world/index.html
    *   post-two/index.html
    *   index.html
*   index.md

就这些了，真的！好吧，也许[阅读文档](https://www.11ty.io/docs/)或者添加一些[插件](https://www.11ty.io/docs/plugins/)，如果你想要更多配置的话。否则，把你的`_site`文件夹放在某个地方(见下一节)。

### B 计划——使用 pandoc

感谢 [@gypsydave5](https://dev.to/gypsydave5/write-and-deploy-a-super-fast-web-site-in-30-seconds-with-no-framework-4lab) 想出了这个主意！

如果你没有瑞士军刀 pandoc，[安装它](https://pandoc.org/installing)。

```
 sudo apt-get install pandoc #linux
    brew install pandoc         #mac
    choco install pandoc        #windows 
```

Enter fullscreen mode Exit fullscreen mode

我使用 Linux 作为我的系统，所以接下来我将启动我的 bash shell 并输入这个命令:

```
 for POST in _posts/*.md; do pandoc --to=html5 --output=_site/$(basename ${POST%.md}).html --standalone $POST; done 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是一个 for 循环，将`_posts`文件夹中的每个`.md` markdown 文件转换成一个独立的`.html`文件。

与(正确配置的)eleventy 相比，这种方法的警告是它**将**看起来很可怕。没有 CSS 样式，所以你会得到类似于 90 年代网页的东西。

幸运的是 pandoc 确实允许你设置一个 [`--css`](https://pandoc.org/MANUAL.html#options-affecting-specific-writers) 文件或 url 来使它更漂亮(归功于[马丁](https://devilgate.org/blog/2012/07/02/tip-using-pandoc-to-create-truly-standalone-html-files/))。你可以指向你制作的某个`pretty.css`文件，或者像我一样在下面作弊，加入一个 CDN 托管的 css。

```
 for POST in _posts/*.md; do pandoc --to=html5 --css https://cdn.rawgit.com/yegor256/tacit/gh-pages/tacit-css-1.3.1.min.css --output=_site/$(basename ${POST%.md}).html --standalone $POST; done 
```

Enter fullscreen mode Exit fullscreen mode

我用的是上面的[默契 css](https://github.com/yegor256/tacit) ，这是一个无类的 css 框架。这意味着我不会为使用一些前端框架如 Bootstrap 或更新的播放器所带来的固执己见的类名而烦恼。简单地写适当的 HTML5(无论什么适当的方法)就可以了，至少在 HTML6 出来之前是这样。

您可能需要进一步调整这个`pandoc`方法，让它看起来像`eleventy`一样漂亮。完成这一步可能有一百万种方法，所以我会留给你自己去想象。如果你有另一个这样的一行脚本，请在评论中分享它！

现在，让我们直接进入可怕的部分——出版。

## 发布/托管

所以你已经写了你的博客文章(在 markdown 中)，你已经把它转换成漂亮的超文本(html)，现在是时候把它放在某个地方了！再说一次，我的目标是免费的。这并不意味着不容易。

到目前为止，我已经经历了数不清的不可靠的免费主机提供商。我不喜欢的是一顿免费的午餐，从那以后我会胃痛。

我认为出版本身就是一件可怕的事情，尤其是对新作家来说。但是你最不想做的事情就是写一篇博文，然后贴在你的电脑上，盯着你，只有你一个人。这个词值得你的意见，相信我。

你只需轻轻一点手指，就能把它推向世界！当你强调世界会如何看待你的帖子时，你真的想考虑一下主机/SSL-certs/域名/数据库配置是如何工作的吗？不要！

但是，嘿，我们现在使用的是静态文件(大小小于 1MB)，而不是一些庞大的数据库！没有数据库意味着没有时髦的配置。对于静态文件，难道不应该有一个简单的方法来上传我的文件并获得一个 URL 链接吗？

现在是 2018 年，我们有什么？

我将向你展示 [Zeit Now](https://zeit.co/now) 的禅宗般的简单。或者，跳过 B 计划- P2P 托管！

### 现在就计划 A - Zeit

在您的终端中安装带有 npm 的 now-cli ，如下所示:

```
 npm install -g now 
```

Enter fullscreen mode Exit fullscreen mode

现在转到博客的顶层目录，输入这个:

```
 now _site 
```

Enter fullscreen mode Exit fullscreen mode

键入“y”表示“是”进行确认。根据你的文件有多大，可能需要一两秒钟，然后你会得到一个你的博客的网址！看起来像`https://site-abcdefghij.now.sh`的东西

当然，您可能喜欢一个更好的 url，在这种情况下，您可以设置一个[别名](https://zeit.co/docs/features/aliases)，如下所示:

```
 now alias https://site-abcdefghij.now.sh https://example.now.sh 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，这就是现在 Zeit 上简单的静态托管。如果你有钱的话，你甚至可以将它命名为你自己的[自定义域名](https://zeit.co/docs/features/aliases#custom-domains)。我也要对另一个虚拟主机提供商 [Netlify Drop](https://app.netlify.com/drop) 喊一声，把你的`_site`文件夹拉上拉链，拖放到网站上！

恭喜，你成功了！如果你想知道另一种方式，请继续读下去；)

### B- P2P 数据的方案

本指南的学分[。](https://beakerbrowser.com/docs/guides/publish-a-peer-to-peer-website)

[在您的终端中安装带有 npm 的 dat](https://github.com/datproject/dat) ，如下所示:

```
 npm install -g dat 
```

Enter fullscreen mode Exit fullscreen mode

现在转到博客的顶层目录，键入以下内容来共享您的`_site`文件夹中的文件:

```
 dat _site 
```

Enter fullscreen mode Exit fullscreen mode

根据你的文件有多大，可能需要一两秒钟，然后你会得到一个你的博客的网址！看起来像`dat://abcdefghijklmnopqrstuvwxyz1234567890abcdefghijklmnopqrstuvwxyz12`的东西。

等等，`dat://`是什么？那是 P2P 协议部分，你需要一个特殊的浏览器像 [BeakerBrowser](https://beakerbrowser.com) 才能打开。这对你来说可能没问题，或者你可能更喜欢标准的`https://` url。

输入 [hashbase](https://hashbase.io) 。Hashbase 包装你的 dat 并给出一个更好的 url，如`https://example.hashbase.io`。例如，您也可以访问烧杯浏览器网站，网址为[https://Beaker Browser-com . hashbase . io](https://beakerbrowser-com.hashbase.io)。

Hashbase 作为一个超级对等体来托管你的文件，这样你就不用开着电脑了...所以，注册吧，或者通过分叉他们的 [Github repo](https://github.com/beakerbrowser/hashbase) 成为一个主人。

我一直想走自我托管路线，这可能是我迄今为止最接近的一次。也许有一天我会安装一个树莓派迷你服务器，但是现在这个就够了。

## 告诫选择者

我无法预测未来会发生什么。就像我在开头提到的，我断断续续写了十年左右的博客。那时候看起来不错的想法，现在却不尽然了。你需要注意这篇文章中的后见之明偏见。

这个$0 纯文本->超文本转换器->发布/托管管道假设如下:

1.  纯文本编辑器将继续存在(并且可以打开我的 markdown 文件)。也许 UTF-8 不再有效了？也许我最喜欢的 Atom 编辑器会过时？！！

2.  将来我可以把 markdown 转换成 html。十年后 Eleventy 还能工作吗？如果 pandoc 也走了我该怎么办？！！

3.  我现在使用和推荐的主机服务将保持免费。如果 Zeit Now/Github Pages/Netlify/Hashbase 开始收费使用怎么办？dat 协议真的能延续到未来吗？！！

开悟的人可能会注意到每一步都有一个潜在的 C 计划。我不能用任何编辑器编辑纯文本吗？为什么，不能用一些 Python 脚本把 markdown 转换成 html？肯定会有另一家虚拟主机提供商免费提供这项服务吧？

让我指出，这幅画中最薄弱的一环可能是你自己。

> 你在对你的博客文章进行版本控制，对吗？

Git 很棒，我在上面的指南中没有提到它，但是它在后台，默默地确保我的历史是完整的。

我在本指南中还遗漏了一些细节，比如导航链接、制作存档页面等。所以为了更好的衡量，我所有的静态博客模板都托管在 Github [这里](https://github.com/weiji14/letsjustwander)。这是我能做的最可笑的框架，你可以自由地复制或构建这个设计。

当然，使用这个免费的静态博客系统肯定有缺点。有些人认为二级域名不专业(但我可以接受)。还需要额外的努力才能在页面上获得互动内容，比如视频嵌入。

但是，嘿，没有提供 javascript！这意味着没有弹出广告(可能是好事也可能是坏事)。我的网站加载速度也快得惊人，只有几毫秒。

最让我烦恼的是，几个月或几年后，我所有的努力工作都从网上消失了，而且不得不迁移到其他地方，通常是在不方便的时候。这种可移植的设计旨在使最终的迁移尽可能无痛。

目前，这可能是我能想到的最好的了。

```
 echo 'Hello World' > index.md  #write
    eleventy                       #convert
    now _site                      #publish 
```

Enter fullscreen mode Exit fullscreen mode

这就是 2018 年及以后的免费静态博客指南。