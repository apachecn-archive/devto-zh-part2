# 在 5 分钟内搭建和部署一个 Jekyll GitHub Pages 博客

> 原文：<https://dev.to/jonesrussell/scaffold-and-deploy-a-jekyll-github-pages-blog-in-5-minutes-3ln3>

Ahnee！静态网站卷土重来。内容生成方面的创新、工作流中 Markdown 的采用、部署技术和免费托管使静态网站成为那些不需要框架或内容管理系统功能的人的一个有吸引力的选择。

Jekyll 是一个静态网站生成器，在静态网站生成领域引起了巨大的轰动。随着 [GitHub Pages](https://pages.github.com/) 成为免费静态网站托管的一个有吸引力的选项，GitHub 已经成为事实上的社交编码标准。

> Jekyll 是 Ruby 中一个支持博客的静态站点生成器

### 我的静中体验

18 年前，我的第一份工作是网站开发，我们制作和维护静态网站。我工作的商店构建了一个内部 Perl 模板“引擎”,它递归地搜索一个目录，寻找带有自定义文件扩展名的文件进行解析。

这些文件包含 HTML 和 XML 风格标签形式的内容，这些标签本质上是变量，用于设置导航中的活动部分，帮助生成面包屑，确定在侧栏中显示哪些共享内容块，等等。每个文件的核心是生成的页面的内容。使用 regex，主模板的内容被解析文件的内容填充，然后输出到 html 文件中。

网站通常有 100 多页，引擎也不是很好。没有简单的方法来生成单个页面或页面子集。而且很慢。在进行维护工作时，我们不可避免地会直接编辑 HTML 文件。站点范围内的更改最终变成了一个可怕的独立于引擎运行的 Perl regexp，我们的主模板很快就会过时。

今天我们有更好的工具。Jekyll 用 Ruby 编写，可以说是最流行的静态站点生成器。

### 为什么要开发静态网站？

为什么真的。我能想到一些创建和维护静态网站的好处。

*   **无需服务器端编程**创建一个网站通常从一个框架开始。当今最流行的创建网站的软件是 Wordpress。WP 是用 PHP 编写的，最初是一个博客平台，但后来发展成为一个成熟的内容管理系统。一个 WordPress 网站需要一个支持 PHP 的服务器，你可以自己安装或者付费给像 https://wordpress.com/的[这样的主机提供商。在不了解 PHP 的情况下创建一个 WordPress 网站是完全可能的，但是要定制你的网站，你最终将不得不亲自动手编写一些代码。](https://wordpress.com/)
*   你的网站会很快静态网页是用 HTML 编写的，由 Apache 或 Nginx 等 web 服务器提供服务。因为 HTML 文档不需要任何额外的处理，所以它被直接提供给浏览器，而 web 服务器只需做最少的工作。像 Nginx 这样的 Web 服务器是为服务静态资产而优化的，结果网页几乎是瞬间加载的。
*   **搜索引擎青睐快速网页**谷歌为我们提供了搜索引擎优化的指南。2012 年，谷歌透露速度是排名的一个因素。

### 教程

说得够多了，让我们开始创建我们的新网站吧。

#### 先决条件

*   终端(一个命令行)——[如何在 Ubuntu Bionic Beaver 18.04 Linux 上打开终端](https://linuxconfig.org/how-to-open-a-terminal-on-ubuntu-bionic-beaver-18-04-linux)
*   ruby gems—[Ubuntu 的 RVM 软件包](https://github.com/rvm/ubuntu_rvm)
*   Git — [如何在 Ubuntu 18.04 上安装 Git](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-18-04)
*   GitHub 帐户— [加入 GitHub](https://github.com/join)

安装“gem”和“git”超出了本教程的范围，但是上面提供的链接对于 Ubuntu 18.04 非常有用。如果你需要帮助，请给我留言或评论。

#### 创建并预览您的站点

首先，您需要打开一个终端并安装 Jekyll ruby gem:

> $ gem 安装 bundler jekyll

[![](img/e698f40bf2284bb02c6bf14ca7a42517.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3vO1dfzW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2Ao-WMlkQ2Cra6pT0O8HSeVQ.png)

使用 jekyll 生成您的新网站:

> $ jekyll 新的我的-真棒-网站

[![](img/972fe124b5cff5da549c0aadc5c05fe3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---yh5K7Q6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2AfphO0ihPGqX8UZ8B_P-pdg.png)

如果一切顺利，你可以使用 jekyll 预览你的新网站:

> $ CD my-awesome-site
> 
> $ bundle exec Jekyll serve

[![](img/1a74daab71048156b5a95c26c767c705.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hxGoParV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2ATGha9u_EC39odTSBF4nETQ.png)

> 将浏览器指向 [http://localhost:4000](http://localhost:4000) 。

[![](img/28b04e2fa24c55289815fefb52c30446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xHamMyCj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aiaupqu3_PhnF1iRCDlYZng.png)

> 完成预览后，按 Ctrl-C 关闭预览服务器。

### 部署到 GitHub 页面

GitHub Pages 是一款优秀的免费网站托管服务，适用于你的 GitHub 仓库。

> 你和你的项目的网站。
> 
> 直接从你的 GitHub 库托管。只需编辑、推送，您的更改就生效了。

#### 创建新的存储库

存储库(或简称为 repo)是管理项目和跟踪项目文件随时间变化的地方。

> 参观[https://github.com/new](https://github.com/new)
> 
> 在“存储库名称”下输入“我的网站”
> 
> 可选地填写“描述”:

[![](img/351b55fb3f7f5f7d937a4474d56adc2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mNIhJ5pB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2An_waIDZ75IoaJxPdNvD0EA.png)

保持“公开”选中(“私人”回购仅适用于付费 GitHub 计划)，并保持所有其他选项不变。

> 要继续，请单击“创建存储库”。创建回购后，您将看到以下屏幕:

[![](img/8d052a1ad8605d194f64d80084192b11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xiYUdfY6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFibe17D9cKelwO2amQP0ag.png)

您现在有一个空的 repo，可以继续配置。

#### 为 GitHub 页面配置存储库

要将您的存储库链接到 GitHub 页面，您必须指定要发布哪个分支。

一个 [git 分支](https://git-scm.com/book/en/v1/Git-Branching-What-a-Branch-Is)本质上是你的代码随时间的一系列快照。分支允许您或多个用户在隔离的工作区域中修改代码，而不会影响其他分支或整个项目。

出于本教程的目的，我们将指定要发布的“主”分支。

> 从你的“我的牛逼网站”存储页面点击“设置”标签，这将把你带到[https://github.com/*用户名*/我的牛逼网站/设置](https://github.com/jonesrussell/my-awesome-site/settings)。
> 
> 向下滚动到“GitHub Pages ”,您将看到“Source ”,下拉菜单设置为“None”。
> 
> 单击下拉菜单并选择“主分支机构”:

[![](img/a4eb264c2a076b1b5f241adaaf63ac22.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u_2Dn4e0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuGIFFtwSc7L0-QnmKAXORA.png)

> 选择“总分行”后，点击“保存”。

保存后，向下滚动以查看消息“您的网站已准备好在 [https:// *用户名*. github . io/my-awesome-site/](https://jonesrussell.github.io/my-awesome-site/)发布。”

[![](img/61234c601abbe19ec1c9fcfe9dfc7980.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bQi3hxs6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMQwnvC6Inb93zlev6ZuYRQ.png)

**配置您的 gem 文件**

Ruby 使用 Gem 文件来设置正确构建 Ruby Gem 所需的依赖关系。在前面的步骤中，Jekyll 自动生成了一个 Gemfile。

您必须更新您的 gem 文件，以指示 GitHub 构建您的 gem 来部署到 GitHub 页面。

使用你觉得舒服的编辑器，但是这里我使用的是“vim”:

> $ vim Gemfile

[![](img/e7b24ac9f36cbf80974cadf5fef27998.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gKMBtG0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2ABh6_5s7-bqE7CAfAK-HyNw.png)

我们的更改非常简单，只需注释掉(在该行后面加上#):

> 宝石“jekyll”，“~> 3.8.4”(上面截图中第 11 行；3.8.4 是撰写本文时的版本，现在可能已经更改)

和取消评论:

> # 宝石" github-pages "，group: :jekyll_plugins(第 18 行)

您的 Gemfile 现在应该看起来像下面的截图。第 11 行附加了一个' # '，第 18 行开头的' # '已被删除。

[![](img/241d536e0a8302e224fbc2bc763721b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iN7nV1QD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2A9mMkQCJzxLCmXL_I5yfm1g.png)

现在保存并关闭 gem 文件(vim 的说明):

> :wq

**Update _config.yml**

_config.yml 包含特定于您的网站的变量。在部署之前，您可能需要设置标题和描述，可能还需要设置您的 Twitter 和 GitHub 用户名。

您必须设置 baseurl 以确保正确生成 url，否则您的资产和页面链接将不起作用。

> $ vim _config.yml

[![](img/60b32cc31571b721a3f87e75a027b80b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eXuX1zs3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2A9UEZoWCCdQYb4_ah0aXFKw.png)

进行您喜欢的任何编辑，但是一定要编辑“baseurl”(第 22 行),使其显示为:

> baseurl: "/my-awesome-site" #您的站点的子路径，例如/blog

[![](img/b7abf371b8857677f8a77a356751e7d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GHLKJd5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2Awku2TAwAraDwe4GCoxINHg.png)

保存并关闭。

> :wq

**提交您的站点文件**

您有了为 GitHub 页面配置的站点文件，还有一个名为“my-awesome-site”的空 GitHub 存储库。

下一步是将您的文件“签入”到 repo(用您的 GitHub 用户名替换*用户名*):

> $ git init
> 
> $ git add。
> 
> $ git commit -m“第一次提交”
> 
> $ git 远程添加原点[https://github.com/username/my-awesome-site.git](https://github.com/jonesrussell/my-awesome-site.git)
> 
> $ git push-u 原点主

[![](img/308a609732d5d093dd415771d68c9e7a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QmtUF21f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/741/1%2AXFr7LjpYyynfAapn9UnKlw.png)

> 通过访问[https://github.com/*用户名* /my-awesome-site](https://github.com/jonesrussell/my-awesome-site) 验证您的文件已经发布到 GitHub:

[![](img/fc686a7c434d4eb13a57c688fb1d6473.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oKjeNxz7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A550RFFagQa4dtiXMZkZfdw.png)

**Tada！访问您的网站**

当您将文件提交到存储库时，GitHub 会自动构建一组 HTML 文件并部署到 GitHub 页面。

> 通过访问[https://username.github.io/my-awesome-site/](https://jonesrussell.github.io/my-awesome-site/)验证您的博客是否已部署。

[![](img/4560aaa5fee17a056e5bbfd864888434.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9dYiX6O5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AlP_yQX0QSLepJ29IenSFjQ.png)

### 结论

你现在开始进入 Jekyll 和 GitHub 页面的世界。要创建新的博客文章，请向“_posts”目录添加一个新文件，并将其签入到您的 repo 中。请务必检查默认帖子，并坚持它的命名约定和它在其中使用的模板。登录后，访问你的网站，你的新帖会出现在主页列表中。

我希望这篇文章是有帮助的，给我留言或评论任何问题，纠正，或者只是说 Ahnee！

加布卡纳。