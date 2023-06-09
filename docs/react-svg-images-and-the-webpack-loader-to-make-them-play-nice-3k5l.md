# React、SVG 图像和 webpack 加载器，使它们播放得更好

> 原文：<https://dev.to/paigen11/react-svg-images-and-the-webpack-loader-to-make-them-play-nice-3k5l>

[![Laptop screen filled with code sitting on a couch](img/57b13684c07280be41a565d0c5bed837.png "Laptop screen filled with code sitting on a couch")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ABUbZHOR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.paigeniedringhaus.com/static/02176e36f46c03707a991bdf294d1856/15ec7/laptop-on-couch.jpg)

*Goran Ivos 在 Unsplash 上拍摄的照片*

## 简介

这并不是一个新问题——让 SVG 用 React 正确渲染的困境。但这是一个我直到最近才不得不处理和克服的问题，我想分享我的经验，希望它能节省其他人搜索 Github、StackOverflow 和 npm 的几个小时。

在我目前的公司，我为两个独立的开发团队工作，他们都使用 JavaScript 微服务作为他们的 ui:一个使用 AngularJS，另一个使用 JavaScript MVC。所有的后端服务都是 Java Spring Boot 应用程序。

最近，有消息称谷歌将终止对 AngularJS v1.5 的长期支持——我们正在运行的版本。

很快就清楚了，我们有三个选择:

1.  将系统升级到 [AngularJS 1.7](https://blog.angular.io/stable-angularjs-and-long-term-support-7e077635ee9c) (将继续提供 3 年以上的长期支持)，
2.  将我们的应用程序升级到 Angular 2+，
3.  或者迁移到竞争对手的 JavaScript 框架:ReactJS。

我和我的一个同事负责找出哪一个是最佳方向。这个探索过程值得在自己的博客上发表，但可以说，我最喜欢冒险进入 React(我更喜欢语法)。在我们查看了我们的选项并进行修补后，我们决定看看是否可以在现有应用程序的 React 工作中进行一次小型的概念验证(POC )(因为我们需要两个 ui 在迁移完成前相互路由)。

在开始我们的 React POC 大约 4 小时后，我们有了一个可以工作的应用程序，尽管不是很漂亮。我们添加了 Jest 和 Enzyme 单元测试、Cypress 端到端测试等等。但是有一件事困扰了我们将近两个星期:让 SVG 图形在 DOM 中正确呈现。

今天，我将向您展示我是如何让 SVG 与 React 应用程序一起工作的。

* * *

## 为什么是 SVGs？有什么好处？为什么不直接做成 PNG 呢？

问得好。使用 SVG 的好处有很多，如果你不知道的话，SVG 代表 [**可缩放矢量图形**](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics) 。虽然教科书上的定义听起来很深奥:

> “SVG 是基于 [XML](https://en.wikipedia.org/wiki/XML) 的[矢量图像格式](https://en.wikipedia.org/wiki/Vector_image_format)，用于支持交互性和动画的[二维](https://en.wikipedia.org/wiki/Two-dimensional)图形”——[维基百科，可扩展矢量图形](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics)

它们基本上取代了 JPEG、PNG、TIFF 或 GIF 等其他格式的传统图像。

以下是 SVG 优于其他图像格式的几个原因:

*   **无论屏幕大小、缩放级别或图像大小，都有清晰的分辨率** —由于 SVG 是基于矢量路径、形状和填充构建的，因此它们可以缩放到任何大小而不会损失清晰度。另一方面，png 是基于像素的，不会一直看起来清晰明了(尤其是在视网膜显示设备上)。
*   **加载速度更快** —如果你在你的网站上使用高分辨率图像来迎合那些 retina 显示屏，PNG 文件可能会相当大，而且越大的文件渲染越慢。SVG 只是代码，这意味着文件更小(除非你加载的东西非常详细，有一百万个向量路径)。
*   **动画是可能的** —与静态的 PNG 文件不同:所见即所得-SVG 可以用 CSS 制作动画和样式。动画、不同的颜色、状态的变化(比如鼠标悬停或点击)——所有这些都可以在 SVG 上实现。
*   **可访问性和搜索引擎优化** —谷歌将 SVGs 编入索引，这意味着你的网站可以获得更好的搜索引擎优化分数，对于访问你网站的有视觉障碍的人来说也更容易访问。

### 正确。那么 SVG 就是了，现在如何在 React 中使用它们

在我的项目中，我试图在 React 中重新创建现有 AngularJS 应用程序的一个更简单的屏幕。这个屏幕包含四个大的、可点击的卡片，将用户带到其他不同的屏幕，每个卡片都有一个漂亮的 SVG 文件，与单词相关联。

所以我将原始项目中的 SVG 文件复制到 React 应用程序中，将文件导入到将呈现它们的组件中，并告诉 DOM 呈现它们:

```
<Image source={require("../img/key.svg")} /> 
```

Enter fullscreen mode Exit fullscreen mode

在 JSX，我添加了 CSS 样式，等待 webpack 进行神奇的解析...没什么。嗯，不是什么都没有，控制台中出现关于无法识别的文件类型的错误，但肯定没有 SVG 钥匙形状的图标显示在 UI 中。

因此，我开始寻找在 React 中正确呈现 SVG 所需的库。这是一个长达数天的过程。如果您不知道，大约有 7，845，923 个 npm 模块都声称可以让 React 很好地与 SVG 配合使用。其中一些可能有用，我尝试的前 10 个左右没有用。

### 然后… SVG 内联加载器

后来我找到了。一个被恰当地命名为 [`svg-inline-loader`](https://www.npmjs.com/package/svg-inline-loader) 的 npm 模块，它有像样的文档，它有 3200 次周下载，它在 Github 上有 325 颗星，*它是由构建 webpack* 的核心团队开发和维护的——它看起来很有前途。

所以我试了一下，把它保存到我的`package.json`文件里的`devDependencies`里。

```
$ npm install svg-inline-loader react-inlinesvg --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后将配置对象添加到`webpack.config.js`文件:

```
{
    test: /\.svg$/,
    loader: "svg-inline-loader"
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经准备好了。对 webpack 的一个简单更新，对 JSX 语法和...

```
import React, { Component } from 'react';
import KeyImage from '../img/key.svg'; 
import SVG from 'react-inlinesvg';
class Card extends Component(){
return(
    <div>
      < /* other UI stuff here */ > 
      
    </div>  
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

BOOM — SVG 显示！

我还想补充一点，这个 npm 模块有一些非常漂亮的额外特性:它可以从 SVG 中删除额外的标签信息(这在用 Sketch 或 Adobe 生成 SVG 时经常发生)，它可以添加类或 id 来防止 SVG 冲突。很好。

就这样。没有 CSS 变化，没有超级怪异的语法，什么都没有。只是`svg-inline-loader`，一个 webpack 的额外模块加载器，问题就解决了。

感谢你的阅读，我希望它能像帮助我一样帮助你。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

* * *

## 进一步引用&资源

*   [可缩放矢量图形](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics)定义
*   [svg-inline-loader](https://www.npmjs.com/package/svg-inline-loader)
*   [你应该使用 SVG 而不是 png 的 5 个理由](https://watb.co.uk/5-reasons-you-should-be-using-svgs-over-pngs/)