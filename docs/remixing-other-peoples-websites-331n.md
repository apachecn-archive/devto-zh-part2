# 转载别人的网站

> 原文：<https://dev.to/moopet/remixing-other-peoples-websites-331n>

# 为什么你想改变你访问的网站的用户界面？

嗯，有很多原因:

*   个性化您的体验
*   删除不需要的内容
*   添加您自己的菜单项
*   在浏览器中模拟 UI 改进，记录您的结果，并将它们作为改进建议提交给现有项目

...请自便。

## 基本站点修改

修改网站最简单的无代码方式就是从页面中删除不需要的元素。这可以通过代码来实现，但是当你可以重新使用广告拦截器的时候，这往往是多余的。为此，我使用了[子块原点](https://github.com/gorhill/uBlock/)。

虽然这很快也很容易，但它只能让你删除你不感兴趣的东西。你得到了一个更加定制化、更加简化的体验，但是你没有得到任何新的东西。

### 举例:清除[微软 office online](https://www.office.com) 中的杂物。

所以这个:

[![Office.com before](img/ba42837adeea53010075c145a5f80db9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7vmkKQ5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x08cs31nyqdcaeb99twk.png)

变成了这样:

[![Office.com after](img/dc9784fb7263c0575f0246c1a9d8e974.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gaq5txkg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gv9f6uwse2zvqs4zkluv.png)

你可以看到我已经抛弃了英雄形象(这意味着当我使用笔记本电脑时，我不得不向下滚动每一页),还删除了我永远不会使用的办公产品。追加销售 CTA 链接也是如此。

## 高级站点修改

阻止元素是一回事，但是增加功能是完全不同的另一个层面。

为此，我们将使用“用户脚本”，这是一些自包含的 javascript 代码片段，当页面 URL 匹配某个模式时，它们会自动触发自身。

有了这些脚本，您可以对站点的行为方式进行一次大检查。你可以把它锤成你想要的形状。

现在，用户脚本已经存在很长时间了，在 web 扩展变得越来越容易和越来越可移植的世界里，这似乎有点过时了。为什么要用这么粗糙的工具？嗯，因为它又快又简单，除了普通的日常前端 Javascript 之外没有什么需要学习的。我听到你问为什么不使用书签？主要是自动化和社区。

## 让你开始的一些想法

### 让你最喜欢的网站以你最喜欢的窗口大小工作

厌倦了使用不能正常响应的网站？

*   移除不需要的元素(观察列表、新闻、侧边栏中的所有内容、IMDB Pro 的广告、megamenu 等)。)
*   重新设计搜索表单的样式，使其保持在窗口顶部
*   修复天真地隐藏侧边栏导致的宽度和填充问题(见“之前”图像的右侧)

所以这个:

[![IMDB before](img/1ad623fcf6987d1ef93747a84d66a367.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jV_1QA3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gu408bs5nfu4ddpfrt0t.png)

变成了这样:

[![IMDB after](img/b7587aa2ddbe1c6506d39faf21f99617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--evTGdIzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ji784urkdkdigk9zhyfq.png)

### 变分页为无限滚动

你喜欢无限滚动吗？

*   写一个函数来加载一个页面，并把它的一部分注入到当前页面
*   隐藏现有导航
*   如果页面在底部，并且有一个“下一页”链接，则触发滚动功能

### 修复在新标签页中打开每个链接的站点

一些网站(比如 Quora)坚持在一个新标签中打开几乎所有的链接。这打破了网络的工作方式，我个人觉得这很烦人。

*   绑定到每一个锚，防止它冒泡
*   将当前 URL 推入浏览器的历史记录中
*   直接更新`window.location`

### 隐藏不能用选择器或 xpath 规则阻止的元素

一些网站使用产生“div 汤”的框架，并且没有一致的类名或 id。有时这只是构建过程中的一个产物，有时这是故意让人们更难检测和阻止不想要的内容。

[![Reddit](img/eacb208400f57aa7cadb107472fdadda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dm5ot7si--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl4zjv1b8diro7q6atnq.png)

假设你从未对从众感兴趣。你在 Reddit 上，你想要一个更加不受干扰的体验。

如果您知道某个元素包含文本“趋势社区”。

*   编写一个函数来检查每个 div 中的已知文本
*   沿着它的父节点向上移动，直到到达想要隐藏的级别，然后这样做
*   标记你检查过的每一个 div，这样你就不会再检查了
*   如果 DOM 改变了，或者没有更好的方法，用`setInterval()`重新触发脚本。

## 工具

最初的标准是油滑猴，但现在已经被[坦佩猴](https://tampermonkey.net/)和[暴力猴](https://violentmonkey.github.io/about/)超越。尽管名字不吉利，我还是推荐 Violentmonkey，因为它是麻省理工学院许可的。Tampermonkey 是目前最受欢迎的用户脚本管理器，但它是闭源的，不能保证你的隐私。就有效性而言，用哪个都无所谓；它们都可以运行用户脚本，并且都实现了原始的 Greasemonkey API。

## API

[Greasemonkey API](https://wiki.greasespot.net/Greasemonkey_Manual%3aAPI) 允许你做一些你通常不能做的事情，比如包含任意的远程脚本。大部分时候不需要了解什么。

很多人用`@require` front matter 来包含 jQuery，比如。你不必，我只是说。

## 共享

有一些用户脚本的公共存储库，比如[油腻的叉子](https://greasyfork.org/)，它们通常是获得想法的好地方。

## 安全

如果你要使用其他人的用户脚本，这些插件会让你在安装前检查它们，很明显脚本实际能做什么有一些限制——但是总有可能有人在分发恶意代码。或者，假设他们满是 bug 的努力会有危险的副作用。

实际上，这并不比从 Arch 用户库或 NPM 或任何数量的用户贡献包系统安装软件更危险。

精明购物。

封面图片来自安德鲁·贝尔勒[免费图片](https://www.freeimages.com/photographer/andrewatla-57677)