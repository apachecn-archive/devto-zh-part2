# 星期五前端:反应和古腾堡版

> 原文：<https://dev.to/kball/friday-frontend-react-and-gutenberg-edition-oa>

这个星期，我对 React 和 Gutenberg 一起来到 WordPress 感到非常兴奋！

Wordpress 被全世界数百万人使用，随着新的 Gutenberg 编辑器的出现，它突然拥抱了现代网络技术，特别是 React。

这为前端开发人员创造了巨大的机会。在本周的周五前端灵感视频中了解更多信息:

[https://www.youtube.com/embed/SQWAoCs4li8](https://www.youtube.com/embed/SQWAoCs4li8)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [如何更好的写 CSS](https://medium.freecodecamp.org/how-to-get-better-at-writing-css-a1732c32a72f)

对编写可维护的 CSS 所需的工具和方法有很好的概述。讨论 SCSS、方法、命名系统和文件系统架构。非常好的读物。

##### [如何用 CSS 制作一个跳动的 SVG 图标动画](https://techstacker.com/how-to-animate-a-bouncing-svg-icon-with-css/yXrdnAk8PB8gkvRbe)

带示例代码的简单教程。充分利用内联 SVG 的属性来直接设计图标的样式，并且非常详细地分解关键帧动画。不错！

##### [我应该尝试使用 IE 版本的网格布局吗？2018 年重访](https://rachelandrew.co.uk/archives/2018/07/17/should-i-try-to-use-the-ie-version-of-grid-layout-revisited-for-2018/)

我最近链接了一个关于在 IE 中使用 CSS Grid 的系列——这里有一个关于什么时候这样做有意义的反驳。Rachel Andrew(世界上网格专家之一)强调，IE 中的网格实现差异很大，只有少数特定情况下使用它才有意义。

##### [用 CSS 指针事件可以做的事情](https://codepen.io/MartijnCuppens/full/MBjqbM/)

好吧，这有点让我吃惊，我必须通读代码 3 到 4 次才能弄清楚到底发生了什么。显然，通过关闭容器上的指针事件，然后在子元素上显式启用它们，您可以编写纯 CSS 组件，这些组件在悬停或单击子元素时会发生变化。这真是疯狂的事情。

##### [用 CSS 解决了！基于给定元素数量的逻辑样式](https://css-tricks.com/solved-with-css-logical-styling-based-on-the-number-of-given-elements/)

很好地展示了伪选择器所支持的一些功能，这些功能使您的页面在风格上对用户输入、页面中的状态以及它们相对于其他同级元素的布局做出反应。

#### JavaScript

##### [Redux 或者 ES6](https://www.fullstackreact.com/articles/redux-or-es6/)

(偏见警告——这是我的一篇客座博文)React 生态系统的一个奇妙但也常常令人沮丧的地方是它对现代 JavaScript 的接受程度。虽然这对提高我们代码的能力和表达能力很有帮助，但它也经常让学习变得非常沮丧！这篇文章从 Redux 文档和教程中选取了一组例子，分解了哪些概念来自 Redux，哪些是 ES6。对于每个例子，它都提供了足够的信息来理解正在发生的事情，同时也为那些感兴趣的人提供了更完整的资源链接。

##### [JavaScript 基础知识学习前的反应](https://www.robinwieruch.de/javascript-fundamentals-react-requirements/)

与上一篇文章类似，这是一篇使用基于 React 的例子浏览大量 JavaScript 基本概念(尤其是现代 JS)的文章。做得非常好。

##### [Redux 与 React 上下文 API](https://dev.to/dceddia/redux-vs-the-react-context-api-1nof)

非常好的解释了在 React 中处理状态的各种方式。从裸露的 React 组件开始，强调道具训练和它产生的问题，展示一种用子道具解决它的方法，然后深入解释 Redux 和上下文 API。这不会让你深入任何一个主题，但它确实给出了问题域和不同类型解决方案的一个很好的概述。

##### [理解 React 中的高阶成分和古腾堡](https://dev.to/kball/understanding-higher-order-components-in-react-and-gutenberg-1mii)

(偏见警告-我也写了这篇文章)随着 WordPress 在新的 Gutenberg 编辑器中拥抱 React，数百万开发者突然被引入这个世界，并争先恐后地赶上。在这篇文章中，我将分解 React 中非常常见的一种架构模式——高阶组件。

##### [Vue UI:初看](https://morningstar.engineering/vue-ui-a-first-look-916600d9a918)

即使在主要关注 React 的版本中，这也太有趣了，不容错过。下一版本的 Vue CLI 附带了一个内置的 GUI 项目管理系统。允许您创建项目，在创建前后进行配置，甚至添加一些插件，所有这些都使用浏览器中的 GUI。我是一个老派的终端家伙，所以这似乎不太像我的事情，但我怀疑这将在更多视觉背景的人中间引起轰动，扩展 Vue 在“简单易学”和“有利于设计师学习编码”领域的主导地位。

#### 其他牛气

##### [web assembly 的未来——看看即将推出的功能和提案](https://blog.scottlogic.com/2018/07/20/wasm-future.html)

对于那些对 WebAssembly (me me me！)这是一篇很棒的帖子，因为它让你看到了它的发展方向。对 WebAssembly 正在进行的各种功能和提议进行了出色的分析，包括它们在流程中所处位置的更新。

##### [你知道样式和脚本标签可以设置为显示:块吗？](https://css-tricks.com/did-you-know-that-style-and-script-tags-can-be-set-to-display-block/)

好吧，把这个放在奇怪但很酷的文件夹下。只需将`style`和`script`标签设置为`display: block`和`contenteditable`属性，就可以为页面上的样式和脚本创建一个内嵌编辑器。除了教育之外，我不确定我是否看到了它的用途，但对于只是玩玩来说，它真的很棒！

##### [将图片转换成 WebP](https://www.smashingmagazine.com/2018/07/converting-images-to-webp/)

Webp 是一种新的图像格式，支持 alpha 通道，允许有损或非无损版本，比 PNG 和 JPEG 节省了大量的空间，甚至支持动画(GIF 替换，有人知道吗？)它还不能在所有的浏览器上工作，但是当它存在的时候，你可以通过各种方式设置你的服务器或者页面来使用它。本文不是关于这个的，而是深入到将图像转换成 WebP 的各种方法中，包括直接从工具中导出或者在构建步骤中导出。

##### [关于设计和建造拨动开关](https://www.sarasoueidan.com/blog/toggle-switch-design/)

引人入胜的一步一步演练所有的思维过程，研究和工作，进入设计和编码一个可访问的和美丽的拨动开关。这就是我们很多人喜欢框架和组件库的原因之一——构建正确的东西需要大量的工作！但是看到有人记录整个过程真的很酷。

##### [web assembly 是 Java 小程序& Flash 的回归吗？](https://words.steveklabnik.com/is-webassembly-the-return-of-java-applets-flash)

看看 WebAssembly 与之前将 web 引入更具原生风格的编程的尝试有何不同。对我来说，真正突出的是这些年来我们对标准和标准化过程了解了多少。作为一个行业，我们似乎越来越擅长创建网络技术和技术平台。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】