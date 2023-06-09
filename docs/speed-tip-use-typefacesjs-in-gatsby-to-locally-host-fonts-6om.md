# 速度提示:在 Gatsby 中使用 Typefaces.js 在本地托管字体

> 原文：<https://dev.to/stoutlabs/speed-tip-use-typefacesjs-in-gatsby-to-locally-host-fonts-6om>

自从推出我的新的由盖茨比驱动的网站以来，我注意到只有一件事仍然在降低加载速度:通过谷歌字体导入字体！尽管字体文件通常相对较小，但这些导入会导致对谷歌服务器的额外访问...因此，出现了微小的负载阻塞延迟。(尤其是在慢速连接上。)

### typefaces . js 简介

在过去，自托管字体总是有点混乱，所以大多数人(包括我自己)只能直接从谷歌字体中导入...毕竟这是他们的建议！

谢天谢地，还有一个更优雅的 JS 解决方案:Kyle Mathews 的 [Typefaces.js](https://github.com/KyleAMathews/typefaces) 包。(是的，盖茨比的创作者之一。)假设你的网站或应用的构建过程使用了 Webpack 和 CSS 和字体的加载器，那么使用起来简单得可笑。就我而言，我想在《盖茨比》中使用它...

#### 在盖茨比中使用 Typefaces.js

这就是你要做的:

##### 1。在 NPM 上找到所需字体的字体文件。

对我来说，是:[艾米丽的糖果](https://www.npmjs.com/package/typeface-emilys-candy)和[梅里韦瑟](https://www.npmjs.com/package/typeface-merriweather)。*注意:谷歌的所有字体都可以在 NPM 上使用，因为凯尔已经通过程序添加了它们。(字体松鼠的字体也在工程中！)*

##### 2。通过 yarn 安装以上文件:

```
 yarn add typeface-merriweather typeface-emilys-candy 
```

##### 3。移除以前导入的那些字体。

(我的以前是通过“我的 SCSS”中的@import 导入的)

##### 4。将以下内容添加到`gatsby-browser.js`:

```
 require('typeface-emilys-candy');
   require('typeface-merriweather'); 
```

或者，在顶层/布局组件中导入:

```
 import 'typeface-emilys-candy';
   import 'typeface-merriweather'; 
```

##### 5。做一个全新的重建，你就完成了！

整个过程总共花了我大约 2 分钟的时间，在这个过程中，我的移动加载时间减少了大约 500 毫秒。如果你还没有在你的 Gatsby 网站上这么做，我强烈推荐你这么做！