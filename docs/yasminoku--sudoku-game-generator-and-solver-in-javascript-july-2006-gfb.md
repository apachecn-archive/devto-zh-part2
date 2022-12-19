# yasminoku——JavaScript 中的数独游戏、生成器和求解器(2006 年 7 月)

> 原文：<https://dev.to/jalbam/yasminoku--sudoku-game-generator-and-solver-in-javascript-july-2006-gfb>

“Yasminoku”是一款完全用 DHTML (HTML、CSS 和 JavaScript)编写的开源数独游戏(游戏、生成器和求解器)。

[![Screenshot](../Images/fe2051c5619bb84f3e0a0ea18f498d63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5_emoYVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://dhtmlgames.com/yasminoku/yasminoku_new.jpg)

这是我的第五个 DHTML 游戏，制作于 2006 年中期。

游戏将产生数独，玩家必须解决它们。但它不仅是一个普通的数独游戏，也是一个数独解算器，允许用户引入自己的数独来解决它。

你也可以通过编辑脚本开头的配置变量来改变游戏行为。

这个游戏根本不用任何图片，只用纯 HTML 和 CSS。

这款游戏非常成功和著名，例如被一家著名的西班牙在线报纸“Libertad Digital”收录。

官方语言是西班牙语和英语。你可以在网上找到很多其他语言的翻译，包括德语、法语、葡萄牙语、捷克语、匈牙利语和中文。

这个游戏的其他修改和改编版本可以在互联网上找到，包括一个 Opera 小工具和几个翻译。

这款游戏已经在 BeOS，Linux，NetBSD，OpenBSD，FreeBSD，Windows，Mac OS X，BlackBerry Tablet OS，Android，iOS 等平台下进行了测试。

**更新(2014 年 12 月 9 日):**

新的改进版本(0.75a)提供了一些错误修复，使用了一些 CSS3 效果和 HTML5，更好的移动设备支持，autosave(使用 Web 存储，如果不支持则返回到 cookies)，它是打印机友好的，它自动适应任何屏幕分辨率(最小为 200 x 180)，改进了浏览器集成(例如，可以很容易地包含在任何具有 IFRAME 的网站中)， 可用于并移植到许多平台，并可移植到更多平台(它与 Apache Cordova、PhoneGap Build、PhoneGap、英特尔 XDK、BlackBerry WebWorks、几乎任何浏览器扩展指南等兼容)，并且现在具有语言自动检测的多语言功能(主版本包括中文、英语、加泰罗尼亚语和西班牙语，添加新语言非常容易)。

它可以通过鼠标(或移动设备上的手指)和/或键盘(Wii 等一些设备上的游戏手柄或一些电视设备上的遥控器也应该可以工作)来控制。

通过新的配置菜单，您可以选择语言(如果您对自动检测的语言不满意)，禁用自动保存，改变游戏帮助您的方式，设置即时计算解决方案显示的秒数，以及禁用一些视觉效果(以提高性能)。

这个游戏被特别设计成允许在任何其他网站中使用(例如，通过使用 IFRAME)。我们只需要获得由“Share”选项(在配置菜单中)生成的 URL(将在 IFRAME 标记的 SRC 参数中使用)。如果我们想对它进行更多的配置，我们可以设置或编辑包含在生成的 URL 中的一些参数(你可以在游戏的网站上了解更多)。您甚至可以使用另一个数独生成器，并通过其 URL 的参数将生成的数独发送给游戏。

因为你可以选择初始数字，从 0 到 81，请记住，有时游戏可以产生一个以上的解决方案。

经过测试的本机端口应该可以正常工作。浏览器版本几乎可以在任何地方工作，无论是现代浏览器还是旧浏览器(包括 Internet Explorer 5.0)。

除了普通版(针对网页浏览器)，部分端口和版本包括 Android(包括诺基亚 X 和亚马逊 Kindle Fire、亚马逊 Fire TV、亚马逊 Fire TV、Fire Phone 等使用的 Fire OS。)、iOS (iPhone、iPod Touch、iPad)、BlackBerry 10、BlackBerry table t OS(BlackBerry PlayBook)、诺基亚系列 40 (S40) / WRT Widget、诺基亚 Asha widget / W3C 打包 Web App (W3C Widget)、Windows Phone 7 & 8、Chrome OS/Chromium OS(Google Chrome App)、Firefox OS (Mozilla Firefox App)、Mozilla Firefox Add-on、Opera Extension (Opera add-on)、Google Chrome extension、Safari extension、Microsoft Edge extension、WebOS、脸书 App、Windows 32 bit(包括安装程序)、苹果 Mac OS X(英特尔)

阅读更多关于如何在你的网站中使用它，将它移植到其他平台，将它翻译成其他语言，等等。请参考下面的官方网站。

多语言在线游戏:[http://yasminoku.tuxfamily.org/new/online/](http://yasminoku.tuxfamily.org/new/online/)

多语言在线游戏(镜像):[http://www.dhtmlgames.com/yasminoku/new/online/](http://www.dhtmlgames.com/yasminoku/new/online/)

官方网站:[http://yasminoku.tuxfamily.org/](http://yasminoku.tuxfamily.org/)(镜像在[http://www.dhtmlgames.com/yasminoku/](http://www.dhtmlgames.com/yasminoku/))。

这个库可以在 GitHub 上找到:[https://github.com/jalbam/yasminoku](https://github.com/jalbam/yasminoku)