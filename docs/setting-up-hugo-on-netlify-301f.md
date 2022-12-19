# 在 Netlify 上设置 Hugo

> 原文：<https://dev.to/erickgnavar/setting-up-hugo-on-netlify-301f>

Hugo 是一个静态的站点生成器，就像另一个替代品(Nicola，Jekyll 等)一样，允许以纯文本格式编写并生成 html，js，css 文件。

Hugo 使用起来非常简单，因为它是一个简单的二进制文件，名为`hugo`，允许开发和准备要发布的网站。它的一些特点是:

*   生成新网站
*   运行开发服务器
*   生成静态文件
*   为网站生成新页面

所以让我们从头开始建立一个网站，并使用 [Netlify](https://www.netlify.com) 自动发布它

## 创建新站点

首先我们需要安装 hugo。您需要使用您喜欢的包管理器来安装这个二进制文件。在 mac OS 中，您可以使用 Homebrew 通过以下命令安装它:

`brew install hugo`

你可以用`hugo version`检查版本，在我写这篇文章的时候，最新的版本是`0.48`。

现在我们需要生成一个新的空站点，所以我们将使用命令`hugo new site myblog`。一旦命令完成，你将有一个名为`myblog`的新文件夹

新文件夹的结构应该是:

```
├── archetypes
│   └── default.md
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes 
```

现在我们只需要注意一个文件`config.toml`,这个文件包含了新站点的配置。我们可以定义博客的标题、菜单结构、主题参数等。

`config.toml`的内容应该是这样的:

```
baseURL = "http://example.org/"
languageCode = "en-us"
title = "My New Hugo Site" 
```

我们必须将`baseURL`改为`/`以避免断开链接的错误(我们将在后面看到这些可能的错误)。所以结果`config.toml`会是:

```
baseURL = "/"
languageCode = "en-us"
title = "My New Hugo Site" 
```

## 添加主题

现在我们必须安装一个主题。雨果有很多很棒的主题。可以在[雨果主题](https://themes.gohugo.io)中勾选一个。

对于这个例子，我们将使用[美丽的雨果](https://themes.gohugo.io/beautifulhugo/)

我们有两个选项可以将主题包含在新网站中:

*   克隆主题存储库并将其添加到我们的文件夹中。这将复制我们文件夹中的所有文件。
*   使用 git 子模块来创建对主题存储库的引用。这样我们就不需要复制所有的文件。

这次我们将使用第二个选项。

首先，我们需要使用下面的命令`git init`初始化我们的`myblog`文件夹中的 git 存储库。现在我们已经创建了一个 git 存储库。

现在我们必须运行以下命令来添加主题:

```
cd themes
git submodule add https://github.com/halogenica/beautifulhugo.git
cd .. 
```

这将克隆主题存储库，并将其作为子模块添加到我们的存储库中。我们可以看到，现在在`themes`文件夹中有一个`beautifulhugo`文件夹，在`myblog`的根目录下也有一个名为`.gitmodules`的新文件，内容如下:

```
[submodule "themes/beautifulhugo"]
    path = themes/beautifulhugo
    url = https://github.com/halogenica/beautifulhugo.git 
```

现在我们必须告诉 hugo 我们想在新网站中使用这个主题，我们将在`config.toml`文件中添加以下几行:

```
theme = "beautifulhugo" 
```

## 运行开发服务器

为了在发布之前查看新网站的运行情况，我们将使用嵌入式开发服务器。我们用`hugo server -D -p 9000`运行它。

在那之后，我们将得到一个类似这样的输出:

```
| EN
+------------------+----+
  Pages | 7
  Paginator pages | 0
  Non-page files | 0
  Static files | 33
  Processed images | 0
  Aliases | 1
  Sitemaps | 1
  Cleaned | 0

Total in 46 ms
Watching for changes in /Users/erick/Code/hugo/myblog/{content,data,layouts,static,themes}
Watching for config changes in /Users/erick/Code/hugo/myblog/config.toml
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:9000/ (bind address 127.0.0.1)
Press Ctrl+C to stop 
```

现在，我们可以打开浏览器并输入 [http://localhost:9000](http://localhost:9000) ，我们将看到具有所选主题的新站点。

服务器正在等待要编译的更改，所以我们让它继续运行。

## 书写内容

此时，我们没有任何要显示的内容，所以让我们创建一些内容。

Hugo 默认可以渲染 Markdown 和 Org-mode 文件。在这个例子中，我们将使用 markdown 格式创建一个新帖子。

运行`hugo new post/hello-world.md`在`content/post/`中创建一个名为`hello-world.md`的新文件，Hugo 会创建一个新文件，内容如下:

```
---
title: "Hello World"
date: 2018-09-22T15:05:47-05:00
draft: true
--- 
```

Hugo 使用这些行来显示结果文件中内容的细节。

让我们把下面几行加到`hello-world.md`

```
# This is a heading with level 1

## This is a heading with level 2

This is a paragraph

This is some python code

This is a list:

- item 1
- item 2 
```

现在，如果我们转到浏览器，我们会看到主页上有`hello-world.md`的内容摘要

默认情况下，Hugo 会在主页上显示一个由`content/post`创建的帖子列表。现在我们可以进入帖子查看完整内容。

一旦我们完成了这篇文章，就有必要从`hello-world.md`文件中删除`draft: true`，否则当我们发布这个网站时，这个文件就不会显示出来。

## 发布站点

### 将站点上传到远程存储库

我们可以用 Github、Gitlab 或者 Bitbucket 来做到这一点。这些是 Netlify 支持的服务。在这个例子中，我已经将资源库上传到 Github，并且可以在[https://github.com/erickgnavar/hugo-demo-site](https://github.com/erickgnavar/hugo-demo-site)获得。

### 在网络生活中创建账户

现在我们必须在 [Netlify](https://www.netlify.com) 中创建一个帐户，有一个免费的计划，我们可以用它来托管新网站。

### 部署地点

一旦我们将 Netlify 帐户和站点上传到外部存储库中，我们就可以继续进行部署了。

现在我们可以登录并点击“来自 Git 的新站点”开始这个过程。

[![/images/setting-up-hugo-on-netlify/01.png](../Images/1c5c1fc2f11159aeb2dcc99c284e81b1.png "/images/setting-up-hugo-on-netlify/01.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mGe4_giI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/01.png)

然后有 3 个步骤要遵循:

#### 连接到 git 提供者

我们必须使用上传网站的服务登录。

[![/images/setting-up-hugo-on-netlify/02.png](../Images/e13990976858f4b7b945282bb2db2813.png "/images/setting-up-hugo-on-netlify/02.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y0MQbbWa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/02.png)

#### 挑选一个储存库

现在，我们可以访问我们的存储库列表。我们可以搜索网站所在的那个。

[![/images/setting-up-hugo-on-netlify/03.png](../Images/41f40d69804454acec273c17ec109184.png "/images/setting-up-hugo-on-netlify/03.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FLS4wZer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/03.png)

#### 构建选项

一旦我们选择了存储库，我们就可以指定构建选项。Netlify 认识到这个站点是由 Hugo 创建的，所以这些选项已经配置好了。

要继续，我们单击“部署站点”。

[![/images/setting-up-hugo-on-netlify/04.png](../Images/d618f3b6ee1f7d47222c8a7cb4c3ecbf.png "/images/setting-up-hugo-on-netlify/04.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ul0FL5HD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/04.png)

#### 部署结果

Netlify 将提取存储库并使用给定的构建选项编译站点，然后它将生成一个 url 来访问已部署的站点。

[![/images/setting-up-hugo-on-netlify/05.png](../Images/2262e748195655ffbf9fefe556c16da3.png "/images/setting-up-hugo-on-netlify/05.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yjsbl3OL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/05.png)

现在我们可以转到 Netlify 为该站点生成的 url，我们将看到最终的站点。

#### 有用的网络配置

默认情况下，只有当我们将更改推送到主服务器时，Netlify 才会构建站点。我们可以将其更改为“部署设置”，并将“分支部署”选项更改为“全部”，如下图所示:

[![/images/setting-up-hugo-on-netlify/06.png](../Images/2feb21919afde72a67dac8d404c9c915.png "/images/setting-up-hugo-on-netlify/06.png")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1C2Av0mB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erick.navarro.io/images/setting-up-hugo-on-netlify/06.png)

有了这个配置，我们可以将更改推送到不同于`master`的分支，Netlify 将生成一个新的 url 来查看更改。这对于在将更改发布到生产站点之前测试更改非常有用。

还可以配置不同种类的通知(Slack、email 等)来接收部署的结果。