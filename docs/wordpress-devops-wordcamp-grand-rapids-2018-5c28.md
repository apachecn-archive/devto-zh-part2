# WordPress devo PS–2018 年大急流城 WordPress

> 原文：<https://dev.to/tnolte/wordpress-devops-wordcamp-grand-rapids-2018-5c28>

## 1。介绍

*   发展历史
    *   高中
    *   1996 年-个人学校网站-用 C 语言编写的 cgi-bin 处理的图像地图
    *   1996 年-戴维斯科食品国际公司(第一份自由职业)-静态 HTML 网站
    *   大学
    *   2000 年–灵感点基督教营地和静修中心–用于表单处理的 perl，基于 PHP 模板的网站
    *   大学毕业后
    *   2002 年–十字路口靶场基督教营地-[PHP CMS](http://www.phpwcms.org/)
    *   2004-2006 年——美国路德教弟兄会及相关部门——[PHP CMS](http://www.phpwcms.org/)，PHP Authorize.NET 捐赠处理， [osCommerce](https://www.oscommerce.com/)
    *   2006 年–iPCS 无线公司(电信)–PHP 和 Perl
    *   2011 年–爱立信/Sprint(电信)–PHP/。NET/C#/Java
    *   2012 年–艾伦挤压机/SPI(制造)–PHP/Ruby/c#/。网
    *   2016-Sprint(电信)-Java/c#/。网
*   WordPress 历史
    *   个人的
    *   2006 年-[个人博客](https://tim.noltefamily.org/)
    *   2006 年-[信仰塑造播客](https://www.faithshaping.org/)
    *   2008—[印章盒](https://www.thestampbox.com/)，妻子印章/工艺现场
    *   2009 年-[领养博客](https://adoption.noltefamily.org/)
    *   自由记者
    *   2007 年至 2017 年——黎明教堂(网站、博客、播客)
    *   2010 年–海地海星项目
    *   2012 年—[其合作伙伴](https://www.itsdelivers.com/)
    *   2018—[暖烤箱烤房](https://warmovenbakehouse.com/) (WooCommerce)

## 2。DevOps 简介

*   DevOps 是什么？
    *   DevOps 是一种软件工程文化和实践，旨在统一软件开发(Dev)和软件运营(Ops)。
    *   DevOps 是一种做事的方式，而不是工具本身。
        *   [重温“什么是 devo PS”](http://radar.oreilly.com/2014/06/revisiting-what-is-devops.html)—[迈克·劳基德斯](http://twitter.com/mikeloukides)

> 根据您使用的工具，很容易想到 DevOps(或任何软件行业范例);特别是，如果您使用 Chef 或 Puppet 进行自动化配置，使用 Jenkins 进行持续集成，使用一些云提供商提供按需服务器功能，这很容易让人认为您在进行开发运维。但是 DevOps 不是关于工具的；这是关于文化的，它远远超出了开发者和操作者的范围。

*   DevOps 的目标是缩短开发周期，增加部署频率，以及更可靠的版本，与业务目标紧密结合。
    *   实施 DevOps 实践的目的是节省时间、金钱和资源。
    *   开发和运营团队之间的密切了解
*   许多 DevOps 计划都专注于自动化一切。如果有人做了不止一次，那就自动完成，这样就再也不用手动完成了。
    *   如果我们把所有的时间都花在自动化和实现 DevOps 实践上，那么真正的产品不会发布，或者真正的工作不会完成。
    *   DevOps 给 WordPress 开发提供了什么？
*   提高生产力——开发人员、设计人员和测试人员可以致力于解决新问题，而不是重复解决以前问题所需的工作。
    *   环境——我们如何建立开发人员可以在其中工作、运营人员可以轻松支持的环境标准？
    *   重复性任务——开发人员或运营团队在重复做什么工作，使得 send 以某种可靠的方式实现自动化？
    *   业务工作流程——如何标准化业务流程和工作流程？
*   提高质量——一个团队可以依靠可测试的、可再现的、量化的结果，这些结果可以清楚地描述当前的产品状态。
    *   单元测试——需要可以被测试来满足需求的代码。
    *   标准——可以根据明确定义的业务或行业标准自动检查代码。(WordPress 编码标准)
    *   业务需求–可以自动生成测试和标准合规性报告，以确认业务需求得到满足。
*   缩短发布交付时间——当遵循标准和最佳实践时，为发布做准备所需的错误和变更就会减少。
    *   自动化测试——测试可以根据变更运行，也可以根据业务定义的常规时间表运行。
    *   按需用户测试——测试可以由开发人员或质量控制人员按需运行，无需花费时间进行设置。
    *   更多的发布——可以根据需要进行小规模的定期或计划的发布。

## 3。第一步是什么

*   理解概念
    *   您需要采取一些小步骤来开始理解什么可以做，什么应该做，以及它如何适合您的业务或组织。
    *   资源
    *   [卡尔·亚历山大](https://carlalexander.ca/)–[自动化 WordPress 部署简介](https://carlalexander.ca/introduction-automated-wordpress-deployments/)
    *   WP 推送器–面向 WordPress 开发者的 Git
    *   汤姆·麦克法林–[不成功便成仁](https://tommcfarlin.com/ship-it-or-die/)
*   确定您的痛点
    *   你在重复什么？
    *   你最花时间的是什么？
    *   最需要用户支持的是什么？
*   利用他人已经完成的工作
    *   服务
    *   源代码控制
        *   GitHub–Git
        *   [Bitbucket](https://bitbucket.org) – Git & Mercurial
        *   装配–颠覆& Git
    *   持续集成平台
        *   [特拉维斯奇](https://travis-ci.org)
        *   循环
        *   [Cloud bees](https://www.cloudbees.com)–基于云的 Jenkins
        *   [代码船](http://codeship.com/)
        *   [部署总部](https://www.deployhq.com/)
    *   报告
        *   代码覆盖报告服务
    *   指导
        *   OSTraining–[使用 Deploy、Git 和 Bitbucket 的 WordPress 部署](https://www.ostraining.com/blog/wordpress/deploy-git-wordpress/)
    *   插件
    *   萝卜概念-[WordPress GitHub 插件更新器](https://github.com/radishconcepts/WordPress-GitHub-Plugin-Updater)
    *   剧本
    *   迪安·克拉特沃西-[GIT 到 WordPress.org 的 SVN 知识库](https://github.com/deanc/wordpress-plugin-git-svn)
    *   盖瑞·琼斯—[WordPress 插件目录部署脚本](https://github.com/GaryJones/wordpress-plugin-svn-deploy)
    *   迈克·乔利—[Github 到 WordPress.org 的部署脚本](https://github.com/mikejolley/github-to-wordpress-deploy-script)
    *   艾哈迈德·阿瓦斯–[wpgideploy CLI](https://github.com/ahmadawais/WPGitDeploy-CLI)
    *   构建工具
    *   [作曲家](https://getcomposer.org/)
        *   [PHP 代码嗅探器](https://packagist.org/packages/squizlabs/php_codesniffer)
        *   [WordPress 编码标准](https://packagist.org/packages/wp-coding-standards/wpcs)
    *   [NPM](https://www.npmjs.com/)–[咕哝](https://gruntjs.com/)
        *   cedaro-[grunt-WP-i18n](https://github.com/cedaro/grunt-wp-i18n)
        *   斯蒂芬·哈里斯
        *   [grunt-WP-readme-to-markdown](https://github.com/stephenharris/wp-readme-to-markdown)
        *   [咕噜-WP-展开](https://github.com/stephenharris/grunt-wp-deploy)

## 4。最终状态是什么样的？一个插件例子。

[预定特色图片插件](https://github.com/timnolte/scheduled-featured-images/tree/1.0.0-dev)–[https://Travis-ci . org/n digitals/Scheduled-Featured-Images/branches](https://travis-ci.org/ndigitals/scheduled-featured-images/branches)

[Daybreak.tv 定制主题](https://bitbucket.org/daybreakchurch/daybreakv5/branch/dev-5.0.10.1)–[https://app . codeship . com/projects/48683/deployment \ _ branches/54216](https://app.codeship.com/projects/48683/deployment%5C_branches/54216)

*   工具
    *   NPM 和作曲家——可再现的环境
    *   咕哝——运行任务
    *   codecov–代码覆盖报告
*   配置
    *   PHP 代码嗅探器——WordPress 编码标准
    *   phpcs/phpcbf
    *   PHPUnit–单元测试
    *   phpunit
    *   国际化
    *   grunt i18n (addtextdomain，makepot)
    *   WordPress 自述文件到 Markdown-GitHub 和 WordPress 自述文件维护一致性
    *   wp_readme_to_markdown
*   练习
    *   运行本地本地检查
    *   phpunit
    *   phpcms
    *   phpcbf
    *   针对开发分支的测试自动化
    *   TravisCI–分支机构
    *   针对最终版本的测试自动化
    *   特拉维斯奇-大师
*   部署
    *   将插件和主题更新直接部署到服务器
    *   [代码船](http://codeship.com/)–rsync
    *   通过插件部署插件和主题更新
    *   可以选择哪个分支作为插件更新的来源，可以直接从 GitHub 安装
    *   将插件和主题更新部署到 WordPress.org
    *   使用 WordPress.org·SVN 预提交/提交脚本

## 5。问&答

这个空间将被保留下来，以记录在演讲结束时提出的问题和答案。其他问题可以在帖子评论中提出。

帖子[首先出现在](https://www.ndigitals.com/articles/wordpress-devops-wordcamp-grand-rapids-2018/)[诺尔特数字解决方案](https://www.ndigitals.com)上。