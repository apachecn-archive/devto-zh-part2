# 独特、可爱的网页

> 原文：<https://dev.to/genejams/unique-lovely-web-bits--dfk>

 <small>虽然这篇文章中的主题可能仍然是真实的，但是这些内容引用了一个旧版本的 *zuix.js* 库。最新文档见 [zuix.js](https://zuixjs.org) 网站。</small> 

* * *

昨天我想完成我上一篇文章中使用的动画按钮`hamburger_icon`的文档。

[![genejams](img/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 从头开始移动 web 应用程序布局

### { Gene } Jul 22 ' 182min read

#showdev #webdev #javascript #zuixjs](/genejams/mobile-app-layout-from-scratch--5ch9)

正如经常发生的那样，记录事情将有助于改进它们，这将需要一些最后一刻的改变，在这种情况下，是关于添加额外的选项来选择不同的汉堡动画和颜色。结果可在[这里](https://zuixjs.github.io/zkit/docs/components/hamburger_icon)得到。

但是等一下...我在 *Glitch* 上的所有 3 个示例项目中使用过这个 *hamburger_icon* 按钮，也在 GitHub 页面上的几个其他项目中使用过。

> 这是否意味着我必须复制所有使用 *hamburger_icon* 的不同项目的新代码，并再次发布它们？

[![Oh Sh*t!](img/983c319da889b30f538ee945c6a7a0a1.png)T2】](https://i.giphy.com/media/E2USislQIlsfm/giphy.gif)

幸运的是,**不是这种情况,**因为这些项目中没有一个持有被使用的位的拷贝，而是直接链接到每一个的原始源代码。

## 唯一位

这也是我们在我上一篇文章的最后一个例子中用来实现标题栏的`HTML`代码中可以看到的:

```
<header data-ui-include="//zuix-app-1.glitch.me/layout/header"></header> 
```

Enter fullscreen mode Exit fullscreen mode

<small>一个来自**index.html**的“比特”在 **zuix-app-3** .glitch.me</small> 上主持

`data-ui-include="//zuix-app-1.glitch.me/layout/header"`意味着`layout/header`是直接从托管第一个示例的服务器上获取的，它由以下两个文件组成:

*   https://**zuix-app-1**. glitch . me/layout/header . html
*   https://**zuix-app-1**. glitch . me/layout/header . CSS

但是当我们看着`header.html`时，我们看到了

```
<div class="logo">
    <a href="/">app-logo</a>
</div>
<div data-ui-load="@lib/components/hamburger_icon"
     data-ui-options="options.menuButton"
     class="menu-button"></div> 
```

Enter fullscreen mode Exit fullscreen mode

其中`data-ui-load="@lib/components/hamburger_icon"`意味着，这个头将依次从`@lib`加载`components/hamburger_icon`(这是 [zKit](https://zuixjs.github.io/zkit) 组件收集服务器的快捷方式)。

所以`zuix-app-3`包含了来自`zuix-app-1`主机的`layout/header`,并且还加载了来自`zuixjs.github.io/zkit`的 *hamburger_icon* 组件。

这意味着如果在主机`zuix-app-1`上修改了`layout/header`，它将在`zuix-app-2`和`zuix-app-3`上自动更新，也意味着每当在*zuixjs.github.io/zkit*上更新`components/hamburger_icon`时，它将在*故障*上托管的所有实例上无缝地即时更新！

[![What?!@#?](img/72e75ac417cbe98427e9ee09396a5765.png)T2】](https://i.giphy.com/media/glmRyiSI3v5E4/giphy.gif)

因此，使用对组件的唯一引用对生产力和创造力都有明显的好处，无论它们是托管在同一个服务器上还是托管在一堆不同的服务器上。

## 可爱，但是...

为了使这个工作没有问题，我们必须保证 100%的向后兼容性，只要分布式组件被更新。如果这是不可能的，那么必须为更新的组件分配一个新的路径。

此外，我们必须考虑到，从多个可靠性不确定的主机下载资源有时会影响性能，尤其是在存在网络问题的情况下。这是通过使用*cdn*得到的一个老教训。

因此，当您的应用程序真正关心性能时，可以采用不同的策略，这包括下载所有使用的资源并将它们打包到一个本地文件中。

但是在你开始认为我将要谈论构建系统、包管理器、web 打包器等等之前，我想向你保证它不是。

嗯，这也是一个选择，但是如果我们还不想处理那些构建工具，通过使用 *zuix.js* 我们可以直接在浏览器控制台中生成应用包。

[![Impossible](img/dfce1aad47d5024c10000615eb48e89a.png)T2】](https://i.giphy.com/media/xTiTntReleqBnhBNwQ/giphy.gif)

这就是我所说的**浏览器内捆绑**(或者客户端捆绑)，但是我稍后会写这个。

接下来阅读:

[![genejams](img/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 向您的 web 应用程序添加 ViewPager

### { Gene } Jul 28 ' 182min read

#showdev #webdev #javascript #zuixjs](/genejams/adding-a-viewpager-to-the-mobile-web-app-n67)