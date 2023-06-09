# 带包裹的简单 HTML/CSS 工作流

> 原文：<https://dev.to/antjanus/simple-htmlcss-workflow-with-parcel-15ah>

最近，我开始做一个 HTML/CSS 兼职项目，我发现自己处于恐慌状态。我已经有一段时间没有做普通的 HTML/CSS 了。多年来，JavaScript 一直是我工作流程的一部分，JavaScript 的输出总是一个单页应用。

事实上，我已经很久没有在静态网站上工作了。

## 我的天真开始

但是我知道这样一个工作流，尽管非常简单，但需要一个工具链来加速我的开发，更重要的是，确保最终的输出是无错误的、正确的和跨浏览器兼容的。

当我写第一篇`index.html`时，我立刻想到，“哦，我需要一些模板”。没什么大不了的，我只需要包括页眉、页脚和其他 HTML 文件中的一些其他元素。

我的第一个想法是转向 PHP。在我的开发生涯中，我写了相当多的 PHP，并且可能是我最喜欢的 PHP 特性之一是，它可以通过一个简单的`include`作为 HTML 使用。

所以我有一个`header.php` :

```
<html>
  <head>
   Whatever - don't care atm
   <link href="styles.css" ref="application/css" /> 
  </head>
  <body>
    <nav><ul><li>home</li></ul></nav> 
```

Enter fullscreen mode Exit fullscreen mode

```
 <?php
// index.php
include('./header.php');
?>

Page-specific content goes here!

<?php
include('./footer.php')
?> 
```

Enter fullscreen mode Exit fullscreen mode

还有一些`footer.php` :

```
 </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果我更改了页面导航的某些内容，我就不必去大量的文件中复制/粘贴这些更改。

不幸的是，我没有安装 PHP，但如果我必须得到它，我会。

此外，使用 PHP 还可以获得许多好处:

*   内置的编程语言生成列表来模仿东西
*   更复杂的`includes`,如果我想的话——例如，将`header.php`转换成一个以模板变量作为参数的函数
*   即时“编译”(例如，保存，刷新和它的工作！)

我遇到的另一个问题是 CSS。普通的 CSS 很好，但有了 SCSS，我可以加快我的开发速度并保持风格一致。另外，我可以把我的 CSS 分成我想要的任何数量的文件。这样，我可以在变量中定义颜色，用我喜欢的方式嵌套我的选择器等等。

我想我可以用传统的方式做事(特别是因为我不知道如何使用原生 CSS 变量):

```
 /*---------------------
Variables:

main color: blue
secondary color: orange 

nav grey: #444
*/

// Navigation section
.nav {
  background-color: #444;
}

// Footer section
.footer {

}

.footer a {
  color: blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

你知道，这完全可以工作，但我开始害怕安装 PHP，使用查找/替换来更新 CSS 中的“变量”，然后用一个大的 CSS 文件来处理所有事情。当我开始恐慌时，我想起了包裹。

## 介绍包裹

Parcel 是 JavaScript bundler，至少你是这么听说的吧？有点像网络背包。

好吧，我不会为这个项目写任何 JavaScript(至少，在这个阶段我不打算写！).

Parcel 是一个非常强大的编译/处理/无论什么 JavaScript 工具，它可以非常容易地自动化你的整个工作流程。它基于零配置思想。它并不完全是零配置，但它在 90%的使用案例中都有“正常”的配置。

当我将它用于 Preact、react 和 TypeScript 时，我不需要做太多事情就能让它工作。几乎任何您想要添加的配置都与工具本身更相关，而不是包。

例如，package 会在`localhost:1234`自动公开你的编译结果，它会自动监视你的文件，并且会正确地为 JSX 编译向 Babel 别名 Preact。

所以我开始考虑如何在我的用例中使用 package，我承诺自己在这上面花费最少的时间，因为对我来说，投入开发比一个好的工作流更重要。

## 基本设置

基本设置非常简单。因为我不想全局使用 package，所以我会安装它以及它所有的插件和依赖项，它的 transpilers 等等:

```
npm install parcel-bundler --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

对，就是这样！我将为我的站点创建一个名为`site`的文件夹，并设置一个 NPM 脚本，这样我们就可以使用本地安装:

```
mkdir site
touch site/index.html 
```

Enter fullscreen mode Exit fullscreen mode

还有剧本:

```
"scripts":  {  "start":  "parcel site/index.html
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`npm start`将启动您的开发流程，其中:

*   一切都是自动观看的
*   文件显示在`localhost:1234`
*   编译后的文件在`dist`文件夹中

## SCSS 编译

我以前用过 package，这意味着我已经知道它有强大的 SASS/SCSS 支持。我记得这个语法，主要是因为我参与了几个基础项目，然后我在工作中设置了它，所以我更多地使用了它。不然我觉得两者不相上下(先不要开始 SASS/LESS wars)。

过程很简单，安装 sass:

```
npm install sass --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

并链接到您的 HTML:
中的 scss 文件

```
<html>
  <head>
    <link href="styles.scss" type="text/css" />
  </head>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

你可能不得不重新启动包裹，但它应该“只是工作”TM。

## 嗯，HTML 模板化？

包不包括模板引擎。这是件好事。它*包含的是 PostHTML。*

现在，我担心这一部分，但在做了一些研究后，这是什么让我使用包裹。PostHTML 是一个 HTML 后处理器。有点像 PostCSS。

把它想象成一个工具，让你用 HTML 做一些很酷的事情。其中之一就是通过插件使用`include`语法！什么？

我真的想要我的类似 PHP 的`include`，这就是`posthtml-include`插件给我们的，但是通过一个更类似 HTML 的语法，比如:

```
<include src="./partials/header.html"></include> 
```

Enter fullscreen mode Exit fullscreen mode

那是我惊叹的时刻。我查看了 PostHTML 插件，发现了一个惊人的工具库。我坚持使用`include`插件，因为它是最容易和最简单的。这让我不会“太聪明地”做事。

所以要安装它，你需要安装插件

```
npm install posthtml-include --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

现在您必须创建您的第一个配置。我想指出的是，这是针对`posthtml`的配置，而不是针对包的配置。创建一个`.posthtmlrc.js`文件并把它放在这里:

```
module.exports = {
  plugins: {
    'posthtml-include': {
      root: __dirname + '/site',
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将启用插件，并告诉您项目的根目录在哪里。

## 还有什么？

嗯，没什么。有了这个简单的设置，我就有了 SCSS 编译、基本的 HTML 包含、文件查看、服务器运行，除了一个需要知道项目根的插件之外，基本上没有任何配置。

我想下次我设置这个的时候，我会试着使用 [posthtml-modules](https://github.com/posthtml/posthtml-modules) ，它允许一种类似于 Angular 和 AngularJS 所做的`transclusion`或“内容投影”。像这样:

```
<!-- index file -->
<module href="./module.html">
  Content to transclude
</module>

<!-- module file -->
Here is my special content:

<pre>
  <content></content>
</pre>

<!-- resulting index file -->

Here is my special content:

<pre>
  Content to transclude
</pre> 
```

Enter fullscreen mode Exit fullscreen mode

另一个选择是使用 [posthtml-expressions](https://github.com/posthtml/posthtml-expressions) 进行真正的模板化；然而，我仍然不是 100%赞成在我的配置中有一个巨大的变量列表。另一方面，这会让我们更接近

## JavaScript 呢？

事情是这样的...在我开始做这个项目后，我意识到我需要一些 JS。我需要有粘性下拉菜单(点击显示菜单，而不是悬停)，和一个滑块。使用 CSS 两者都是可能的；然而，这不仅超出了我的能力范围，而且我认为这是很难维护的。

因此...此时，除了在`index.html`文件中包含一个 JS 脚本标记之外，我不需要做太多事情。开始编码。

> 喜欢这篇文章？[在 Twitter 上关注我](https://twitter.com/antjanus)或查看[我的博客](https://antjanus.com)