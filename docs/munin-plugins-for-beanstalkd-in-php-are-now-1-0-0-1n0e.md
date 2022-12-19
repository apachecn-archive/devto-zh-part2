# PHP 中 Beanstalkd 的 Munin 插件现在是 1.0.0

> 原文：<https://dev.to/fgm/munin-plugins-for-beanstalkd-in-php-are-now-1-0-0-1n0e>

2014 年，我为 AirBnB 推出的 Beanstalkd 创建了一个 PHP 版本的 [Munin 插件，但最初是用 Python 创建的。](https://github.com/fgm/beanstalk-munin-php)

一晃四年过去了，经过修改以符合 PHP-FIG PSR1、PSR2 和 PSR12 标准，以及 Zend 编码标准，加上额外的文档，我今天在 Apache APL-2.0 许可下发布了这个包的第一个“稳定”版本 1.0.0。

要使用它并确保您的[收藏夹作业队列](http://kr.github.io/beanstalkd/)的可观察性，请遵循 INSTALL.md 文件中的说明。简而言之，只需 3 个步骤:

1.  克隆存储库
2.  用`composer install`安装其依赖项
3.  将插件符号链接到`/etc/munin/plugins`

就这样，您现在可以绘制命令速率、连接、作业速率、队列年龄、队列类型大小和超时频率。

博客标签:

*   [PHP](https://blog.riff.org/topic/computing/php_0)
*   [蛋](https://blog.riff.org/taxonomy/term/238)
*   [可观察性](https://blog.riff.org/taxonomy/term/239)
*   [php-fig](https://blog.riff.org/tags/phpfig)