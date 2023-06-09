# 恐怖冒险游戏(2006 年 9 月)

> 原文：<https://dev.to/jalbam/la-villa-del-seis--horror-adventure-game-in-php-and-dhtml-september-2006-585j>

《La villa del seis》(又名《La villa del 6》)是一款用 PHP、HTML、CSS 和 JavaScript 编写的开源冒险游戏。

[![Screenshot](img/c6f262c679ea6b6396435319c5d96dcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P1VCLilO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://dhtmlgames.com/lavilladel6/lavilladel6.gif)

这个游戏是在 2006 年末制作的，使用了我之前制作的 [Yasmina 的任务](https://dev.to/jalbam/yasminas-quest--adventure-game-engine-in-php-and-dhtml-november-2005-1bpk)的引擎，并对其进行了修改和改进。它是与 Yasminan Llaveria del Castillo 共同制作的，其中一些照片由 Esteban Villegas Gallego 拍摄。

官方语言是西班牙语和加泰罗尼亚语。

它在互联网上已经相当有名，有成千上万的下载量和在线玩家。至少在一本名为"[aoo/Cero](http://www.revistaa%C3%B1ocero.com/)"的著名西班牙杂志上，何塞·曼努埃尔·塞拉诺·奎托撰写并于 2007 年 1 月发表的一篇题为"[La ruta de los pueblos condenados](http://www.revistaa%C3%B1ocero.com/secciones/geografia-magica/ruta-pueblos-condenados)"的文章中提到了这一点(书面和网上)。

由于这款游戏拥有亚斯米娜的任务引擎，有些人选择了这个游戏的代码来创造他们的冒险，而不是使用 T2 的任务代码。和[亚斯米娜的任务](https://dev.to/jalbam/yasminas-quest--adventure-game-engine-in-php-and-dhtml-november-2005-1bpk)一样，这款游戏既可以点击冒险，也可以文本冒险，并且可以在任何类型的网络浏览器上运行。JavaScript 和 CSS 是有用的，但不是真正必要的。图像也不是必须的，允许游戏在基于文本的网络浏览器中作为 Lynx 或 Links 来玩。

这款游戏已经在 BeOS，Linux，NetBSD，OpenBSD，FreeBSD，Windows，Mac OS X，QNX，黑莓平板操作系统，Android，iOS 和其他系统下进行了测试。在它的主页上，你可以看到一些游戏在许多不同平台上运行的截图，如任天堂 DS、索尼 PSP、世嘉 Dreamcast 和 Pocket PC。

最近(2018 年 9 月 27 日)，增加了一个使用 [php-webkit](https://github.com/baconbrad/php-webkit) 的新包，因此可以在装有 SP2 (Service Pack 2)和更新版本(包括 Windows 10)的 Windows XP 上离线播放。只需下载[la _ villa _ del _ seis _ PHP-WebKit _ win32 . zip](https://github.com/jalbam/lavilladel6/blob/master/la_villa_del_seis_php-webkit_win32.zip)或者[la _ villa _ del _ seis _ PHP-WebKit _ win32 . 7z](https://github.com/jalbam/lavilladel6/blob/master/la_villa_del_seis_php-webkit_win32.7z)文件即可。[la _ villa _ del _ seis _ PHP-WebKit _ win32 _ installer . exe](https://github.com/jalbam/lavilladel6/blob/master/la_villa_del_seis_php-webkit_win32_installer.exe)文件也提供了同一个包的安装程序。将相同的包移植到其他操作系统和平台应该不会太难。对于一些较旧的 Windows 版本(比带有 SP2 的 Windows XP 更旧)，[la _ villa _ del _ seis _ on _ server 2 go . zip](https://github.com/jalbam/lavilladel6/blob/master/la_villa_del_seis_on_server2go.zip)文件提供了一个使用 [Server2Go](https://sourceforge.net/projects/server2go/) 的较旧的包。

西班牙语在线游戏:[http://lavilladel6.tuxfamily.org/lavilladel6_spanish/](http://lavilladel6.tuxfamily.org/lavilladel6_spanish/)

西班牙语在线游戏(镜像):[http://www.dhtmlgames.com/lavilladel6/lavilladel6_spanish/](http://www.dhtmlgames.com/lavilladel6/lavilladel6_spanish/)

加泰罗尼亚语在线游戏:[http://lavilladel6.tuxfamily.org/lavilladel6_catalan/](http://lavilladel6.tuxfamily.org/lavilladel6_catalan/)

加泰罗尼亚语在线游戏(镜像):[http://www.dhtmlgames.com/lavilladel6/lavilladel6_catalan/](http://www.dhtmlgames.com/lavilladel6/lavilladel6_catalan/)

官方网站:[http://lavilladel6.tuxfamily.org/](http://lavilladel6.tuxfamily.org/)(镜像在[http://www.dhtmlgames.com/lavilladel6/](http://www.dhtmlgames.com/lavilladel6/))。

也可以在 GitHub 上找到:[https://github.com/jalbam/lavilladel6](https://github.com/jalbam/lavilladel6)