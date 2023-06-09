# 如何用 Gatsbyjs 和 AWS 制作一个很棒的博客

> 原文：<https://dev.to/kylegalbraith/how-to-make-an-awesome-blog-using-gatsbyjs-and-aws-33nc>

不用担心，我仍然计划在 dev.to 上发布我的所有博客文章。除了这个令人难以置信的社区，dev.to 的一个最好的特性是能够使用规范的 URL 指向你的原始博客文章。

先不说免责声明，让我们来看看我是如何建立自己的博客[blog.kylegalbraith.com](https://blog.kylegalbraith.com)的，我使用了 GatsbyJS，TailwindCSS，当然还有我的网站托管相关的所有工具，亚马逊网络服务。在这篇文章中，我将涉及以下主题:

*   静态网站博客 Gatsby + TailwindCSS 入门。
*   建立你的第一个博客。
*   实现搜索引擎优化和社会共享的通用功能。
*   加分:配置 AWS 基础设施来托管您的博客。

听起来是个可靠的计划，对吗？让我们开始吧。

### GatsbyJS + TailwindCSS ==牛逼

我之前在博客上发表过关于 TailwindCSS 的文章，内容是关于推出[边做边学简讯](https://blog.kylegalbraith.com/2018/05/27/how-i-launched-a-new-project-in-a-weekend-using-tailwind-css-and-amazon-web-services/)。这是一个奇妙的实用的 CSS 框架，有很多现成的附加功能。

此外，在我的[通过使用 It 学习 AWS 课程](https://www.kylegalbraith.com/learn-aws)中，我们使用 GatsbyJS 创建了一个演示静态网站，然后我们可以用它来学习 AWS 的核心概念，如托管、保护和部署静态网站。

所以为了我的博客，我决定把它们融合在一起。我想要像 Gatsby 一样的静态网站生成器的简单性，并能够使用 TailwindCSS 快速设计它的样式。因此，我创建了一个 [starter(又名样板文件)Gatsby 项目](https://github.com/kylegalbraith/gatsby-starter-tailwind-seo-social)，它展示了使用预先配置了 Tailwind 的 Gatsby 静态网站生成器所需的所有配置。

要开始，你需要安装来自 NPM 的`gatsby-cli`。

```
npm install --global gatsby-cli 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要使用`gatsby-starter-tailwind-seo-social`项目在您选择的目录中创建一个新的 Gatsby 项目。

```
gatsby new kylegalbraith-blog https://github.com/kylegalbraith/gatsby-starter-tailwind-seo-social 
```

Enter fullscreen mode Exit fullscreen mode

这将在当前目录下创建一个新文件夹`kylegalbraith-blog`。这个文件夹中是 Gatsby 站点和 TailwindCSS 的所有样板文件和初始配置。如果我们运行一个快速的`develop`命令，我们可以看到初始站点的样子。

```
cd kylegalbraith-blog
gatsby develop 
```

Enter fullscreen mode Exit fullscreen mode

我们最终应该看到的是类似这样的东西。

[![Tailwind starter project with Gatsby](img/2bdccfde4ac1238558d8c338fbd1db11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JhUX06JR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kylegalbraith/gatsby-starter-tailwind-seo-social/master/gatsby-starter-running.PNG)

跟我到目前为止？非常好。

下拉 starter 项目后，您可以开始在 Visual Studio 代码或您喜欢的 IDE 中打开它。如果你看一下文件夹结构，你会看到一些不同的东西。

[![Folder structure](img/177288e62c1b64c7d72220fc09de1dfe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qV06zXYE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2rae4ye952o7fng734gp.PNG)

首先要熟悉的是`src`目录。这是组成你的博客的所有代码所在的地方。GatsbyJS 是一个基于 React 的静态网站生成器，所以一切都是根据组件、静态资产、布局和页面来定义的。

如果您展开 components 文件夹并打开`Header`组件，您会看到如下所示的代码。

```
import React from "react";
import Link from "gatsby-link";
import logo from "../img/favicon.png";
import config from "../../config/config";

const Header = () => {
  return (
    <nav className="bg-grey-lightest">
      <div className="container mx-auto p-4 md:p-8">
        <div className="text-center lg:text-left">
          <Link to="/" className="lg:inline-flex items-center no-underline text-grey-darkest hover:text-black">
            <div className="mb-4 flex-1 pt-5">
              <img
                src={logo}
                alt="The blog of Kyle Galbraith, Software Engineer & Entrepreneur"
                className="w-24 h-24 border-indigo-darkest mr-4 rounded-full" />
            </div>
            <div className="flex-2">
              <h1 className="text-5xl ml-2 font-hairline text-indigo-darkest">
                {config.authorName}
              </h1>
              <span className="block ml-2 mt-2 font-hairline text-indigo-darkest">
                {config.siteDescription}
              </span>
            </div>
          </Link>
        </div>
      </div>
    </nav>
  );
};

export default Header; 
```

Enter fullscreen mode Exit fullscreen mode

这是 Gatsby 博客的头组件。现在这仍然是一个样板博客。让我们通过改变`src/config/config.js`中的一些配置设置来增加趣味。您可以更新`authorName`和`siteDescription`来匹配您的信息。

```
module.exports = {
    siteTitle: "Your Blog Title",
    shortSiteTitle: "Your Short Blog Title",
    siteDescription: "This is an awesome blog that you are going to make your own.",
    siteUrl: "https://blog.kylegalbraith.com",
    pathPrefix: "",
    siteImage: "images/facebook-cover.jpg",
    siteLanguage: "en",
    authorName: "Kyle Galbraith Was Here",
    authorTwitterAccount: "kylegalbraith",
    authorSocialLinks: [
      { name: "github", url: "https://github.com/kylegalbraith" },
      { name: "twitter", url: "https://twitter.com/kylegalbraith" },
      { name: "facebook", url: "http://facebook.com/kyle.galbraith459" }
    ]
  }; 
```

Enter fullscreen mode Exit fullscreen mode

现在这些字段已经更新了，您可以通过从命令行再次运行`gatsby develop`在浏览器中实时检查更改。默认情况下，该命令在端口`8000`启动一个本地主机服务器。

[![gatsby develop from the command line](img/4eb11a9061321ade6a4192b3ccd46032.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BAK3Bt51--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdv1ao6tjnnviilfftc4.gif)

然后，您可以在浏览器中查看您的更改。如果继续运行`develop`命令，对组件所做的任何更改都将在浏览器中热重新加载。

[![Change starter to your own details](img/58a9d8bc4de547ee8f4a5253de1c28db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1PakIMDW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1umt1p7nkij85kgtie6x.PNG)

很酷吧？您可以更改任何配置设置以匹配您的博客详细信息，组件将自动更新。

改变内容很酷，但是你可能也想加入你自己的风格。转到`Footer`组件，让我们将外部 div 的背景色从`bg-grey-lightest`改为`bg-indigo`。

```
import React from "react";
import config from "../../config/config";

const Footer = () => (
  <div className="bg-indigo">
    <div className="text-center max-w-xl mx-auto p-4 md:p-8 text-sm">
      <p>
        <a
          href={config.siteUrl}
          className="no-underline text-indigo-darkest hover:text-grey-darkest"
        >
          This blog is powered by <a href="https://www.gatsbyjs.org/">GatsbyJS</a> using the gatsby-starter-tailwind-seo-social from <a href="https://blog.kylegalbraith.com">Kyle Galbraith</a>.
        </a>
      </p>
    </div>
  </div>
);

export default Footer; 
```

Enter fullscreen mode Exit fullscreen mode

现在你的博客的页脚应该是蓝色的。通过使用 TailwindCSS，您可以使用许多预构建的实用程序类，这些类允许您快速开发新的 UI 组件或更改现有组件的样式。

但是在某些时候，你会想要给一个组件分配你自己的自定义 CSS。这是通过向`src/layouts`下的`index.tailwind.css`添加自定义样式来处理的。滚动到底部，你可以看到已经为`body`元素定义了一个自定义样式来添加背景渐变。我们把渐变改成别的吧。

```
body {
    background: #1f4037;
    background: -webkit-linear-gradient(to right, #99f2c8, #1f4037);
    background: linear-gradient(to right, #99f2c8, #1f4037);
} 
```

Enter fullscreen mode Exit fullscreen mode

要更新样式表，您需要从`package.json`运行一个 npm 脚本。`build:css`脚本将运行`tailwind`命令并输出最终的 CSS。

```
npm run-script build:css
...
> tailwind build ./src/layouts/index.tailwind.css -c ./tailwind.config.js -o ./src/layouts/index.css

Building Tailwind!
Finished building Tailwind! 
```

Enter fullscreen mode Exit fullscreen mode

现在再次检查 localhost，你可以看到背景渐变已经更新。

[![New Gatsby background](img/8700f81c3338b97fe98175d00a0b6173.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LRB1Jh-R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1l1pixpf6smxjr7ew23m.PNG)

这是 Gatsby + TailwindCSS 博客设置的样板文件。您可以利用现有的 [Tailwind 实用程序类](https://tailwindcss.com/docs/colors)或者添加并扩展您自己的类来进一步设计博客。您也可以构建自己的组件来为您的博客添加新功能。

### 设置实际的博客文章

Gatsby 是一个非常简单的博客平台，允许你在 Markdown 中写博客。正如您从样板文件开始看到的，已经创建了一篇博客文章。如果你点击这篇博客文章，你可以看到一篇充满美味培根 ipsum 的博客文章。

如果你看一下这篇博文的 url，你应该会看到下面的格式，`2018/08/01/a-sample-gatsby-plus-tailwind-blog-post/`。这是由`pages`目录下的文件夹结构定义的。

[![Pages directory](img/9ec88ec1d52ea0cea90eb14a5a2a4d5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--af6ULKp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l68lds75nb5j37s8aykr.PNG)

博客文章写在 markdown 文件夹中，`index.md`，图片是你在文章顶部看到的封面图片。这也是在脸书和推特上分享时将使用的图片。

但是 markdown 帖子是怎么变成 HTML 帖子的呢？

[![Gatsby magic](img/2efaffd0a4dc0a7542a12480470954e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ecmCBgUr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ko2e7x90de1siumh8tcy.gif)

好吧，不尽然。它实际上是由位于`gatsby-config.js`中的两个插件处理的，分别叫做`gatsby-source-filesystem`和`gatsby-transformer-remark`。第一个从`pages`目录中加载文件，并将它们送入转换器，转换器将 markdown 语法转换成适当的 HTML。

您可以通过在`08`目录下创建一个新目录并初始化一个新的 markdown 文件来创建一篇新的博客文章。

```
mkdir pages\2018\08\02\new-post
touch pages\2018\08\02\new-post\index.md 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以向新的降价文件中添加一些新内容。

```
--------
title: This is a new post
date: "2018-08-02"
cover: ""
-------- A brand new blog post from here. 
```

Enter fullscreen mode Exit fullscreen mode

如果你刷新你的本地博客，你应该看到你有一个新的博客文章，标题来自你的 markdown 文件。

放松点，对吧？

现在，您已经知道如何使用 Gatsby 快速开发您的新博客，并使用 Tailwind 将其风格化以满足您的需求，让我们来探索这个入门项目中内置的 SEO 和社交共享组件。

### SEO 和社交分享

如果你正在努力在你的博客上写内容，你要确保你把它交到那些认为有用的人手中。这可以通过优化你的文章的搜索引擎优化，让其他读者更容易分享你的内容来实现。

幸运的是，这是盖茨比项目的一部分。

在`templates`目录下看一看，你可以检查出`blog-post.js`文件。这是定义单个博客文章如何出现在您的博客上的模板。

```
return (
      <div className="text-left p-4 bg-grey-lightest shadow-lg">
        <Seo data={post} />
        {
          post.frontmatter.cover &&
          <Img sizes={post.frontmatter.cover.childImageSharp.sizes} alt={post.frontmatter.title} className="w-full" />
        }
        <h1 className="text-3xl lg:text-5xl text-indigo-darker font-normal mt-6 mb-2">
          {post.frontmatter.title}
        </h1>
        <p className="block mb-8 pb-4 border-b-2">
          📅 {post.frontmatter.date} – {config.authorName}
        </p>
        <div className="blog-content" dangerouslySetInnerHTML={{ __html: post.html }} />
        <div className="mt-16 pt-8 social-content text-center border-t">
          <p className="font-light">Did you enjoy this post? Share the ❤️ with others.</p>
          <Social url={url} title={post.frontmatter.title} />
        </div> 
        <ul
          className="mt-8 border-t-2 pt-4"
          style={{
            display: 'flex',
            flexWrap: 'wrap',
            justifyContent: 'space-between',
            listStyle: 'none',
            paddingLeft: 0
          }}
        >
          <li>
            {
              previous &&
              <Link to={previous.fields.slug} rel="prev" className="text-indigo-darker hover:text-indigo-lighter">
                ← {previous.frontmatter.title}
              </Link>
            }
          </li>
          <li>
            {
              next &&
              <Link to={next.fields.slug} rel="next" className="text-indigo-darker hover:text-indigo-lighter">
                {next.frontmatter.title} →
              </Link>
            }
          </li>
        </ul>
      </div>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

查看返回的 HTML 模板，可以看到使用了两个定制组件`Seo`和`Social`。那么他们到底在做什么呢？

如果你看一下`Seo`组件，你可以看到它正在返回一个 React 头盔组件。

```
 <Helmet
            htmlAttributes={{
                lang: config.siteLanguage,
                prefix: "og: http://ogp.me/ns#"
            }}
        >
            {title}
            <meta name="description" content={description} />
            <link rel="shortcut icon" href={favicon} />

            <meta property="og:url" content={url} />
            <meta property="og:title" content={title} />
            <meta property="og:description" content={description} />
            <meta property="og:image" content={image} />
            <meta property="og:type" content="website" />

            <meta name="twitter:card" content="summary" />
            <meta name="twitter:image" content={image} />
            <meta name="twitter:description" content={description} />
            <meta
                name="twitter:creator"
                content={config.authorTwitterAccount ? config.authorTwitterAccount : ""}
            />
        </Helmet> 
```

Enter fullscreen mode Exit fullscreen mode

该组件获取一篇博客文章，并返回必要的 HTML 作为标题、描述和 favicon。对 SEO 非常重要的标签。它还为脸书、`og:url`和 Twitter `twitter:description`返回必要的元标签。你的新盖茨比博客中的每一篇博文都会通过使用你帖子中的内容自动获得这种优化。

但是，您也希望您的内容易于共享。所以让我们看看`Social`组件给每篇博客文章添加了什么。

```
 <ul className="list-reset inline-flex">
            <li className="p-4">
                <TwitterShareButton
                    url={url}
                    title={tweet}
                    className="button">
                    <TwitterIcon
                        size={32}
                        round={true} />
                </TwitterShareButton>
            </li>
            <li className="p-4">
                <FacebookShareButton
                    url={url}
                    quote={title}
                    className="button">
                    <FacebookIcon
                        size={32}
                        round={true} />
                </FacebookShareButton>
            </li>
        </ul> 
```

Enter fullscreen mode Exit fullscreen mode

这里的`react-share`组件被用来创建 Twitter 和脸书分享按钮。每一个都是使用博客文章的标题和 url 预先填充的，这样当用户点击它们时，它们就有了准备发布的内容。

### 奖励积分:配置 AWS 基础设施来托管您的博客

如果你想开始学习亚马逊网络服务，那么这个额外的部分就是为你准备的。

这篇文章的这一部分假设你已经有了一个 AWS 帐户设置，并且对这个平台有一个初步的了解。如果 AWS 对你来说是全新的，[考虑一下我的 learn AWS 课程包，它主要是通过实际使用来教你这个平台](https://www.kylegalbraith.com/learn-aws)。在我的课程中，我们重点学习核心 AWS 服务，如 S3、CloudFront、Lambda 和 API Gateway，实际使用它们来托管、保护和交付静态网站。

起始项目中包含一个`deployment`文件夹。在这个文件夹中，我包含了一个 Terraform 模板，它配置 AWS 资源来托管您的博客。该模板在您的 AWS 帐户中提供以下资源。

*   为静态网站托管配置的 S3 存储桶。存储桶的名称必须与您博客的 url 相匹配。例如，我的博客位于`blog.kylegalbraith.com`，因此这个桶被命名为`blog.kylegalbraith.com`。
*   一个 CloudFront CDN 发行版，位于你的 S3 网站桶的前面。它还被配置为通过您传入的 ACM 证书使用 SSL。如果您不熟悉在 AWS 中向静态网站添加 SSL，请查看这篇博文。

那么，实际上如何部署这个基础设施呢？问得好。以下是为您的博客部署 AWS 基础设施应该遵循的步骤。

1.  确保您安装了 [AWS CLI](https://aws.amazon.com/cli/) 并配置为与您的 AWS 帐户交互。
2.  安装[地形](https://www.terraform.io/)并将其添加到您的`PATH`中，这样您就可以在任何地方执行它。
3.  现在，您可以从`deployment`目录中初始化 Terraform 模板。

```
cd deployment
terraform init
...
Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.30.0)... 
```

Enter fullscreen mode Exit fullscreen mode

*   初始化提供者后，您可以运行`terraform plan`来可视化将要创建的资源。您可以通过`-var`标志将必要的变量从`variables.tf`传递到`plan`命令，如下所示。

```
terraform plan \
    -var blog_url=blog.yourcoolsite.com \
    -var acm_certificate_arn=arn:aws:acm:us-east-    
1:yourAccountId:certificate/yourCert
...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

-----------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
    Terraform will perform the following actions:
      + aws_cloudfront_distribution.blog_distribution 
```

Enter fullscreen mode Exit fullscreen mode

*   `plan`方法告诉您将要供应什么资源。要启动供应，您必须运行`terraform apply`，传递与之前相同的变量。

```
terraform apply \
    -var blog_url=blog.yourcoolsite.com \
    -var acm_certificate_arn=arn:aws:acm:us-east-:yourAccountId:certificate/yourCert
...
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes aws_s3_bucket.blog: Creating... 
```

Enter fullscreen mode Exit fullscreen mode

*   在创建 S3 bucket 和 CloudFront 发行版时，`apply`命令需要几分钟才能完成。如果您想跳过上面看到的批准步骤，将`-auto-approve`标志传递给`apply`命令。
*   一旦`apply`命令完成，你将拥有一个全新的 CloudFront 发行版，它配置了 S3 网站桶作为你的博客的源。[下一步是更新您的 DNS 记录，以便将您的博客流量路由到 CloudFront 分布](https://medium.freecodecamp.org/how-to-make-use-of-cloudfront-for-secure-delivery-of-static-websites-to-the-world-d2f54e8b096)。

配置好 AWS 基础设施后，您现在可以将 Gatsby 博客部署到您的 S3 桶中。这是通过在`package.json`中运行`build`脚本，然后从 AWS CLI 运行 S3 拷贝命令来完成的。

```
npm run-script build
aws s3 cp public/ "s3://blog.yourcoolsite.com/" --recursive 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本运行产生最终 TailwindCSS 的`build:css`配置。然后它运行`gatsby build`,生成一个生产版本，并将内容输出到`public`目录中。从那里开始，你只需要将目录中的内容复制到你的博客所在的 S3 桶中。

### 结论

我更喜欢尽可能没有摩擦的过程。当过程繁琐且非常手工化时，我会变得无所事事，因为这通常意味着在没有价值的事情上花费时间。一天只有 24 小时，因此在繁琐的手动过程中浪费时间是不理想的。

在过去，创建一个博客在我的脑海中一直有这种感觉。我的旅程是从写 raw HTML 开始的，并不好玩。然后出现了 WordPress 之类的东西，虽然更好，但仍然很慢，而且开销很大。最后，我转到了像 [dev.to](https://dev.to/kylegalbraith) 和 [Medium](https://medium.com/@kyle.galbraith) 这样的平台，这太棒了，因为它简化了创作过程，让我可以专注于内容。

但是，我仍然需要展示我拥有的内容。盖茨比解决了这个问题，一边做一边踢屁股。那边的[人](https://www.gatsbyjs.org/)已经创建了一个伟大的开源项目，拥有一个强大而充满活力的社区。

希望你已经看到了使用 Gatsby 和 Tailwind 这样的工具创建并运行一个博客是多么容易。一旦你创建了一些东西，你就可以把它部署到 AWS，就像你在这里看到的，或者任何其他静态网站的托管平台。

如果你在阅读这篇文章时有任何问题或遇到困难，请随时给我留言。

感谢阅读！

#### PS:你渴望了解亚马逊 Web 服务吗？

想了解更多关于 AWS 的知识？我最近发布了一个电子书和视频课程，穿越了信息的海洋。它着重于在 AWS 上托管、保护和部署静态网站。目标是在您使用服务时了解与此问题相关的服务。如果你一直想学习 AWS，但不知道从哪里开始，那么看看我的课程。