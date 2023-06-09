# 使用 CircleCI 将 Vue.js 网站部署到亚马逊 S3

> 原文：<https://dev.to/rossta/deploying-a-vuejs-website-to-amazon-s3-with-circleci--5hh7>

在这篇文章中，我们将演练我如何为我的 [Vue.js 静态网站](http://connect-four-vue-abcdefg.s3-website-us-west-2.amazonaws.com/)设置持续部署。每当我`git push`到我的存储库的主要分支时，一个自动化的过程将构建项目并将任何新文件上传到带有适当缓存头的亚马逊 S3。

*这篇文章是正在进行的关于[building Connect Four with vue . js 和 Phoenix](https://rossta.net/series/connect-four.html) 系列文章的一部分。*

以下是相关工具的概述:

*   Github(或类似的)帐户
*   `vue-cli`
*   AWS 帐户
*   一个 S3 桶设置为主机静态网站
*   读取和写入 S3 桶的 AWS 凭据
*   CircleCI 账户
*   被配置为构建和部署站点的`circle.yml`

### 从 CodePen 到 Github

由于到目前为止我的 Connect Four 版本是完全在 CodePen 上构建和托管的，我的第一步是将源代码转移到 Github。我使用 Webpack 模板初始化了一个新的`vue-cli`(版本 2)。

```
$ yarn global install vue-cli
$ vue init webpack connect-four-vue 
```

随着游戏的运行，现在有了版本控制([源代码](https://github.com/rossta/connect-four-vue))的支持，我能够捆绑一组适合部署到像 S3:
这样的提供商的静态资产

```
$ yarn run build 
```

对于`vue-cli`版本 2，该命令构建一个索引文件及其到`dist/`目录的相关资产。当我们建立持续部署的构建时，这将是很重要的。

<aside class="callout panel">

尽管在第 3 版中对 vue-cli 有很大的改变，但在撰写本文时，它仍处于 alpha 阶段，文档[仍在 WIP](https://github.com/vuejs/vue-cli/tree/2c61d236d77dfcb22b4560afe4c957ddf45b4337/docs) 中。尽管在 v3 中初始化和构建 Vue 项目的命令可能不同，但是本文中描述的工作流程仍然适用。

</aside>

### 主持人最多

要在亚马逊 S3 上托管，我需要一个有权限的 S3 存储桶，使其内容对公众开放，用于静态网站托管。这可以从 [AWS 控制台](https://aws.amazon.com)完成:

[![](img/fe9e8261beaadacc13fca49bc41a7538.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HemTy-9J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/aws-create-bucket-767a39de.jpg)

在 bucket management 页面的 **Properties** 窗格上，我启用了*静态网站托管*，并输入`index.html`作为索引文档的名称，以匹配 Vue 构建的输出。

[![](img/7239ef036dfbc0afb235b72ac4d674c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--McSb6V-Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/aws-static-website-properties-03528fa5.jpg)

这个屏幕还显示了 S3 存储桶索引页面的公共端点，这是我们在浏览器中导航到我们部署的站点所需要的。端点可能是类似于的东西。

在 **Permissions** 选项卡上，我添加了一个 bucket 策略，为 bucket 中的所有内容提供公共读取权限。

[![](img/2272d2720429256b3b0e18437fe46829.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NtpqOqQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/aws-static-website-policy-10243aae.jpg)

AWS 文档推荐以下静态网站托管政策。如果为您自己的存储桶设置这个，请确保将*资源*字符串中的`bucket-name`替换为您的存储桶名称:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "PublicReadForGetBucketObjects",  "Effect":  "Allow",  "Principal":  "*",  "Action":  "s3:GetObject",  "Resource":  "arn:aws:s3:::bucket-name/*"  }  ]  } 
```

有关在亚马逊 S3 上建立静态网站的更多信息，请查看凯尔·加尔布雷斯的[如何在 S3 主持一个网站并迷失在大海中](https://medium.freecodecamp.org/how-to-host-a-website-on-s3-without-getting-lost-in-the-sea-e2b82aa6cd38)，其中也涉及到成本和自定义域名设置。Connor Leech 写了一篇关于[在亚马逊 S3](https://medium.com/@connorleech/host-a-vue-js-website-on-amazon-s3-for-the-best-hosting-solution-ever-%EF%B8%8F-eee2a28b2506) 上托管一个 Vue.js 网站的不错的文章，我用它作为我的起点。AWS 文档也提供了一个[一般演练](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)。

### 自动为民

对于持续部署，我选择了 CircleCI 1.0，因为我最熟悉它的配置选项，而且它目前是免费的，可以满足我的需求。

随着我的 Github 帐户链接到我的 CircleCI，我从 CircleCI 仪表板的*项目*选项卡将我的 Connect Four Github 项目添加到 CircleCI。

[![](img/1273505105c06e9fc84cd8776f0d3975.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HNgDbZiB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/circle-add-projects-922272d9.jpg)

我还在我的项目中添加了一个`circle.yml`文件，如下所示:

```
machine:
  environment:
    CIRCLE_BUILD_DIR: $HOME/$CIRCLE_PROJECT_REPONAME
    PATH: $PATH:$CIRCLE_BUILD_DIR/bin:${HOME}/${CIRCLE_PROJECT_REPONAME}/node_modules/.bin"

  post:
    - mkdir -p $CIRCLE_BUILD_DIR/bin
  node:
    version: 8.9.1

dependencies:
  pre:
    - go get -v github.com/bep/s3deploy
  override:
    - yarn
  cache_directories:
    - ~/.cache/yarn
    - bin

test:
  override
    - yarn run build

deployment:
  s3up:
    branch: develop
    commands:
      - s3deploy -source=dist/ -region=us-west-2 -bucket=connect-four-abcdefg 
```

上面的配置将在每个`git push`上做几件事:

*   使用`yarn`安装 npm 依赖项，并获取二进制文件`s3deploy`
*   用`yarn run build`构建项目
*   用`s3deploy`将构建文件同步到我的 S3 桶中(仅在`develop`分支上)

注意，`s3deploy`命令接收到一个`-source=dist/`选项，指示只有构建步骤输出的文件才会与 S3 同步。

我喜欢`s3deploy`的简单和速度(是用 Go 写的)。它将只上传新文件或已更改的文件。它还提供了高级配置，通过路由一个单独的`.s3deploy.yml`文件来微调文件集的响应行为。下面是我用来为我的桶中的静态资产添加长期缓存和 gzip 的内容:

```
routes:
    - route: "^.+\\.(js|css|svg|ttf)$"
      #  cache static assets for 20 years
      headers:
         Cache-Control: "max-age=630720000,  no-transform,  public"
      gzip: true
    - route: "^.+\\.(png|jpg)$"
      headers:
         Cache-Control: "max-age=630720000,  no-transform,  public"
      gzip: true
    - route: "^.+\\.(html|xml|json|js)$"
      gzip: true 
```

有关配置选项的更多信息，请参见 [`s3deploy`项目页面](https://github.com/bep/s3deploy)。

### 权限请

快好了！为了给`s3deploy`命令在我的 S3 桶中添加和修改文件的权限，我需要一组链接到另一个 AWS 授权策略的 AWS 凭证。为此，我在 AWS 的 Security Credentials 面板中创建了一个新的 Amazon IAM 用户，用于编程访问。

[![](img/e0a39c3bfa973fb89afa2ae21abe37fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vI_gcYjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/aws-add-user-1-2b33e2f3.jpg)

我使用以下策略将该用户添加到安全组:

```
{  "Version":  "2012-10-17",  "Statement":[  {  "Effect":"Allow",  "Action":[  "s3:ListBucket",  "s3:GetBucketLocation"  ],  "Resource":"arn:aws:s3:::<bucketname>"  },  {  "Effect":"Allow",  "Action":[  "s3:PutObject",  "s3:PutObjectAcl",  "s3:DeleteObject"  ],  "Resource":"arn:aws:s3:::<bucketname>/*"  }  ]  } 
```

注意:不要混淆这个政策和你的静态网站托管政策！

对于我的新 IAM 用户，现在可以使用一组新的凭证、一个 AWS 访问密钥 id 和秘密访问密钥来以编程方式控制我的 S3 存储桶。

[![](img/6d96bb3a921acd9e4ff8c55b78aacb8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rGSIuuf4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/aws-add-user-2-5943f1b3.jpg)

如果您遵循这些步骤，请确保将您的凭据保存在安全的地方。拥有这些凭据的任何人都可以使用我们使用的权限修改您的 S3 存储桶的内容。

<aside class="callout panel">

查看[文档](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)了解更多关于管理 Amazon IAM 用户的信息。

</aside>

我通过 web UI 中的项目级配置页面将这些凭证添加到 CircleCI 中。

[![](img/8779eb2b8ebd1dd5133d07f521667f7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MoMIOhni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rossta.net/asseimg/blog/connect-four/s3/circle-aws-ceee440d.jpg)

这一步使 AWS 凭证对构建环境可用。这比在`circle.yml`文件中以纯文本形式添加凭证更安全。

### 升空！

现在，当我们在我们的主要分支上推送 Github 时，CircleCI 上的构建过程将获取我们的依赖项，捆绑静态资产并将我们的 Vue 代码库编译到`dist/`目录，然后这些代码将被同步到亚马逊 S3。只要构建和同步步骤成功，我们就能确保最新的代码总是在生产环境中运行，而不会受到命令行的干扰。

* * *

你喜欢这个帖子吗？请分享！更好的是，注册我的时事通讯，听听我正在进行的关于[大楼的系列中的新帖子，用 Vue.js 和 Phoenix](///blog/basic-connect-four-with-vuejs.html) 连接四个。

*Tj·霍洛韦丘克在 Unsplash 上拍摄的背景照片*

*本文最初发表于[rossta.net](https://rossta.net/blog/deploying-vue-to-amazon-s3-with-circleci.html)。*