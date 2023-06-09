# 星期五前端:网络版的增强现实

> 原文：<https://dev.to/kball/friday-frontend-augmented-reality-on-the-web-edition-4eha>

这个星期给我的灵感是谷歌刚刚在 Chrome Canary 中启用的一个新 API，它真正打开了网站中增强现实生活的可能性！

[https://www.youtube.com/embed/oVJqL_vehxk](https://www.youtube.com/embed/oVJqL_vehxk)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [更多 Unicode 模式](https://css-tricks.com/more-unicode-patterns/)

一个多月前，我分享了一篇关于 CSS 和 unicode 生成的模式的文章。那篇文章是那一周星期五版的最受欢迎的文章之一，现在作者带着更多的模式回来了。这些太有趣了！

##### [关于“未使用的 CSS”工具](https://css-tricks.com/heres-the-thing-about-unused-css-tools/)

Chris Coyier 打破了“未使用的 CSS”工具的限制和挑战。我记得和“uncss”就 ZURB 基金会进行了各种各样的斗争；配置可能是一场噩梦，在动态网站上跟踪“未使用”的真正含义是一个挑战。难怪有些人决定使用 CSS-in-JS 解决方案……但是对我来说，突出的一点是:“我不认为这些工具是无用的——它们只是...工具”。无论你是在做静态分析，JS 中的 CSS，还是任何其他无数的可能性，这些都只是更好地理解和管理你的代码的工具。根据您的团队和代码库的情况使用。

##### [不使用占位符属性](https://www.smashingmagazine.com/2018/06/placeholder-attribute/)

我觉得这种说法的强势版(“占位符属性坏了，不要用”)有点过了。然而，本文提出的缺点是真实存在的，并且在使用占位符属性时要牢记这一点，这是非常有价值和重要的。正如许多批评技术的文章一样，这可能更好地描述为“占位符属性有局限性和缺点，了解它们并正确使用它们”，但这种懦弱的立场不会引起任何注意。:)

##### [Flexbox 对准综合指南](https://webdesign.tutsplus.com/tutorials/a-comprehensive-guide-to-flexbox-alignment--cms-30183)

在这一点上，flexbox 越来越广为人知和理解，但这仍然是一个很好的复习/指导所有不同的对齐选项。如果您还没有使用 flexbox alignment，这是一篇必读的文章。Flexbox 解决了某些自 web 开发之初就存在问题的对齐问题，并使它们变得易于解决。

##### [对中:最新最酷的方式与最古老最酷的方式](https://css-tricks.com/centering-the-newest-coolest-way-vs-the-oldest-coolest-way/)

没有什么惊天动地的事情，但是很好地提醒了我们在 CSS 方面已经走了多远。从无关的标记和各种看似巫术的表格样式到实现页面居中，现在我们可以在 grid 中使用几行实际上是语义性的、非常清楚地描述我们在做什么的短线。

#### JavaScript

##### [这就是为什么在 JavaScript 中映射一个构造的数组不起作用的原因](https://dev.to/sreisner/heres-why-mapping-a-constructed-array-in-javascript-doesnt-work-55di)

对数组如何在 JavaScript 中实现(即作为一个特殊的对象类)以及为什么会导致一些意想不到的行为的有趣探索。爱死了。

##### [了解更多关于 JavaScript 的承诺:25 次测试从零到英雄](https://medium.freecodecamp.org/learn-more-about-javascripts-promises-from-zero-to-hero-in-25-tests-ddaaf4d3c928)

我以前做过测试驱动开发，但这是我第一次看到“测试驱动教程”。我觉得我喜欢！作者设计了一系列茉莉测试来探索承诺的功能。对于教程来说，这是一种相当直观的方法，另外，现在您已经有了一个测试套件，可以在 jsfiddle 上进行修改，并获得关于您是否正确使用 promises 的即时反馈。

##### [JavaScript async/await:优点、缺陷以及如何使用](https://hackernoon.com/javascript-async-await-the-good-part-pitfalls-and-how-to-use-9b759ca21cda)

这太棒了。不仅仅是关于如何使用 async/await 的指导，还强调了它的优点和缺点，以及一些不同的方法来最大化它的优点和避免缺点。值得再次强调的是:async/await 是关于承诺的语法糖，如果你不理解承诺… [去学习它们](https://codeburst.io/a-simple-guide-to-es6-promises-d71bacd2e13a)！他们会让你的生活变得更好。

##### [给你的 JavaScript 加点料](https://medium.com/@riccardoodone/spice-up-your-javascript-5314bf28f3e5)

一种有趣的方法是使用链式闭包和返回函数的函数来实现函数的“类 currying”方法。如果你以前没有遇到过这种方法，它会让你大吃一惊，尽管我必须说我更喜欢(不可否认在实现方面稍微繁重一点)的方法 [Rambda](https://github.com/ramda/ramda) 来实现真正的奉承。如果 currying 只是让你想起了泰国菜，别担心，我不久前也去过。:P 如果你想深入了解这个概念，我推荐[这本](http://fr.umio.us/favoring-curry/)。

##### [如何构建玩起来好听的 Vue 组件](https://vuejsdevelopers.com/2018/06/18/vue-components-play-nicely/)

(偏见提醒——我是以客座博文的身份写这篇文章的)。我一直在使用很多开源的 Vue 组件，开发自己用的组件，并致力于开源组件库。本文分解了我从那项工作中收集的一组最佳实践模式，解释了如何创建在任何项目中都工作良好的 Vue 组件。

#### 其他牛气

##### [用于网络的增强现实](https://developers.google.com/web/updates/2018/06/ar-for-the-web)

这真的真的很酷。谷歌 Chrome 刚刚(在 canary，在真正先进的 android 设备上)启用了一个 API，让 web 开发人员进行复杂的“点击测试”，本质上是在照片或流媒体视频中绘制出事物在物理空间中的位置。这为增强现实铺平了道路——将虚拟物品放在现实世界中的适当位置——可以通过网站访问。想象一下，电子商务网站可以让你看到他们的产品在你的客厅里是什么样子，或者一个城市旅游网站可以让你在城市中漫步时看到注释或听虚拟导游。只需安装一个网络浏览器，就可以从你的智能手机上下载所有内容。未来看起来很神奇！

##### [8 点网格:网页排版](https://medium.freecodecamp.org/8-point-grid-typography-on-the-web-be5dc97db6bc)

非常有趣的关于网页排版尺寸系统背后的思想的概述。如果你不太熟悉字体设计(我不是)，你可能会想点击底部的参考文献和以前的文章。不管怎样，如果你正在为你的网站做设计/排版选择，你会想要读这本书的。

##### [在静态网站上处理表单的 5 种方式](https://forestry.io/blog/5-ways-to-handle-forms-on-your-static-site/)

我喜欢各种用例的静态站点。它们快速、简单，而且扩展性超级好，所以如果你登陆 HackerNews 首页时遇到了疯狂的流量爆发，你不必担心。但是让我们面对现实吧——大多数网站需要某种方式让用户提交内容，即使只是一个“联系”表单。本文介绍了一些可供您选择的方法。

##### [营销人员迫不及待地使用 AMP 收发电子邮件，但以下是阻碍他们的因素](https://litmus.com/blog/marketers-cant-wait-to-use-amp-for-email-but-heres-what-will-be-holding-them-back)

哇，我从来没有听说过电子邮件放大器，但这看起来真的很有趣。有一堆警告和当前的限制——这篇文章有很多——但如果你对电子邮件营销很感兴趣，我认为这是一个你会想要观察和尝试的领域。

##### [如何将草图导出为 CSS 动画代码](https://medium.com/sketch-app-sources/how-to-export-sketch-to-css-animations-code-63404263ebf2)

在花了几个小时的时间和一个设计师来回尝试在一组 CSS 动画上获得准确的时间之前，这让我非常兴奋。使用 Anima 插件直接在 Sketch 中构建动画，然后将它们导出到 CSS。斯威伊特。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这样，请务必注册，以便每周五将这些简讯直接发送到你的收件箱中！在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)