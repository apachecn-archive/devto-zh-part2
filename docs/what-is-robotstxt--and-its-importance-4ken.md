# Robots.txt 是什么？以及它的重要性。

> 原文：<https://dev.to/vijayg/what-is-robotstxt--and-its-importance-4ken>

爬行和索引是我们真正需要理解的两个不同的概念。我们知道如何查看 robots.txt，但对 robots.txt 有误解。大多数人认为 robots.txt 是“noidex”或“doindex”。但是请理解 robots.txt 不是用来做索引的。主要用于指示 Google bot 或任何第三方搜索引擎 bot 抓取网站的某个部分。它可能是图像、文本或我们在网页上使用的任何多媒体，除此之外还有敏感数据，特别是在电子商务业务中，人们使用他们的信用卡或借记卡信息进行交易。为了隐藏这些敏感数据，从谷歌机器人或任何机器人想爬我们的网站，我们建议任何网站，我们必须使用 robots.txt 文件。
如何查看 robots.txt 是否适用于任何网站？
Robots.txt 主要驻留在域名的根目录下。

*   因此，我们可以在[www.xyz.com/robots.txt](http://www.xyz.com/robots.txt)检查任何域 robots.txt 文件。如何在我们的网站上实现 robots.txt
*   如果你是 WordPress 用户，你可以使用各种插件来配置你的机器人
*   如果你不是 WordPress 用户，那么你可以简单地使用你的域名的 cPanel。您可以在。htaccess place。robots.txt 用户代理的基本结构:* Disallow:/WP-admin Disallow:/track/

这将防止机器人爬你的管理文件夹跟踪轨道，评论饲料，评论

Robots.txt 文件主要用于阻止搜索引擎机器人抓取你网站的特定部分。如果你想对你网站上已经被索引的网页进行去索引，那么我建议你从谷歌上删除这些网址。