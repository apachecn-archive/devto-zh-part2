# 为 Cogear 创建插件。射流研究…

> 原文：<https://dev.to/cogear/creating-a-plugin-for-cogearjs-25h4>

[齿轮。JS](https://cogearjs.org) 是一个用[节点构建的现代静态网站生成器。JS](https://nodejs.org) 并基于 [Webpack](https://webpack.js.org) 。

如果你错过了 **Cogear 的介绍。JS** 文章系列，不客气:

[![codemotion image](img/0c21e6138639dc3e48daa6719734be56.png)](/codemotion) [## 齿轮。JS —现代静态网站生成器

### 德米特里·别利亚耶夫

#opensource#node#webpack#cogearjs](/codemotion/cogearjs--modern-static-websites-generator-450a)
[![codemotion image](img/0c21e6138639dc3e48daa6719734be56.png)](/codemotion) [## 用 Cogear 建立一个博客。射流研究…

### 德米特里·别利亚耶夫

#node#cogearjs#webpack#javascript](/cogear/creating-a-blog-with-cogearjs-21af)
[![codemotion image](img/0c21e6138639dc3e48daa6719734be56.png)](/codemotion) [## 部署 Cogear。JS 生成的站点添加到 Now.sh

### 德米特里·别利亚耶夫

#node#cogearjs#bash#devops](/cogear/deploying-cogearjs-generated-site-to-the-nowsh-367m)

是时候谈谈制作插件了。

让我们从给插件下定义开始。

> 插件是一个模块，可以挂钩到系统核心来改变其行为。

**节点。JS** 有第一方[event emitter](https://nodejs.org/api/events.html)——一个巨大的 API 表面，具有同步事件发射、魔法错误事件和检测到的有缺陷的内存泄漏。**齿轮。JS** 使用了一个名为[emit ery](https://github.com/sindresorhus/emittery)的小库，它没有这些特性。

**齿轮。JS** 核心是由模块构建的。看一看[存储库中的主脚本文件](https://github.com/codemotion/cogear.js/blob/master/lib/cogear.js)以及它插入的所有模块。

### 来源`./lib/cogear.js`

[![Source of ./lib/cogear.js](img/6bc2da4edea38701d4563b7992fabb00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---wSD9zTl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3az9aq4mxh88vwmrzrmv.png)

此外。JS main object 类是全局可用的，并且扩展了 Emittery，因此可以在整个代码中调用事件。

**齿轮。JS** 作为 **Emittery** 的继承者，有两种方法——`on`和`emit`。使用`on`方法，你可以为指定的事件添加回调函数。使用`emit`方法，你可以带参数或不带参数调用这个事件。

在[发布文档](https://github.com/sindresorhus/emittery)中阅读更多信息。

可以很容易地从全局可用的`cogear`实例的上下文中获取参数。或者您可以手动传递它们。

[![Cogear on ane emit methods](img/d80bcd43f2f5bfe51c31334544015240.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--db7ASp-j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yvvbjdysc24wlfz03i3g.png)

要查看所有事件，请在 GitHub 存储库上使用[搜索](https://github.com/codemotion/cogear.js/search?utf8=%E2%9C%93&q=cogear.emit&type=)。它将显示所有您可以挂接的事件。

那么什么是 **Cogear 中的插件呢。JS** 环境？这是一个简单的对象，只需要一个方法，叫做`apply`。

为了让你进一步学习，我鼓励你看一段来自我们 YouTube 频道的新视频:
[https://www.youtube.com/embed/V427GA4BZHk](https://www.youtube.com/embed/V427GA4BZHk)

它将揭示插件制作的主题。

你也可以进入适当的文档部分:
[【https://cogearjs.org/docs/plugins】](https://cogearjs.org/docs/plugins)

并在 awesome-cogear 资源库中发现插件:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[code motion](https://github.com/codemotion)/[awesome-cogear](https://github.com/codemotion/awesome-cogear)

### 最佳插件、主题等列表。

<article class="markdown-body entry-content" itemprop="text">

# 真棒-cogear

最佳插件和主题 [**Cogear。JS**T3】](https://cogearjs.org)

# 插件

## 资产

*   [Compressor](https://github.com/codemotion/cogear-plugin-compressor)–使用 gzip 压缩页面和资源，以加快加载速度。
*   [页面 JSON](https://github.com/codemotion/cogear-plugin-pages-json)——将页面存储到`pages.json`。可用[反应](https://reactjs.org)、 [Vue 现场取用。JS](https://vuejs.org) 、[角度](https://angular.io)等。

## 录像

*   [YouTube](https://github.com/codemotion/cogear-plugin-youtube)–通过链接嵌入 YouTube 视频。

## 前端框架

*   [Vue](https://github.com/codemotion/cogear-plugin-vue)–将 Vue SFC 导入到入口点。

## 博客

*   [博客](https://github.com/codemotion/cogear-plugin-blog)–带有分页和标签的简单博客。
*   [博客-RSS](https://github.com/codemotion/cogear-plugin-blog-rss)–将 RSS 添加到博客。

# 主题

*   [默认](https://github.com/codemotion/cogear-theme-default)–默认主题。去学习如何？
*   [博客](https://github.com/codemotion/cogear-theme-blog)——没有框架的博客主题。克隆它并根据您的需要进行编辑。
*   [Bootstrap 4](https://github.com/codemotion/awesome-cogear) -各种 Bootstrap 4 主题。最适用于响应式设计(手机、平板电脑等)。)
*   [ejs](https://github.com/dtslvr/cogear-theme-ejs-bootstrap-blog)中的 Bootstrap 4——基于 ejs 的 Bootstrap 4 主题，由 [@dtslvr](https://github.com/dtslvr) 提供

# 事先调整

*   [博客](https://github.com/codemotion/cogear-preset-blog)–带有响应主题的简单博客预设。
*   [ejs 中的 Bootstrap 4 博客](https://github.com/dtslvr/cogear-preset-ejs-bootstrap-blog) — Bootstrap 4 博客预置，由…提供

</article>

[View on GitHub](https://github.com/codemotion/awesome-cogear)

如果你喜欢这个视频，请订阅 [Cogear。JS YouTube-channel](https://www.youtube.com/channel/UCYVvgPLMfRwA2K_ledHCZcw) 和 giva a star to the projects repository:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[codemotion](https://github.com/codemotion)/[cogear . js](https://github.com/codemotion/cogear.js)

### 现代静态网站生成器(Node.JS/Webpack)

<article class="markdown-body entry-content" itemprop="text">

# 齿轮。JS-现代静态网站生成器

[需求](https://raw.githubusercontent.com/codemotion/cogear.js/master/#requirements) | [安装](https://raw.githubusercontent.com/codemotion/cogear.js/master/#installation) | [使用](https://raw.githubusercontent.com/codemotion/cogear.js/master/#usage) | [选项](https://raw.githubusercontent.com/codemotion/cogear.js/master/#options) | [网站](https://cogearjs.org)

# 关于

**齿轮。JS** 是用[节点构建的静态站点生成器。JS](https://nodejs.org) 并基于 [Webpack](https://webpack.js.org) (v4.6)。

它受到 Jekyll 和其他人的启发，但建立在最新的前端技术之上。

提供出色的动态热重装体验，可即时实施任何更改。

# 介绍视频

[![Introduction to Cogear.JS](img/acee6f42d9ce86c915917cff17931f2e.png)T2】](https://youtu.be/nDga67P6lag?list=PLBuIATAeU0NWhrpWnI--TRG76DwAgem1I "Introduction to Cogear.JS")

## 特征

*   <g-emoji class="g-emoji" alias="desktop_computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f5a5.png">🖥</g-emoji> **现代堆栈技术**用捆绑的脚本和样式构建现代静态网站。快速制作原型并立即部署到服务器上。使用**任何现代前端栈** ( **webpack** 捆绑)–[Vue。JS](https://vuejs.org) 、[反应](https://reactjs.org)、[角度](https://angular.io)、[余光](https://emberjs.org)等。
*   <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji> **超快可靠**每秒执行近 1.000 页(取决于页面内容和原始计算机处理器能力)。在线。服务器每秒钟可以处理数千个请求来服务静态文件(甚至在微型 VPS 上)。
*   <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji> **对于任何主机**都不需要**任何数据库**(数据存储在平面…

</article>

[View on GitHub](https://github.com/codemotion/cogear.js)

# 加入社会影响力 Hacktoberfest 挑战赛

[![Join the Social Impact Hacktoberfest Challenge](img/12d24b2f1ca6434c86e208aa1cd08868.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0yMnaXLc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/121322/46319645-ab50e200-c58f-11e8-8dd0-cf7a4b1a610e.png)

通过向 [Cogear 投稿，了解如何赢取参加](https://cogearjs.org) [Hacktoberfest](https://hacktoberfest.digitalocean.com) 的超酷 t 恤。JS 。

现在，我们鼓励开发人员:

1.  [为 React](https://github.com/codemotion/cogear.js/issues/6) 创建一个插件(只需给 Webpack 添加一个加载器)。
2.  [创建一个自举 4 主题](https://github.com/codemotion/cogear.js/issues/7)。