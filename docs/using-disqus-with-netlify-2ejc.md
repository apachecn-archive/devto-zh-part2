# 将磁盘与网络一起使用

> 原文：<https://dev.to/youknowfordevs/using-disqus-with-netlify-2ejc>

要在 Netlify 上使用 Disqus，你可以让 Netlify 来做嵌入所需代码片段的工作，或者让你的静态站点生成器(SSG)来承担这项工作。

# Netlify

[![Netlify Logo](img/c5c580fc7d87f421a3fa33d8da2271a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y7zoe1OX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://youknowfordevs.cimg/uploads/netlify.svg)

Netlify 很棒。在[寻找理想的博客平台](https://dev.to/2012/02/24/choosing-a-blogging-platform/)中，它拥有完美的架构。

首先，你在 Markdown 中写下所有的文章，并将它们保存在 Git repo 中，然后使用静态站点生成器(SSG)来构建你的博客。你当然想，你可能会说…新闻在哪里？

第二，你可能习惯于在本地运行你的 SSG，然后把生成的 HTML 推送到 S3 或 CDN 之类的地方，通过把 Netlify 连接到你的 GitHub、GitLab 或 Bitbucket repo，Netlify 会帮你做到这一点。

哈欠…你可以用 GitHub 页面做到这一点，你告诉我。

但是有趣的地方就在这里。Netlify 在其构建过程中构建了一整套功能，可以帮助您管理*生成的*博客，而无需修改回购本身。例如，您可以在博客中添加表单和登录信息。

[![AWS Lambda Logo](img/f5d8fabad09746762807f9cbd6f200f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ifx7xrUT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://youknowfordevs.cimg/uploads/aws-lambda.jpeg)

如果你想让网站超越博客，进入应用领域，你甚至可以添加 AWS Lamda 函数。

# 使用网络代码片段

[![Disqus Logo](img/b4671391f7644af5cfcdb2d683f02425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o3Em4xqr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://youknowfordevs.cimg/uploads/disqus.svg)

在 Netlify 中获得 Disqus 的最简单的方法是创建一个片段。只需将 Disqus 指令中的代码粘贴到[通用嵌入代码](https://help.disqus.com/installation/universal-embed-code)中。

不过，这有两个问题。

首先，Netlify 只支持将代码片段放在结束标签`head`之前或结束标签`body`之前。然而，为了让 Disqus 在我们的博客中看起来更好，它最好放在*页脚*之前。在这个博客(由 Jekyll 生成)的例子中，它就在结束标签`article`之前。

第二个问题是，为了解决[线程分裂和缺少注释的问题](https://help.disqus.com/troubleshooting/use-configuration-variables-to-avoid-split-threads-and-missing-comments)，我们希望能够将页面 URL 和唯一标识符插入到嵌入式脚本中。但是这在 Netlify 代码片段中是不可能的，因为不支持变量替换。

然而，这两个问题都不是阻碍演出的原因；将 Disqus 注释放在页面末尾而不是页脚之前看起来有点奇怪，但并不可怕。我还见过许多网站不愿意将页面 URL 和标识符值放入 Disqus 脚本中——可能是因为它们的网站页面有规范的 URL，并且没有可能导致“线程分裂”问题的歧义。

因此，如果布局或规范的 URL 对你来说都不是问题，那么使用 Netlify snippets 肯定是最好的方法，因为它完全独立于用于生成站点的 SSG。

# 使用 SSG

合并 Disqus 的另一个方法是让你的 SSG 来做这项工作。大多数 SSG 只需要一点点配置就可以让您触发所需代码片段的包含。在 Jekyll 的例子中，向`_config.yml`文件中添加站点特定 Disqus 配置的简称是一个简单的例子。在我的例子中，它看起来像这样:

```
.
.
.
disqus:
  shortname: you-know-for-devs
.
.
. 
```

Jekyll 不会在预览站点中添加正确的代码片段，但在生产模式下生成站点时会添加。这在 Netlify 中很容易做到，只需在您站点的*设置*的*构建环境变量*部分将`JEKYLL_ENV`设置为值`production`:

[![Set JEKYL_ENV in Netlify](img/8aa9f985d2f0b3a0973f6575bd0b1b37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--87vfF_91--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://youknowfordevs.cimg/uploads/set-jekyll_env-netlify.png)

一旦添加了 config 和环境变量，结果是 Jekyll 在`article`标签的末尾嵌入了正确的代码片段(看起来比在`body`的末尾整洁得多),并且页面的 URL 和唯一标识符被添加到用于与 Disqus 后端通信的配置中。

有一件事让我困惑了一会儿，如果在配置中没有设置站点 URL，那么嵌入式脚本就无法与 Disqus 建立连接。这是一个简单的修复；只要确保在`_config.yml` :
中设置`url`

```
.
.
.
url: "http://youknowfordevs.com" # the base hostname & protocol for your site, e.g. http://example.com
.
.
. 
```

就是这样。将您的配置更改提交并推送到您的 repo 中，让 Netlify 施展它的魔法——几秒钟后，您的网站上就有 Disqus 评论了。