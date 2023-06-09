# 我建立了一个自定义的 CMS，因为为什么不呢

> 原文：<https://dev.to/matteojoliveau/i-built-a-custom-cms-because-why-not-gdn>

我的个人网站是用 [Hugo](https://gohugo.io/) 生成的静态网站，效果非常好。它发球快，重量轻，我用的 [Elate 主题](https://themes.gohugo.io/hugo-elate-theme/)简直太棒了。
然而，每当我想从我的投资组合中添加或删除一个项目，或者从我的简历中删除一个工作描述时，我都必须手动编辑配置文件。然后我必须重新编译网站，并将其推回 GitHub，这将触发一个管道来构建一个 Docker 容器，部署在我的服务器和 yadda yadda 上。无聊又不切实际。
于是我开始寻找一些又酷又好维护的 CMSs。WordPress 显然是第一个想到的，但是在为各种网站做了 4 年的编辑后，我真的不能忍受它，加上它与可维护性相反，我不喜欢 PHP。十月看起来是一个很酷的选择，即使它是用 PHP 编写的，至少它使用了 Laravel，这是一个设计良好的框架，但是我在用 Docker 部署它时遇到了麻烦。对于用 Python 写的 **Wagtail** 也是一样，Python 是我喜欢的语言。

所以我放弃了，开始开发我自己的 CMS，根据我的个人需求量身定制。我认为，如果一个好看的、生动的、时尚的首页对于前端开发人员展示他们的技能是至关重要的，那么一个健壮的、构建良好的 web 应用对于后端开发人员来说也是如此。

# 掌声送给 [DevCMS](https://github.com/matteojoliveau/dev-cms) 和它非常原始的名字

这是一个非常简单的应用，CSS 部分用 Ruby on Rails 5.2 和[布尔玛](https://bulma.io)构建(我是后端开发人员，我不擅长样式)。
它使用[administra tive](https://github.com/thoughtbot/administrate)作为其后台界面，并在 S3 使用 ActiveStorage 上传和存储图像(实际上，我拥有一个私人的、兼容 S3 的 [Minio](https://minio.io) 服务器，但这是一样的)。请记住，这个应用程序是在两天内建成的，所以粗糙的边缘仍然存在。

**DevCMS** 围绕三个核心概念构建。

### 项目

对于软件开发人员来说，作品集是至关重要的，它有助于向潜在的招聘者和同事展示你能做些什么。所以项目在这里是一等公民。他们有一个名称，一个简短的描述，一个较长的描述(格式为 HTML，用管理面板中的 WYSIWYG 编辑器编写)，一些标签和一个封面图片。它们可以被“标上星号”,以便在主页上出现更重要的项目，并且它们有一个草稿/已发布的概念，因此它们可以在发布之前被创建和稍后编译。

### 岗位

[![Jobs](img/148831888ad8673079665499455a3be3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xS5ReCUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/egah0zdfaxkk70cznh7n.png) 工作与 LinkedIn 上的工作非常相似，都是在一段时间内在一家公司的一个职位。像项目一样，他们有简短和详细的描述，封面图片，可以起草和发布。它们被呈现在一个漂亮的小时间轴上，从最新的到最老的，这个时间轴是用[的布尔玛扩展](https://wikiki.github.io/components/timeline/)创建的。

~~### Copies~~
~~我想有一些静态页面，比如关于我的和[联系人](https://matteojoliveau/contacts)，但不想硬编码成 HTML。因此，在我致力于实现正确的页面生成时，我添加了一个**副本**的概念，这是一段存储在 Postgres 中的格式化文本，在 HTML 中提供。目前页面仍然是硬编码的，但至少我可以直接从管理面板编辑它们的内容。~~

### 页面

[![Pages](img/b9d9e313fe02413cdb17c6c3506ef80d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_iWe4659--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hqwoim2za0o6c399yg22.png) 
更新 18/08/2018
我添加了对动态页面的基本支持，这样就可以创建和编辑动态页面，而无需访问源代码和执行重新部署。
模板是用[液态语言](https://shopify.github.io/liquid/)编写的，所以安全可控，可以访问发布的项目和作业列表。页面可以自动添加到导航条，它们的顺序由权重系统控制。

此外，一些更小的东西，如页脚的版权年和作者可以通过 **Config** 对象定制，这些对象是存储在数据库中的键值对。

## 对速度的需要

受《实用开发》和这个网站的启发，我希望我的网站同样快速高效。除了标准的 Rails 解决方案，比如提供的 [Turbolinks](https://github.com/turbolinks/turbolinks) 安装之外，我将 JavaScript 的存在保持得非常小(只有 Turbolinks、ActiveStorage 和 mobile burger 按钮处理程序存在)。我还启用了 AWS CloudFront 作为其 CDN 来缓存静态资产，并启用了 CloudFlare 作为额外的缓存层和 DDoS 保护(我过去有过两个，不知道为什么)。
我让[哨兵](https://sentry.io)监控它，以便自动处理和报告异常。如果可以的话，这是一项非常好的服务。

目前，它还不是一个任何人都可以安装的通用框架或应用程序，但如果您想自己尝试，它是很容易定制的。它是在 [GitHub](https://github.com/matteojoliveau/dev-cms) 开源的。
我想在未来使它更加通用和可调，也许通过添加主题支持和真正的管理员定义的静态页面。
我还想添加**技能**的概念，以显示我对各种技术和语言的自信或专业程度，可能会用一些花哨的百分比栏或类似的东西。那么，你怎么看？你是如何建立个人开发者网站的？