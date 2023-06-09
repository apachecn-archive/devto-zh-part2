# PHP 5 的生命即将结束；为什么你应该关心。

> 原文：<https://dev.to/david_j_eddy/php-5-end-of-life-is-upon-us-why-you-should-care-3gpk>

从我的博客上传的十字。

2004 年 PHP 5 向公众发布。那一年，美国经济已经从大萧条中复苏。3 年前发布的 Windows XP 迎来了它的飞跃，年度最佳流行歌曲是的[亚瑟](https://en.wikipedia.org/wiki/Billboard_Year-End_Hot_100_singles_of_2004)的【耶】。记住，[PHP 5 发布时，Windows XP 已经有 3 年历史了。有一段时间了。](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_versions)

<figure>[![](img/c47050541c72c7df366588f10fa42315.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qcn7yxgF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/php_2018_10_support_timeframe.png) 

<figcaption>PHP 版本官方支持时间轴。</figcaption>

</figure>

在接下来的五年里，任何优秀的语言都会发布一些小版本。2014 年 8 月发布的 PHP 5.6 为这种语言带来了许多好的但微小的更新。截至今天(2018 年 10 月)，它是生产中最受欢迎的 PHP 版本之一。这就是问题的根源。尽管一个新的主要版本已经发布了近 3 年，但是最近几年的采用却停滞不前。现在 5.6 将在几个月后寿终正寝。这意味着不再有更新，不再有安全补丁，发现问题时不再有帮助。如果说 Windows XP 的寿终正寝向我们展示了什么的话，那就是当一个软件到了寿终正寝的时候，仍然被广泛使用，这个软件就成了坏人的主要目标。对于坏人来说，还有什么比带有安全漏洞的软件更好的目标呢？

不是听起来像一个恐惧制造者，但是如果你有一个运行 PHP 5 的应用程序，并且你没有在接下来的 6 个月内完成升级的途径；开始计划出现数据泄露、负面报道、声誉受损、失去客户，以及可能的法律诉讼。这不是你是否会被攻破的问题；但是当。你真的想成为因数据泄露而在 https://haveibeenpwned.com/上市的公司之一吗？

<figure>[![](img/17968ac4d38d58f98de852c53e76d649.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_rLyg3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/php7_interconnect_servers.png) 

<figcaption>PHP，现代公共互联网的粘合剂。</figcaption>

</figure>

## 能做些什么？！

那么对于这种情况能做些什么呢？这取决于你的技术水平和主机提供商。一些主机从第一天起就支持 PHP 7。这意味着更新是一个关注你的应用程序的问题。其他人在后面主持拉赫。联系你的主机提供商，让他们支持这种语言的安全版本。如果你有一点技术知识，你和你的团队可以直接移植到一台装有 PHP7 的新机器上。否则，如果你更专业一点，你可以自己安装 PHP 7。这可能需要移除一个不支持的 Wordpress 插件，交换代码库，或者甚至做一些重新编程，因为一个语言扩展不再被支持。

## 对于我们当中的程序员来说

作为一名程序员/开发人员，你能做些什么来尽可能快速、轻松地迁移到 PHP 7 呢？首先也是最重要的是，检查在 php.net[的](https://php.net)[移民文件](http://php.net/manual/en/migration70.php)。如果你还在使用下面列出的四个扩展中的任何一个，你将会有*一些*的工作要做，否则破坏性的改变会被故意保持在最低限度。

*   ereg
*   mssql
*   关系型数据库
*   sybase_ct

<figure>[![](img/b3e34585f682b9d5954bb0b69fdb0123.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--udfVB4nK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/php7_trading_graph.jpg) 

<figcaption>PHP 应用程序无处不在，已经成为一种商品。</figcaption>

</figure>

迁移的额外回报是值得的；您的系统是最新的，本质上更安全，运行速度甚至快了 30%。这意味着在相同的硬件上多了 30% 3 的访问者，或者能够降级硬件，从长远来看节省了资金。

## 相关文章/资源

*   [http://php.net/supported-versions.php](http://php.net/supported-versions.php)
*   [https://Hayden James . io/PHP-5-6-eol-end-of-life-PHP-7-compatibility-check/](https://haydenjames.io/php-5-6-eol-end-of-life-php-7-compatibility-check/)
*   [https://www . go daddy . com/community/Managing-Web-Hosting/PHP-5-6-End-of-Life/TD-p/51463](https://www.godaddy.com/community/Managing-Web-Hosting/PHP-5-6-End-of-Life/td-p/51463)
*   [https://itristanmedia . com/blog/2018/05/17/PHP-5.6-is-approxing-the-end-of-its-of-life-and-what-do-about-it/](https://itristanmedia.com/blog/2018/05/17/php-5.6-is-approaching-its-end-of-life-and-what-to-do-about-it/)
*   [https://www . zdnet . com/article/around-62-of-all-internet-sites-will-run-an-unsupported-PHP-version-in-10-weeks/](https://www.zdnet.com/article/around-62-of-all-internet-sites-will-run-an-unsupported-php-version-in-10-weeks/)
*   [https://w3techs.com/technologies/details/pl-php/all/all](https://w3techs.com/technologies/details/pl-php/all/all)
*   [https://sensorstechforum . com/support-PHP-5-6-x-ends-websites-risk/](https://sensorstechforum.com/support-php-5-6-x-ends-websites-risk/)

<figure>[![](img/b1deeba04d572dc1f89c6431d756a0b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O_N2W_jW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/10/php7_quad_color.png) 

<figcaption>PHP 7 是现在和未来。</figcaption>

</figure>

## 帮助是可用的

要获得低成本评估并免费讨论缓解计划，请联系[me[at]davidjeddy[dot]com](me@davidjeddy.com)。这是如此重要，我将免费为您提供一个初步的安全审计。您的应用程序安全性和用户隐私对我来说非常重要；你也应该如此。

## 包裹

如果你没有 It 团队，使用托管服务提供商，或者你是一家拥有数千名开发人员和运营人员的企业，这都没有关系。这会影响到你。