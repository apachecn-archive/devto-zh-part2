# GitHub 页面现在支持 HTTPS -使用它！

> 原文：<https://dev.to/remotesynth/github-pages-now-support-https---use-it-2d5h>

今天 [GitHub 宣布](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/)自定义域名将支持 HTTPS。这是个好消息！

你们中的许多人可能已经知道， [GitHub Pages](https://pages.github.com/) 是托管静态站点的一种很好的(而且完全免费的)方式。它已经为任何使用 [Jekyll](https://jekyllrb.com/) 的人建立了集成，这样每当新代码被签入时，它将自动重建你的站点。但是，您可以部署和托管任何静态站点，这使得它成为个人博客、事件站点或项目站点等的一个很好的解决方案。

缺点是，到目前为止，如果没有像 [CloudFlare](https://www.cloudflare.com/) 这样的东西的支持，你就不能使用 HTTPS，CloudFlare 提供了一个带有共享 SSL 证书的免费账户。这对于许多项目来说没问题，但并不总是合适的。例如，我在 GitHub 页面上主持了一些活动网站，你无法知道你与谁共享了 SSL 证书(对于一家公司来说，这可能会导致一些尴尬)。

唯一的其他选择是接受可怕的“你的连接到这个网站是不安全的”警告。如果像 7 月份 Chrome 68 中的[计划的那样，Chrome 开始在地址栏中显著标记页面为“不安全”，这个警告只会变得更糟。](https://techcrunch.com/2018/02/08/chrome-will-soon-mark-all-unencrypted-pages-as-not-secure/)

更好的是，GitHub 增加了配置您的 URL 以执行 HTTPS 的功能，这意味着任何访问您的域的不安全版本的访问者将被自动重定向到 HTTPS。

如果你想开始为 GitHub 页面配置你的域，[查看文档](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)，然后浏览[公告帖子](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/)中的指令来配置 SSL，或者浏览他们的[文档中的特性](https://help.github.com/articles/securing-your-github-pages-site-with-https/)。