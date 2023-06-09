# 为 Heroku 应用程序设置自定义域名和免费 SSL 证书

> 原文：<https://dev.to/postsrc/setting-up-a-custom-domain-name-and-free-ssl-certificate-for-heroku-apps-2063>

## 简介

在这篇文章中，我将介绍如何通过简单的步骤为 Heroku 应用程序设置自定义域名。请注意，您必须首先使用 Heroku 设置您的应用程序，在这种情况下， [*我已经在 Heroku*](https://postsrc.com/posts) 上设置了 Laravel 5.6。

我们还将设置由[提供的免费 SSL 证书，让我们通过利用](https://letsencrypt.org/) [CloudFlare](https://www.cloudflare.com/) 工具来加密，以便于安装，并减少启动和运行的程序。

[![Lets Encrypt Homepage](img/0baf836573862e216db398dd1b28b59d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vpDn4uqw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikateach.com/wp-content/uploads/2018/07/Lets-Encrypt-Homepage-994x420.png)

## 入门

要开始，我们首先需要将域链接到我们的 Heroku 应用程序，要做到这一点，只需运行`heroku domains:add example.com --app=awesomeapp`确保使用`--app`标志来确保您指定了正确的应用程序。或者，在 Heroku 网站的设置部分，你可以访问“域和证书”标签。从那里你可以添加你已经购买的域名。

[![Add new Domain on Heroku](img/ca02d45523e42876504dbafb99c4d92a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WHr8ac1L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikateach.com/wp-content/uploads/2018/07/Add-new-Domain.png)

一旦您添加了域，现在我们必须设置我们的 CloudFlare 站点设置。如果您还没有设置应用程序，请确保首先添加它，现在让我们设置 DNS。在 DNS 选项卡中，您将需要 2 个 CNAME，如下所示。

[![Add a new CNAME record](img/71e69beced8c0da97f66afe9f39be4ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FLc5YcZj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikateach.com/wp-content/uploads/2018/07/Cloudflare-DNS-1352x420.png)

到目前为止，您将能够使用您指定的域名访问您的网站。接下来前往加密选项卡，并启用 ssl 证书。默认情况下，这将安装让我们加密，这样做，您将能够使用 https 协议访问网站。

[![Cloudflare Crypto tab](img/a40928b9277068a32de71a3d5a6d4f77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uSufRjH7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikateach.com/wp-content/uploads/2018/07/Cloudflare-Crypto-tab-1087x420.png)

## 结论

在 Cloudflare 的帮助下，为 Heroku 应用程序设置自定义域名和获取免费 SSL 证书非常简单。

😉谢谢 CloudFlare。