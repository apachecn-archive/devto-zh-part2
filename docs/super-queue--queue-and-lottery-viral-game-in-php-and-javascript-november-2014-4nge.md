# 超级队列 PHP 和 JavaScript 中的队列和彩票病毒游戏(2014 年 11 月)

> 原文：<https://dev.to/jalbam/super-queue--queue-and-lottery-viral-game-in-php-and-javascript-november-2014-4nge>

我为一家中国软件公司制作了这款游戏，该公司的最终客户是一家连锁珠宝店，他们想要一款微信游戏来宣传自己，在一周内给玩家一些奖品和优惠券(以庆祝 11 月 11 日的[中国光棍节](https://en.wikipedia.org/wiki/Singles%27_Day))。[微信(微信)](https://es.wikipedia.org/wiki/WeChat)是中国使用最多的移动设备即时通讯客户端，由[腾讯](https://en.wikipedia.org/wiki/Tencent)(也是拥有 [QQ](https://es.wikipedia.org/wiki/QQ) 的同一家公司)制造。

[![Screenshot](../Images/64044bbb9bb4268ead9c0bac015cb3bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2BhbNpVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://joanalbamaldonado.com/portfolio/img/content/super_queue.jpg)

这些图形是由乔安(乔安)而中文翻译是由董双丽(董·)。开发了将近一个月。

游戏包括两个级别。在第一关，你在排队等候，你的任务是插队，直到你能进门。一旦你进门，第二关是等待异性搭档(如果游戏配置为使用假玩家或管理员强制要求，可以是真玩家，也可以是假玩家)，当你得到一个搭档时，你们每个人都必须旋转一个带有十二宫图符号的轮盘赌，如果你们都得到相同的十二宫图符号，双方都将赢得一份奖励。如果你得到一个不同的符号，你会得到一张优惠券。

这是一款病毒式游戏，因为玩家必须与他人分享游戏，才能插队。每次有人通过你的邀请注册，你都会跳两个位置。但是其他人也能够在你面前跳跃和移动，所以这变成了一种战斗。

玩家不需要让游戏一直开着。因此，他们可以关闭游戏，并在以后随时打开它。如果有任何进步或新的事情发生，游戏会显示出来。

每个队列有一个最大的人数限制，所以当一个人第一次加入游戏时，如果所有其他队列都满了，游戏就会创建一个新队列的“新世界”。这个游戏也用假的人来填充队列，以增加关卡难度和避免看起来是空的。所有这些都可以通过变量来配置。

登录方式、奖品、每个队列的最大人数、获胜机会以及许多其他事情都可以通过配置文件进行配置。

这个游戏已经包含了中文和英文。还有一个本地化文件，可以很容易地将游戏翻译成更多的语言。游戏将试图自动检测用户的语言。

公司想让游戏运行一周。在这一周内，这款游戏取得了圆满成功，拥有超过 50000 名玩家。所有的奖品都发完了。

这款游戏运行在[微信(WeChat)](https://es.wikipedia.org/wiki/WeChat) 上，并使用了它的一些功能，但它可以轻松配置为在任何其他应用程序或平台上使用(例如在 [QQ](https://es.wikipedia.org/wiki/QQ) 、脸书或任何启用了 JavaScript 和 CSS 的网络浏览器中)。

有一种调试模式，可以通过密码访问。调试模式实时显示关于游戏的有用信息，它还提供了一个管理控制面板(类似于上帝模式面板),有许多欺骗、控制其他玩家等选项。

它还包括一个霸王测试页面来检查服务器的限制。

这个游戏是用 HTML、CSS / CSS 3、JavaScript、JSON、XHR (AJAX)、PHP、mySQL、RPC(远程过程调用)、OpenID、OAuth、[微信 API](https://developers.weixin.qq.com/miniprogram/dev/api/) ( [微信 API【T3)/](http://admin.wechat.com/wiki/index.php?title=API_Introduction)[weixinsbrridge](https://github.com/Tencent/weui/wiki/%E5%BE%AE%E4%BF%A1JSAPI)等技术制作的。

您可以自己尝试这个游戏，只需创建所需的数据库(包括一个包含所需表格的 SQL 文件)并编辑配置文件以使用该数据库。基本上，你只需要一个支持 PHP 和 mySQL 的 web 服务器。

可以在 GitHub 上找到:[https://github.com/jalbam/super_queue](https://github.com/jalbam/super_queue)