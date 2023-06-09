# GitHub 页面迁移第 2 部分

> 原文：<https://dev.to/jeikabu/github-pages-migration-part-2-51ho>

这是我上一篇关于迁移到 Github 页面的文章的后续文章

## 捆绑包和 Gemfiles 上的

就像经常发生的情况一样，几个小时后我发现了我和`bundle`的问题。在寻找其他东西时，我偶然发现了 github-pages gem——显然我第一次设置它时错过了——并被提醒说`Gemfile`在我自己的回购中。当在正确的地方执行时`sudo bundle update`就像预期的那样起作用。

与新技术打交道总是一种令人羞愧的经历。

## 在知识库上

反正升级到 Jekyll 3.7.3 还有…

```
 Liquid Exception: No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository. in /_layouts/default.html

ERROR: YOUR SITE COULD NOT BE BUILT:
       ------------------------------------
       No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository. 
```

Enter fullscreen mode Exit fullscreen mode

这个杰基尔问题和它提到的[公关](https://github.com/jekyll/github-metadata/pull/45)澄清了正在发生的事情。

为了迁移到 github，我添加了另一个远程 git 存储库`github`(我之前的 bitbucket repo 是`origin`)。就像有用的错误消息所说的，我要么需要在`_config.yml`中设置**存储库**，要么名为`origin`的 git remote 需要指向 github.com。

很简单。

这两种方法似乎都有效。但是，我需要[使用两个 github 账户](https://stackoverflow.com/questions/20353564/use-different-ssh-keys-for-different-accounts-on-the-same-git-hosting)，所以我设置了**仓库**。

## 主题化

现在来看一个关于 Jekyll 主题化的 GitHub 文档。

在`_config.yml`中，切换`theme: minima`为`theme: jekyll-theme-midnight`导致大量:

```
Build Warning: Layout 'post' requested in _drafts/2018/2018-08-12-github-pages-migration.md does not exist.
Build Warning: Layout 'page' requested in about.md does not exist.
Build Warning: Layout 'home' requested in index.md does not exist. 
```

Enter fullscreen mode Exit fullscreen mode

和一个空白网站。

关于`index.md`的线是直的。我从 [YAML 头版](https://jekyllrb.com/docs/frontmatter/)中删除了`layout: home`，现在有了一个空的索引页。

有[几个问题](https://github.com/benbalter/wordpress-to-jekyll-exporter/issues/37)关于不存在的布局和从[杰基尔文件的覆盖主题](https://jekyllrb.com/docs/themes/#overriding-theme-defaults)我发现我的**极小**主题有:

```
$ ls `bundle show minima`/_layouts
default.html    home.html   page.html   post.html 
```

Enter fullscreen mode Exit fullscreen mode

而**午夜**已经:

```
$ ls `bundle show jekyll-theme-midnight`/_layouts 
default.html 
```

Enter fullscreen mode Exit fullscreen mode

好:

```
cp `bundle show minima`/_layouts/home.html _layouts/
cp `bundle show minima`/_layouts/post.html _layouts/
cp `bundle show minima`/_layouts/page.html _layouts/ 
```

Enter fullscreen mode Exit fullscreen mode

一切正常。

## 分页

每篇文章都有一个长索引开始看起来有点傻。分页上有[杰基尔文档](https://jekyllrb.com/docs/pagination/)。它推荐 jekyll-paginate，声明它已被弃用，取而代之的是[Jekyll-paginate-v2](https://github.com/sverrirs/jekyll-paginate-v2)(GitHub Pages 尚不支持的[)。](https://pages.github.com/versions/)

在`_config.yml` :

```
plugins:
  - jekyll-paginate
paginate: 6 
```

Enter fullscreen mode Exit fullscreen mode

`jekyll serve`抱怨:

```
Pagination: Pagination is enabled, but I couldn't find an index.html page to use as the pagination template. Skipping pagination. 
```

Enter fullscreen mode Exit fullscreen mode

由于我的`index.md`只包含`layout: home`，我将`_layouts\home.html`移动到`index.html`(在回购的根中)。并添加他们推荐的分页模板。

起作用了，但是现在所有的文章都在索引页面上内联分页。我不确定那是我想要的，所以我退出。我尽量避免摆弄视觉布局，但是我发现越来越难保留这个政策。

## 注释

尽管不期望任何人评论(我自己很少留下评论)，我还是有点好奇它们是如何被添加的。

原来 [Disqus](https://disqus.com/) (注意:在中国境内，你需要使用 VPN)是 Github Pages 的“方式”来做到这一点。

我设置了一个帐户，看到我需要添加一些模板魔术。考虑到`_layouts/post.html`(我从**极小**模板中复制的)是这个地方，我打开它，发现了这个广告:

```
{%- if site.disqus.shortname -%}
    {%- -%}
  {%- endif -%} 
```

Enter fullscreen mode Exit fullscreen mode

原来[minima 模板有一个用于 Disqus](https://github.com/jekyll/minima/blob/master/_includes/disqus_comments.html) 的 html 帮助文件，其中包括[【通用代码】](https://disqus.com/admin/universalcode/)，并处理[【配置变量】](https://help.disqus.com/troubleshooting/use-configuration-variables-to-avoid-split-threads-and-missing-comments)。偷那个:

```
cp `bundle show minima`/_includes/disqus_comments.html _includes/ 
```

Enter fullscreen mode Exit fullscreen mode

并且，所以它指向我的 Disqus 站点，[https://rendered-obsolete.disqus.com](https://rendered-obsolete.disqus.com)，按照[本期](https://github.com/barryclark/jekyll-now/issues/53)到`_config.yml`添加:

```
disqus:
  shortname: rendered-obsolete 
```

Enter fullscreen mode Exit fullscreen mode

另外需要注意的是`disqus_comments.html`顶部的`and jekyll.environment == "production"`。 [Jekyll 默认了一个**开发**环境](https://jekyllrb.com/docs/configuration/#specifying-a-jekyll-environment-at-build-time)，所以你要么需要注释掉那个位，要么用:
运行

```
JEKYLL_ENV=production bundle exec jekyll serve 
```

Enter fullscreen mode Exit fullscreen mode