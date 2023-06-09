# 由 Nuxt 和 AWS 支持的静态站点

> 原文：<https://dev.to/leesmith/static-sites-powered-by-nuxt-and-aws-2hp7>

这篇文章最初发表在我的博客上。

[![Nuxt and AWS](img/d433dd55bbff867188f46c34dff89066.png "Static Sites Powered By Nuxt and AWS")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rmDCC4Nd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rd5n1lbv0ww72ht01r90.png)

我想分享一下我是如何建立我的新个人网站和博客的。就静态博客而言，我的目标非常简单。很明显，我不想用 CMS。我想使用静态托管环境，这样我就不用管理服务器了。我还想探索针对前端的技术，因为我花了很多时间使用服务器端语言构建 web 应用程序。现在在静态站点生成领域真的有很多事情正在发生。静态站点已经证明了它们的价值，尤其是在性能和成本方面。从 React 社区开始，Next.js 和 Gatsby 开始流行起来。Hugo 是一个用 go 编写的流行项目，VuePress 刚刚在今年 4 月问世。

所以我最终选择了 [Nuxt](https://nuxtjs.org/) 作为我的静态站点生成器。Nuxt 是一个高层框架，位于 Vue 之上，提供了许多很酷的特性和一些我很欣赏的约定。我关注 Vue 已经有一段时间了，我喜欢我所看到的，所以这也是我选择 Nuxt 的一个原因。我还将部署到 AWS S3，并使用 AWS Cloudfront 作为 CDN，以获得更好的性能和 HTTPS 通信。这个网站是开源的，所以你可以在 [Github](https://github.com/leesmith/leesmith-dot-net) 上随意查看。

## Nuxt

Nuxt 主要受 Next.js 的启发，是在 Vue 中开发通用 JavaScript 应用程序的高级框架。默认情况下，Nuxt 支持服务器端渲染，但也可以配置为 SPA。Nuxt 的重大创新是“预渲染”，它允许你生成你的 web 应用程序，这样它就可以托管在任何流行的静态托管提供商上，如亚马逊 S3 或 Netlify。

开始时，我使用以下命令来创建我的项目:

```
# install vue-cli globally
npm install -g vue-cli

# create a project using a nuxt template
vue init nuxt-community/starter-template leesmith-dot-net

# cd into the project and install dependencies
cd leesmith-dot-net
npm install

# spin up a local dev server on port 3000
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

至此，Nuxt 已经创建了一个基本的项目结构，包括`assets`、`components`、`layouts`、`pages`、`plugins`等。在我的网站上，我建立了一个基本的布局，我写的每一篇文章都在我的布局中。使用 Nuxt 的一个约定是，放在 pages 目录中的每个`.vue`组件文件都生成为自己的 html 文件。在我的 pages 目录中，我有一个 posts 目录，其中包含我站点的每篇博客文章。有了这个约定，您只需做很少的配置工作。

`nuxt.config.js`文件定义了项目范围的设置。有一个部分允许您为`<head>`部分中的元素设置属性。你需要设置语言和元标签等东西。

就布局而言，以下内容足以满足一些非常基本的要求:

```
<template>
  <header>
    <h1>Example Site Title</h1>
  </header>
  <section>
    <nuxt/>
  </section>
</template>

<script>
</script>

<style>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

对于一个基本页面，以下内容就足够了:

```
<template>
  <article><p>Hello World</p></article>
</template>

<script>
</script>

<style>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的开箱即用功能之一是页面过渡，它由 Vue 提供支持。它给了网站一种自然的感觉，而不是点击不同页面时通常的“闪烁”。要控制页面转换的样式，您需要为转换挂钩添加样式，如下所示:

```
.page-enter-active,
.page-leave-active {
  transition: opacity 0.3s;
}

.page-enter,
.page-leave-to {
  opacity: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我选择了布尔玛作为我的 CSS 框架。我还使用了 Fontawesome 图标和 vue-highlightjs 插件。我可能会在以后的文章中进一步详述这些。

除了`nuxt.config.js`中的项目级设置，您还可以在页面级别控制 meta 标签。我希望每个博客帖子(页面)有一个自定义标题。为此，您可以利用 Nuxt 的 API——特别是`head()`方法:

```
// script section of your vue component
<script>
export default {
  head() {
    return {
      title: "Static sites powered by Nuxt and AWS - LeeSmith.net",
      meta: [
        {
          hid: "description",
          name: "description",
          content: "Static sites powered by Nuxt and AWS"
        }
      ]
    };
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这些是 Nuxt 的一些基本知识。从这里您可以生成您的站点:

```
npm run generate 
```

Enter fullscreen mode Exit fullscreen mode

Nuxt 生成一个包含您生成的站点文件的`dist`文件夹。这些是您将推送到静态主机的文件。让我们看看在亚马逊托管时是如何工作的。

## AWS 设置

在 AWS 上进行设置非常简单。他们有易于遵循的整个过程的文件。我不会在这里重新创建他们的文档，但你要做的第一件事是创建你的 S3 桶，并在其上启用静态托管。我假设你已经注册了一个域名，但如果你还没有，我建议你使用 AWS Route 53 进行域名注册和 DNS 管理。

按照 [AWS S3 演练](https://docs.aws.amazon.com/AmazonS3/latest/dev/HostingWebsiteOnS3Setup.html)创建和配置您的 S3 铲斗。

此时，您可以向上推您生成的站点文件，以确保您的 bucket 配置正确。我强烈推荐在使用 AWS 时使用 [awscli](https://aws.amazon.com/cli/) 。通过 web 控制台点击并不是最有效的完成工作的方式。一旦为您的 AWS 帐户安装并配置了 awscli，您应该能够将文件复制到您的 bucket:

```
aws s3 cp dist s3://mybucket --recursive 
```

Enter fullscreen mode Exit fullscreen mode

现在，S3 水桶服务于您的网站，下一件事是通过 AWS Cloudfront 发行版服务您的网站。这使您可以通过使您的网站的静态文件(如 HTML、图像和视频)可从世界各地的数据中心(他们称之为边缘位置)获得来提高性能。有了 AWS Cloudfront，你还可以配置你的网站在 HTTPS 提供服务。速度和安全性对今天的网络至关重要。网站的 HTTPS 几乎成了标准，因为当页面没有使用 HTTPS 时，浏览器会警告你。谷歌搜索算法也有利于速度更快的网站。

遵循 [AWS Cloudfront 演练](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-cloudfront-walkthrough.html)来创建和配置您的 Cloudfront 发行版。

到目前为止，灯塔得分看起来相当不错:

[![Lighthouse](img/8aab9c03de773acdcaae015157f99978.png "Lighthouse score")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NATJBnvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5sgnln5pa14tozy5u0jb.png)

## 部署

我已经编写了一个简单的脚本来自动化部署。该脚本取决于您的 AWS 设置。如果一个文件不存在，它会创建一个`.env`文件，在您填充两个变量(S3·URI 和 Cloudfront 发行版 ID)之后，部署就像运行脚本一样简单。我正在使用 S3 `sync`命令将我的文件推到桶中。`sync`命令使用一个缓存控制选项来设置文件的未来最大年龄值，以及一个删除选项来删除我的 dist 目录中不包含的任何内容。除了将文件同步到桶中，我们还需要使我们的 cloudfront 发行版无效，以便将来访问该站点时可以获得最新的内容。

```
# deploy.sh
if [ ! -f .env ]; then
    echo "# Project environment variables...do not commit this file." >> .env
    echo "AWS_CF_DIST_ID=" >> .env
    echo "AWS_S3_URI=" >> .env
    echo "Please add the necessary values to the .env file."
else
    export $(egrep -v '^#' .env | xargs)
    echo "CF DIST -> $AWS_CF_DIST_ID"
    echo "S3 URI -> $AWS_S3_URI"
    echo "Performing sync..."
    aws s3 sync dist s3://$AWS_S3_URI --cache-control "max-age=31536000" --delete
    echo "Invalidating cloudfront distribution..."
    aws cloudfront create-invalidation --distribution-id $AWS_CF_DIST_ID --paths "/*"
    echo "Deploy complete! 🎉"
fi 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用 CI/CD 服务(如 [Semaphore](https://semaphoreci.com/docs/deploying-to-amazon-s3.html) )进一步自动化您的部署，这样每次推送到您的主分支都会启动一次部署。这与 Netlify 所提供的非常相似。我可以看到，在从事更大的项目时，这是一个很好的特性。

到目前为止，我真的很喜欢玩 Nuxt。如有任何问题/意见，请通过 [Twitter](https://twitter.com/jeremyleesmith) 联系我。

干杯！🍻