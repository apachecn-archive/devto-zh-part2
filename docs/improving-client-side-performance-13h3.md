# 提高客户端性能

> 原文：<https://dev.to/teej/improving-client-side-performance-13h3>

这最初是在我的博客上发布的。

下面是我发现的一些优化站点性能的方法，当它为生产环境做好准备时，或者修补现有的站点时。在可能的情况下，我会列出一些常见的实现，尽管可以理解的是，我们并不总是拥有我们想要进行更改的那种访问。

这些客户端改进的机会通常更常见于网站，而不是 web 应用程序。我认为这是因为现代 web 应用程序开发所带来的工具，例如， [vue-cli](https://github.com/vuejs/vue-cli) 处理代码分割，并生成一个服务工人。有了像 webpack 或 Gulp 这样的构建工具，这也意味着你可以在进程中的某个地方安装一个插件来处理这些问题。其他人需要一些关心和同情。

## 图像

一幅画描绘了一千个单词，但当这些单词中有许多是“慢”的同义词时，那就不好了。人们并不总是意识到图像的细节，在某些情况下他们也不应该意识到。如果上传一张 5mb 的图片是可能的，那么它很可能会在某个时候发生。事情就是这样。

我们可以使用一些方法来自动减少整体膨胀，同时仍然保持图像质量。在自动化并不总是可用的地方(比如静态网站，或者非基于 CMS 的项目),我们仍然可以使用大量的工具。

### 调整大小

调用的第一个端口是调整图像的大小。一般来说，对于只占页面一部分的东西，或者不是页面存在的原因的东西，我们不需要比通常分辨率更高的图像。如果摄影是你的工作，那么你可以提供一个较小的图像，如果他们愿意，可以选择观看高分辨率的版本。

如果你搜索“调整图像大小”,你会发现一大堆在线解决方案可以帮你完成这项工作。如果您在任何给定时间只关心一两张图片，这很好，但是如果您有一个装满它们的文件夹呢？

#### 在 MacOS 上批量调整大小

MacOS 自带一个名为 Preview 的程序，可以让你批量调整图像大小。为此，选择您的所有图像，右键单击并打开预览。

从这里您将看到预览窗口:

[![Preview app](../Images/bced102045696a67055dac6329357365.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jfzBfTrq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bz55errz5tpqc0624tu6.jpg)

按下 CMD + A，您可以选择所有的图像，然后前往工具>调整大小。出现的对话框将让您设置宽度和高度的值，尽管您可能只设置宽度，并让它为您缩放图像，这样图片的任何部分都不会被剪切。

[![Preview app resize dialogue](../Images/8b7b07a4e186eced390bf4468bb1255e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_du76vyz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jcmc9wfqabvnmjpycpk1.jpg)

#### 在窗口上批量调整大小

我已经有一段时间没有在 Windows 机器上工作了，所以请原谅我的无知。

我见过的一个解决方案是[将你的图像作为邮件附件](https://www.easytechguides.com/resize-pictures.html)发送，这时你会看到一个选项来调整它们的大小并保持它们的纵横比。

另一个可能更理想的选择是一个叫做 [ImageResizer](https://github.com/bricelam/ImageResizer) 的软件。当您选择一组图像并右键单击时，它会从出现的上下文菜单中添加一个调整大小的选项。

#### CMS

WordPress 自带了对调整图片大小的内置支持。如果你在 WordPress 上使用 Timber(放手吧，它是一流的)，那么你也可以[动态调整图像大小](https://timber.github.io/docs/reference/timber-imagehelper/#resize)。

如果你使用的是 Craft CMS，你可以在你的控制面板和模板中[调整图片大小，设置图片质量，改变图片格式](https://docs.craftcms.com/v3/image-transforms.html)。

对于 ExpressionEngine 来说，一个非常强大的插件是 [CE Image。如果你正在做一个大项目，那么要价是值得的。否则，控制面板中有一些调整大小的功能。](https://docs.causingeffect.com/expressionengine/ce-image/index.html)

#### 云

根据您的需要，这种方法可能是管理图像的好方法。如果你同意用另一个服务存储你的图像(假设你在某个地方有你自己的备份)，那么免费层的 [Cloudinary](https://cloudinary.com/) 将会非常有吸引力。一旦你上传了一张图片，你就可以操作 URL 来进行修改，比如调整大小或者转换成另一种格式。

### 优化

如果你还没有走上云或 CMS 的道路，那么我们仍然需要进一步优化。

一旦图像被调整到你喜欢的大小，下一步就是分析它们，找出任何可以删除以减小文件大小的碎片。一些方法包括:

*   删除可能已从拍摄原始照片的相机中附加的元数据
*   优化 JPEGs 以减少渲染图片所需的颜色数量
*   如果不需要透明，则从 PNG 转换为有损格式

早在软件出现之前，我们就已经不得不用图像来讨价还价，用像素来交换。我们会进行长时间的讨论，试图说服它 80 度的红色看起来和 200 度的一样好。或者 JPEG 比 PNG 更适合它。对所有参与者来说，这是一段艰难的时期。

幸运的是，有工具可以让这变得快速而简单。我们很开心，照片也很开心。

#### 减小文件大小

ImageOptim 是我最喜欢的优化图像的工具。你把你的图片拖到程序上，程序会自动完成剩下的工作，默认情况下会替换现有的图片。它还让你知道节省了多少钱。如果您不满意，您可以更新首选项以进行更积极的优化。

[![ImageOptim app results](../Images/76b76e02ed7eee7d7cef55757ccb7cc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GvEGtjHI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l6cqdejpj677ay82dvb8.jpg)

ImageOptim 网站有一个页面为其他平台提供建议。再次为我以 MacOS 为中心的心态道歉。

#### 将 PNG 转换为 JPEG

如果您的图像不需要透明，您可以将它们转换为 JPEG，并获得较小文件大小的好处。

在 MacOS 上，你可以像以前一样在预览中打开图像，然后在选择所有内容后，你可以进入文件>导出所选项目。在出现的对话框中，左下角有一个标签为“选项”的按钮。在这里，您可以选择不同的格式。

[![Converting PNG to JPEG with the Preview app](../Images/482dd75d5769b33516ad23dd5a58275a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wp2s9bS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0no4lhhz4fa1y3ufw6a7.jpg)

也有很多在线工具可以做到这一点。在 Windows 上，一个选择可能是像 IrfanView T1 这样的软件，我相信 T3 可以让你批量转换图像。我不知道我是否对 Windows 用户说过，但是我很抱歉。我甚至还没有向 Linux 用户道歉。

### 反应灵敏

让您的图像更具响应性不仅仅需要:

```
img {
  max-width: 100%;
  height: auto;
} 
```

当然，它会调整大小，但是你可能会浪费很多带宽。如果我需要便利贴，我不会把 A4 纸折叠成小方块；那将是浪费纸张。

如果我们遵循上面的方法，我们可以组合不同大小的图像，并为用户提供合适的图像。

为此，我们可以使用`<picture>`元素或者`srcset`属性。有了`<picture>`，你就给了*指令*给浏览器在给定的断点处呈现特定的图像。有了`srcset`，你就把*信息*交给了浏览器，相信它能在任何情况下提供最合适的图像。这两者也可以一起使用，以获得更好的控制。

使用`<picture>`非常适合艺术指导这样的情况，当在较小的屏幕上图像可能太小而没有意义，所以你想提供不同的图像，或原始图像的裁剪版本，以保留原始含义。

来自响应式图像社区团体网站的示例:

```
<picture>
  <source media="(min-width: 40em)"
    srcset="big.jpg 1x, big-hd.jpg 2x">
  <source 
    srcset="small.jpg 1x, small-hd.jpg 2x">
  <img src="fallback.jpg" alt="">
</picture> 
```

`srcset`属性意味着你对用户最终会看到什么没有多少控制权，所以最好把它留给调整过大小的图像。这可能取决于他们的屏幕大小，甚至是他们的网络能力。

另一个来自 Responsive Image Community Group 网站的例子:

```
<img src="small.jpg"
     srcset="large.jpg 1024w, medium.jpg 640w, small.jpg 320w"
     sizes="(min-width: 36em) 33.3vw, 100vw"
     alt="A rad wolf"> 
```

要了解更多信息，我建议查看... [Responsive Images 社区团体网站](https://responsiveimages.org/)，因为它们更详细地介绍了使用和用例。 [Smashing Magazine](https://www.smashingmagazine.com/2014/05/responsive-images-done-right-guide-picture-srcset/) 也有很好的概述。

### 懒装

因此，您可以调整图像的大小，并使用相应的图像标签进行优化。我们还能做什么？这并不能保证用户会看到页面上的每一张图片，所以为什么要让他们下载所有的东西呢？以旋转木马为例。我知道人们有自己的看法，但我不会干涉。你做你的。但是有多少人会看到旋转木马的每一张幻灯片呢？我们可以在新幻灯片到达时加载图像。

滚动也是一样，因为这样或那样的原因，不是每个人都能做到页面底部。

迄今为止，我最喜欢的解决方案是 [LazyLoad](https://github.com/verlok/lazyload) 。

```
<img class="lazy" alt="..."  data-src="../img/44721746JJ_15_a.jpg">
<noscript>
  <!-- If JavaScript isn't available -->
  <img alt="..." src="../img/44721746JJ_15_a.jpg">
</noscript> 
```

```
var myLazyLoad = new LazyLoad({
    elements_selector: ".lazy"
}); 
```

这同样适用于`<picture>`和`<img>`中的`srcset`属性。

## 第三方插件/脚本

你会惊讶地发现在一些网站上为各种任务加载了多少外部脚本。有些是不可避免的，比如分析，但是有些对于它们所提供的功能并不是绝对必要的。

### 共享小工具

这是一个常见的例子，有人希望有按钮可以随时随地在任何他们想要的社交媒体平台上分享页面。不过，你不需要一个外部服务将 JavaScript 放入你的站点来实现这一点。

你可以让它与 HTML 一起工作，或者使用提供的 CSS，或者使用你自己的 CSS 与网站的其他部分融合。为此，您可以使用 [Sharingbuttons.io](https://sharingbuttons.io/) 或[简单共享按钮](https://simplesharingbuttons.com/)。两者都提供了一种简洁的方法，而后者也有一个 JavaScript 版本，可以自动收集相关的页面信息进行共享。如果你使用的是内容管理系统，你应该通过在当前页面的共享 URL 中填充信息来摆脱它。

### 社交媒体源

好吧，这个有点复杂...您可以包含没有 JavaScript 的社交媒体提要，但是您需要某种服务器端访问来呈现它们。你可以使用给定的社交媒体软件开发工具包或插件来开发自己的应用。这样做的额外好处是可以根据你的网站设计风格。

我对这一部分没有任何建议，因为我最终为 Craft CMS、ExpressionEngine 和 WordPress 的工作项目推出了自己的产品。我使用了 Twitter 和 Instagram 的 PHP SDKs。所以这是一次有趣的学习经历。

使用 WordPress 更简单，因为你可以创建一个 PHP 文件并将其包含在你的`functions.php`文件中。对于 Twitter，我使用了 [twitter-api-php](https://github.com/J7mbo/twitter-api-php) 包来收集一个账户的最新推文。

不过，我相信有适合你的 CMS 选择的解决方案，我会让你去寻找一个合适的。这并不容易，我只是没有使用这些插件的经验。希望这能激发一些关于如何加速你的项目的调查。

## 捆绑资产

如果你不使用网袋，大口，包裹等...为了打包所有的依赖项，您可能会在浏览器中看到额外的请求。以我的经验来看，这种情况大多发生在 WordPress 网站上，插件将自己的样式和脚本放到你的模板中。

WordPress 用户欣喜地发现，你可以从许多解决方案中进行选择，例如[自动优化](https://wordpress.org/plugins/autoptimize/)和 [W3 总缓存](https://wordpress.org/plugins/w3-total-cache/)，它们带来的不仅仅是组合资产。

我不知道其他平台的最新插件解决方案，但如果我遇到了，我会在这里添加它们。

如果你正在使用 npm，一个快速的解决方案可能是使用 [UglifyJS](https://github.com/mishoo/UglifyJS2) 包。所以在运行完`npm install uglify-js`之后，你可以给你的`package.json` :
添加一个新的`script`

```
"scripts":  {  "build":  "uglifyjs src/vendor/jquery.js src/main.js -o dist/bundle.min.js -c -m"  } 
```

然后，命令`npm run build`将组合并缩小您的 JavaScript。`-c`和`-m`标志会压缩和破坏你的代码。

## 服务人员

使用服务工作者，我们可以拦截网络请求并提供缓存资产。我们能做的是，一旦用户访问了网站，缓存样式，脚本，字体等...那么来自该用户的任何后续请求都将从缓存中得到服务。一旦我们进行更新，我们可以使服务工作者无效，并且新的资产将被再次下载。

目前对于这个网站，我使用的是 [SW Precache webpack 插件](https://github.com/goldhand/sw-precache-webpack-plugin)。这让我可以在将产品投入生产时使用 webpack 生成一个服务工人。我发现这对于字体来说特别有用，可以避免无样式文本的闪现。

这是我网站上的配置:

```
new SWPrecacheWebpackPlugin({
  cacheId: 'tj-ie-v3',
  filename: 'service-worker.js',
  minify: true,
  stripPrefix: 'static',
  staticFileGlobs: [
    'static/fonts/*.woff2',
    'static/fonts/*.woff',
    'static/js/main.js',
    'static/css/main.css',
    'static/js/chunks/*.js',
  ]
}) 
```

这极大地提高了性能，尽管如果您做了一些剧烈的更改，用户可能会看到一个损坏的站点，直到他们重新加载。也许有一个解决方案，但是在我的服务人员生涯中，我还没有走到那一步。

如果你想更深入地了解它是如何完全工作的，谷歌有一个介绍值得一看。

## 奖金研究

在写这篇文章的时候，我意识到了[渐进式工具](https://progressivetooling.com/)，它看起来是围绕这个主题的工具的一个很好的资源。

如果您想获得更多性能改进，请查看以下内容:

*   [link rel="preload"](https://developers.google.com/web/updates/2016/03/link-rel-preload) 允许您识别优先资产以提高加载性能
*   [Cloudflare](https://www.cloudflare.com/) 利用全局 CDN 来提高性能。它还有一个慷慨的免费提供，这使得它值得一试。

我只有这些了！我会更新我得到的任何新的见解，但希望这里有一些有用的信息。