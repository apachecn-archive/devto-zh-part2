# 将 Github Pages Repo 指向悬停域

> 原文：<https://dev.to/nickymarino/pointing-a-github-pages-repo-to-a-hover-domain-105e>

*本文最初发表于[我的网站](https://nickymarino.com)。查看更多精彩内容！*

我的博客目前使用 [GitHub Pages](https://pages.github.com/) 托管——这是一种免费托管静态网站或博客的好方法——通过链接到我通过 [Hover](https://www.hover.com/) 购买的自定义域名。虽然这两种服务都令人惊叹，但连接这两种服务需要许多打开的标签和几个等待期。这篇文章将解释将 GitHub Pages repo 指向 Hover 上的自定义域所需的步骤。

## 飞行前检查

在将 GitHub 页面连接到自定义域之前，我首先在我的存储库`username.github.io`上更新了我的博客，并检查了它在默认网站(通常是`username.github.io`)上的显示是否正确。

## 将回购关联到该域

首先，您需要用您的自定义域更新您的存储库。在 repo 的设置中，在 GitHub Pages 部分的“自定义域”中输入域。

[![GitHub Pages settings for the repo](img/361cd916c6df231d411410877245cb36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XfZKP5WB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/od8rooznh47i0cdk1cjb.png)

## 悬停上一条记录

下一步是配置悬停。找到 GitHub 当前的 IP 地址列表来创建记录。然后，进入你的 [Hover](https://www.hover.com/) 账号，选择你的域名，进入 DNS 标签)添加到 Hover 上的 DNS 标签。在撰写本报告时，这些国家是:

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153 
```

Enter fullscreen mode Exit fullscreen mode

然后，转到您的[悬停](https://www.hover.com/)帐户，选择您的域，并转到 DNS 选项卡。删除“记录”下带有“A”的所有 DNS 记录。

对于 GitHub 帮助页面上的每个 IP 地址，添加一个 DNS 记录。对于每一个，“类型”将是`A`,“主机名”将是`@`,“TTL”可以保留为默认值。

您还需要创建一个具有类型`CNAME`、主机`www`和值`[username].github.io`的记录，这样就可以通过[www.yoursite.com](http://www.yoursite.com)访问您的站点。最终的一组值应该如下所示:

[![Hover DNS settings](img/5ee51ba2ceb56c3a1ee8628977a2c1d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aIb7COqm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wdum3ad7p2v27j0l1cmw.jpg)

这些更改可能需要几个小时(或大约一天)才能生效。休息一下，睡一觉，然后回到你的领域，确保一切正常。现在我们可以强制执行 HTTPS 了！

## CNAME 文件

在您的 GitHub repo 中，在根文件夹中创建一个名为`CNAME`(无扩展名)的文件。在新文件中，写下你正在使用的悬停域。*确保文件只有一行。*该文件应该类似于下面的文件:

[![CNAME Contents](img/158f1d6186cf5edeb4fd095ecf61346c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CTrrH99V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vrra7gnvb37knc9vndpg.jpg)

## 创建 HTTPS 证书

如果您返回到您的回购设置页面以强制执行 HTTPS，您可能会看到以下“尚不可用”错误:

[![GitHub Pages HTTPS error](img/a5c775ab1d3795a8dfb5afbe12f32a6b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vBA-mnjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gkyyfaxohna4aw08w5qv.png)

根据 [GitHub 的故障排除页面](https://help.github.com/articles/troubleshooting-custom-domains/#https-errors)，您需要删除然后重新添加您的存储库的自定义域。等待大约 24 小时，证书就会生成，您应该可以开始工作了！

*编辑:*感谢[大卫·瑞恩](https://dev.to/davedri/comment/8m9l)指出我错过了 CNAME 记录！