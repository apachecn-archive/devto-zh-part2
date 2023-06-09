# 让我们构建 Web 组件！第 2 部分:聚合填料

> 原文：<https://dev.to/bennypowers/lets-build-web-components-part-2-the-polyfills-dkh>

基于组件的用户界面现在非常流行。您知道 web 有自己的本地组件模块，不需要使用任何库吗？真实故事！你可以编写、发布和重用单文件组件，这些组件可以在任何好的浏览器和框架中工作。

在我们的[上一篇文章](https://dev.to/bennypowers/lets-build-web-components-part-1-the-standards-3e85)中，我们了解了让我们编写 web 组件的四个 web 标准:`<template>`、定制元素、影子 DOM 和 JavaScript 模块。

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们构建 Web 组件！第 1 部分:标准

### 本尼为🇮🇱🇨🇦供电 9 月 18 日 1810 分钟阅读

#webcomponents #customelements #javascript #html](/bennypowers/lets-build-web-components-part-1-the-standards-3e85)

今天，我们将学习一些关于 webcomponentsjs polyfills 的知识，它允许我们编写基于 web 组件的应用程序，这些应用程序可以在不支持这些规范的浏览器上运行。

*   [概述](#overview)
*   [装载聚合填料](#loading-the-polyfills)
    *   [高级装载场景](#advanced-loading-scenarios)
    *   [异步加载](#asynchronous-loading)
*   [编写使用 ShadyCSS 聚合填充的自定义元素](#writing-custom-elements-that-work-with-the-polyfills)
    *   [shady CSS TL；博士](#shadycss-tl-dr)
*   [自定义元素聚合填充](#custom-elements-polyfill)
    *   [支持 IE11](#supporting-ie11)

## 概述

Web 组件真的很棒。如果你是我最喜欢的书呆子，跨浏览器、可重用、可互操作组件的承诺是令人兴奋的。毫无疑问，基于 web 组件的库和应用程序将迅速普及，因为截至 2018 年 10 月下旬，最新版本的 Chrome、Firefox 和 Safari 将原生支持 web 组件。甚至微软也已经开始在 Edge 中实现它们。太棒了。

但是已经在这个地方呆了一分钟以上的 web 开发人员知道事情并不总是那么简单。有时候感觉网络平台功能越酷(我在看着你， [scroll-snap](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Scroll_Snap) ！)，就越不可能得到广泛支持。

但是不要害怕，朋友们！今天，您可以一头扎进 web 组件的世界，而不用担心会把使用旧浏览器的用户甩在后面。谷歌 [web components 团队](https://www.webcomponents.org/)的优秀人员在创建 [webcomponentsjs polyfills](https://github.com/webcomponents/webcomponentsjs) 时就想到了你，这让你可以将你的应用定位到 IE11，我敢肯定这就是你早上醒来的原因。polyfills 也可以在 Chrome 和 Firefox 的旧版本上工作，也可以在微软 Edge 上工作，直到他们~~醒来，在他们的用户语音板~~ 上实现两个[最流行的标签，完成它们的实现。](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/filters/top)

所以不要只是坐在那里，继续读下去！我们将一起学习如何加载聚合填充，如何编写正确利用它们的自定义元素，以及如何避免聚合填充的已知问题和缺陷。

## 加载聚合填充

对于大多数用户来说，最简单的方法是在加载任何组件文件之前，弹出一个脚本标签，将`webcomponents-loader.js`脚本放入页面的`head`中。该脚本检查用户浏览器的 <abbr title="user agent">UA</abbr> 字符串，并仅加载所需的聚合填充或聚合填充集。

```
<head>
  <!-- Load the polyfills first -->
  <script src="https://unpkg.com/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script>
  <!-- Then afterwards, load components -->
  <script type="module" src="./superlative-input.js"></script>
</head> 
```

Enter fullscreen mode Exit fullscreen mode

你可以像我们上面做的那样通过 CDN 加载脚本，或者你可以通过安装到你的项目将它们与你的应用程序代码捆绑在一起:

```
npm install --save @webcomponents/webcomponentsjs 
```

Enter fullscreen mode Exit fullscreen mode

```
<head>
  <!-- ... -->
  <script src="/node_modules/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script>;
</head>

<body>
  <script type="module">
    import './superlative-input.js'
    const template = html`<superlative-input label="🥙"></superlative-input>`;
    // ...
  </script>
</body> 
```

Enter fullscreen mode Exit fullscreen modeBy the way, I just discovered that there's a falafel emoji 🥙, which I think technically brings the world one step closer to perfection.

### 高级加载场景

如果您确切知道您需要什么，您也可以单独加载特定的聚合填充:

```
<!-- Load all polyfills, including template, Promise, etc. -->
<!-- Useful when supporting IE11 -->
<script src="https://unpkg.com/@webcomponents/webcomponentsjs/webcomponents-bundle.js"></script>

<!-- Load only the Shadow-DOM and Custom Elements polyfills -->
<!-- Useful to support Firefox <63 -->
<script src="https://unpkg.com/@webcomponents/webcomponentsjs/entrypoints/webcomponents-sd-ce-index.js"></script>

<!-- Load only the Shadow-DOM polyfills -->
<script src="https://unpkg.com/@webcomponents/webcomponentsjs/entrypoints/webcomponents-sd-index.js"></script>

<!-- Load only the Custom Elements polyfills -->
<script src="https://unpkg.com/@webcomponents/webcomponentsjs/entrypoints/webcomponents-ce-index.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

在任何情况下，您都可以选择硬着头皮加载 bundle 或`sd-ce` polyfills，这将为您的用户节省往返服务器的时间。在生产环境中，减少请求数量非常重要，因此这是一个受欢迎的选择。在大多数简单的情况下，您可能只想使用`webcomponents-loader.js`脚本。

全包给你的关键装载路径增加了 **94kb** ，而装载器只增加了 **5kb** 。您应该平衡旧浏览器上可能的少数用户的需求和 evergreen 浏览器上可能的大多数用户的便利。

### 异步加载

在大多数情况下，您会希望在`head`的顶部同步加载`webcomponents-loader.js`脚本。但是有时您会希望异步加载它。例如:如果你的应用程序实现了一个[静态应用程序外壳](https://google-developer-training.gitbooks.io/progressive-web-apps-ilt-concepts/content/docs/introduction-to-progressive-web-app-architectures.html#instant)来给用户一种性能错觉，你会希望静态 HTML 和 CSS 尽可能快地加载，这意味着消除渲染阻塞资源。在这些情况下，您需要使用`window.WebComponents.waitFor`方法来确保您的组件在 polyfills 之后加载。这里有一个~~从`webcomponentsjs`自述文件中无偿引用~~略加修改的例子:

```
<!-- Note that because of the "defer" attr, "loader" will load these async -->
<script defer src="node_modules/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script>

<!-- Load a custom element definitions in `waitFor` and return a promise -->
<!-- Note that all modules are deferred -->
<script type="module">
  WebComponents.waitFor(() =>
    // At this point we are guaranteed that all required polyfills have
    // loaded, and can use web components API's.
    // The standard pattern is to load element definitions that call
    // `customElements.define` here.
    // Note: returning the import's promise causes the custom elements
    // polyfill to wait until all definitions are loaded and then upgrade
    // the document in one batch, for better performance.
    Promise.all([
      import('./my-element.js'),
      import('/node_modules/bob-elements/bobs-input.js'),
      import('https://unpkg.com/@power-elements/lazy-image/lazy-image.js?module'),
    ])
  );
</script>

<!-- Use the custom elements -->
<my-element>
  <bobs-input label="Paste image url" onchange="e => lazy.src = e.target.value"></bobs-input>
  <lazy-image id="lazy"></lazy-image>
</my-element> 
```

Enter fullscreen mode Exit fullscreen mode

或者一个更典型的静态应用外壳模式的例子:

```
<head>
  <script defer src="https://unpkg.com/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script>
  <style>
    /* critical static-app-shell styles here */
  </style>
</head>
<body>
  <script type="module">
    // app-shell.js in turn imports its own dependencies
    WebComponents.waitFor(() => import('./app-shell.js'))
  </script>
  <app-shell loading>
    <header id="static-header">
      <span id="static-hamburger"></span>
      <span id="static-user"></span>
    </header>
    <main>
      <div id="static-spinner"></div>
    </main>
    <footer id="static-footer"></footer>
  </app-shell>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

## 编写使用聚合填充的自定义元素

如果你正在使用一个 web 组件库，比如 [Polymer](https://github.com/polymer/polymer) 、 [LitElement](https://github.com/polymer/lit-element) 或 [hybrids](https://github.com/hybridsjs/hybrids) (还有其他的)来编写你的组件(我们将在后面的帖子中介绍)，你的组件将可以开箱即用地使用 polyfills。那些库是[专门](https://github.com/Polymer/polymer/search?q=shady+extension%3Ajs&unscoped_q=shady+extension%3Ajs) [编写](https://github.com/Polymer/lit-element/blob/eb481ecc925bc4535e4101112c65b71ff7f7d450/src/lib/updating-element.ts#L387)到[使用](https://github.com/hybridsjs/hybrids/search?q=shady&unscoped_q=shady)的 polyfills。你的任务完成了。喝杯啤酒吧。

但是，如果您在编写组件时没有使用库(首先，这对您有好处)，您将需要经历一些困难，以确保您的组件能够正确地呈现给尽可能多的用户。

眼尖的读者可能已经注意到我们在上一篇文章中使用的一个例子中夹杂了几行复杂的 JavaScript 代码:

```
const template = document.createElement('template')
template.innerHTML = /*...*/

// Let's give the polyfill a leg-up
window.ShadyCSS &&
window.ShadyCSS.prepareTemplate(template, 'awesome-button')

customElements.define('awesome-button', class AwesomeButton extends HTMLElement {
  constructor() {
    super()
    this.onclick = () => report('Clicked on Shadow DOM')
  }

  connectedCallback() {
    // Let's give the polyfill a leg-up
    window.ShadyCSS && window.ShadyCSS.styleElement(this)
    if (!this.shadowRoot) {
      this.attachShadow({mode: 'open'});
      this.shadowRoot.appendChild(template.content.cloneNode(true))
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

看到那个`ShadyCSS`参考了吗？这是 polyfills 的一部分，在不支持它的浏览器中模拟影子 DOM 的样式范围。为了正确确定样式的范围，需要遵循一些规则:

### ShadyCSS 规则:

1.  样式应该在`<style>`元素中定义，该元素是`<template>`元素的直接子元素。
2.  那个`<style>`标签应该是模板中唯一的一个。
3.  在你的元素附加之前，用`ShadyCSS.prepareTemplate(templateElement, tagName)`把它的模板和它的标签名关联起来
4.  在您的定制元素附加到文档之后，但是在创建阴影根之前，在您的定制元素上调用`ShadyCSS.styleElement`来计算它的样式。

将样式标签中的规则解析成一个抽象语法树，然后将生成的父选择器添加到它们前面以模拟作用域。

```
button {/*...*/} 
```

Enter fullscreen mode Exit fullscreen mode

成为...

```
.style-scope .awesome-button button {/*..*/} 
```

Enter fullscreen mode Exit fullscreen mode

将作用域类应用于你的元素及其“可疑”子元素。

```
<awesome-button>
  #shadow-root
  <button></button>
</awesome-button> 
```

Enter fullscreen mode Exit fullscreen mode

成为...

```
<awesome-button>
  <button class="style-scope awesome-button"></button>
</awesome-button> 
```

Enter fullscreen mode Exit fullscreen mode

如果浏览器不支持，ShadyCSS 也会填充 CSS 自定义属性(`var(--foo)`)。

### 动态样式

由于 ShadyCSS 聚合填充的工作方式，建议需要支持旧版本浏览器的 web 组件作者不要使用动态生成的 CSS，例如:

```
const getTemplate = ({disabled}) => `
  <style>
    button {
      background-color: ${disabled ? 'grey' : 'white'};
    }
  </style>
`

class AwesomeButton extends HTMLElement {
  set disabled(disabled) {
    this.render()
  }

  connectedCallback() {
    this.attachShadow({mode: 'open'})
    this.render()
  }

  render() {
    this.shadowRoot.innerHTML = getTemplate(this.disabled)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不要用那个例子(因为许多不同的原因，而不仅仅是 ShadyCSS 兼容性的原因，这个例子考虑得很糟糕)，而是使用 CSS 自定义属性，每当发生动态更新时，就使用`ShadyCSS.styleSubTree`或`ShadyCSS.styleDocument` :

```
const template = document.createElement('template')
template.innerHTML = `
  <style>
    button {
      background-color: var(--awesome-button-background, white);
    }
  </style>
  <button></button>
`;

class AwesomeButton extends HTMLElement {
  static get observedAttributes() {
    return ['disabled']
  }

  connectedCallback() {
    if (!this.shadowRoot) {
      this.attachShadow({mode: 'open'})
      this.shadowRoot.appendChild(template.content.cloneNode(true))
    }
  }

  attributesChangedCallback(name, oldVal, newVal) {
    name === 'disabled' &&
    ShadyCSS &&
    ShadyCSS.styleDocument({
      '--awesome-button-background' : newVal ? 'grey' : 'white',
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

出于好奇，本地实现这一点的方法，即如果不涉及 polyfills，只需对文档进行样式化:

```
// No Polyfill
document.documentElement.style
  .setProperty('--awesome-button-background', newVal ? 'grey' : 'white'); 
```

Enter fullscreen mode Exit fullscreen mode

这些都是人为的例子。在现实世界中，你更有可能完全用 CSS 来解决问题，比如:

```
:host { background: white; }
:host([disabled]) { background: grey; } 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你想，比如说，基于触摸事件旋转色调或者基于 websocket 更新变换元素，CSS 自定义属性是一个不错的选择。

ShadyCSS 提供了一些其他的特性，比如现在已经废弃的`@apply` CSS 语法的垫片，但是我们不打算讨论它们，因为这个规范已经过时了。

阴影填充聚合填充也有一些[已知的限制。剧透:](https://github.com/webcomponents/shadycss#limitations)

*   由于 ShadyCSS 移除了所有的`<slot>`元素，所以你不能直接选择它们，所以你必须使用一些类似`.context ::slotted(*)`的上下文包装器。
*   文档样式可能会渗透到您的树荫下，因为 polyfill 仅模拟封装。

关于已知限制的内幕，请参见[自述文件](https://github.com/webcomponents/shadycss#limitations)。

### shady CSS TL；博士:

所以基本上，只要你愿意，你的元素甚至可以在旧的浏览器和 Edge 上正常工作

*   在元素的`<template>`元素中定义元素的样式；
*   考虑你的元素的阴影槽与聚合填充；在你的元素的`connectedCallback`中制作合适的咒语；和
*   使用`ShadyCSS.styleDocument`或`ShadyCSS.styleSubTree`动态更新 CSS 自定义属性，或者使用其他基于 CSS 的解决方案来避免这个问题。

## 自定义元素聚合填充

[自定义元素 polyfill](https://github.com/webcomponents/custom-elements) 用自定义元素规范中的 API 修补了几个 DOM 构造函数:

*   `HTMLElement`获得自定义元素回调，如`connectedCallback`和`attributeChangedCallback`(我们将在下一篇文章中详细讨论)。它的原型。
*   `Element`获取`attachShadow`，并且像`setAttribute`和`innerHTML`设置器这样的方法被打补丁以处理多填充的定制元素回调。
*   类似于`appendChild`的`Node`上的 DOM APIs 也打了类似的补丁
*   `Document#createElement` *等人*得到类似的待遇。

它还在`window`上公开了`customElements`对象，因此您可以注册您的组件。

polyfill 在`DOMContentLoaded`之后升级定制元素，然后初始化一个`MutationObserver`来升级任何随后附加了 JavaScript 的定制元素。

### 支持 IE11

<figure>[https://www.youtube.com/embed/EWX0ib9onRA](https://www.youtube.com/embed/EWX0ib9onRA)

<figcaption>How I feel when someone tells me they need to support IE11.</figcaption>

</figure>

`<rant>`

polyfills 支持 IE11，但不全是阳光和彩虹。IE11 不再由 MS 开发，这意味着它*不应该*被使用。决定支持 IE11 意味着增加了开发时间，增加了复杂性，增加了 bug 的表面积，并将用户暴露在一个有缺陷的、过时的浏览器面前。任何时候 IE11 支持作为一个需求被提出，它都必须被仔细评估。不要仅仅把它归为“值得拥有的东西”。这是*而不是*美好的拥有。如果这不是基于不可避免的情况的绝对需求，最好根本不要支持它。

`</rant>`

*phew* 。好了，继续表演。

根据规范，定制元素必须用 JavaScript `class` es 定义，但是 IE11 永远不会支持 ES6 的这个特性。所以我们必须用巴别塔或者类似的工具把我们的类移植到 ES5。如果你使用的是[聚合物客户端](https://www.polymer-project.org/3.0/docs/tools/polymer-cli)，有一个将 JS 转换成 ES5 的选项。

在理想情况下，您应该构建两个或更多版本的站点:

1.  使用`class`关键字和 es2015+功能为 evergreen/modern 浏览器编写
2.  使用`function`关键字类传输到 ES5
3.  以及任何介于两者之间的色调。

然后[为你的应用](https://github.com/Polymer/prpl-server)提供差异化服务，向有能力的用户代理发送快速、轻便、现代的代码，向旧浏览器发送缓慢、透明、遗留的代码。

但这并不总是一种选择。如果你有简单的静态主机，需要为所有浏览器构建一个包，你将被迫转换到 ES5，这与原生的`customElements`实现不兼容。

对于这种情况，polyfill 为支持 ES5 风格的`function`关键字元素的原生 customElements 实现提供了一个[垫片，确保将它包含在您的构建中(不要传输这个文件！)如果你的目标是使用相同的捆绑包的新旧浏览器。](https://github.com/webcomponents/custom-elements/blob/master/src/native-shim.js) 

```
<script src="/node_modules/@webcomponents/webcomponentsjs/entrypoints/custom-elements-es5-adapter-index.js"></script>
<script src="/node_modules/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

活跃的 web 组件社区成员 [@ruphin](https://github.com/ruphin) 提出了一个[巧妙的技巧](https://github.com/ruphin/gluonjs/blob/master/examples/index.html)你可以用它来提供一种甚至在静态主机上的差别服务，那就是利用浏览器的`nomodule`特性:

```
<!-- This loads the app as a module on Chrome, Edge, Firefox, and Safari -->
<!-- Modules are always nonblocking, and they load after regular scripts, so we can put them first -->
<script type="module" src="/index.js"></script>

<!-- This loads the app on IE11 -->
<script nomodule src="https://cdnjs.cloudflare.com/ajax/libs/babel-polyfill/6.26.0/polyfill.min.js"></script>
<!-- Take a look at rollup.config.js to see how to build this guy -->
<script nomodule src="./index.nomodule.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

看看他的轻量级 web 组件框架 gluonjs

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [鲁芬](https://github.com/ruphin) / [格隆吉斯](https://github.com/ruphin/gluonjs)

### 轻量级 Web 组件库

<article class="markdown-body entry-content container-lg" itemprop="text">

# [核]胶子（一种理论上假设的无质量的粒子）

[![Build Status](img/92a2881644a5afc2c7ae83c9e3190c9b.png)](https://travis-ci.org/ruphin/gluonjs)[![NPM Latest version](img/b170d02255a38160d182f20b8896a833.png)](https://www.npmjs.com/package/@gluon/gluon)[![Code Style: prettier](img/76245b01cc15290741bd336634799095.png)](https://github.com/prettier/prettier)

用于构建 web 组件和应用的轻量级库

* * *

*   **基于平台:** GluonJS 旨在利用最新的网络平台功能，使其尺寸极小，并在现代浏览器上具有很高的性能。此外，这意味着**构建/编译步骤是可选的**；GluonJS 组件可以在现代浏览器上工作，不需要任何预处理。
*   **组件模型:**用封装的逻辑和样式构建组件，然后组合它们，制作复杂的接口。使用 Web 组件标准，开发人员可以直接使用所有相关的 API。
*   **高度可重用:**因为 GluonJS 创建了符合标准的 Web 组件，所以您可以在几乎任何现有的应用程序中使用 GluonJS 创建的组件。检查各处的[定制元素](https://custom-elements-everywhere.com/)以获得与现有框架的最新兼容性表。
*   **强大的模板化:** GluonJS 使用 [lit-html](https://github.com/PolymerLabs/lit-html) 进行模板化，具有很强的表现力和灵活性。

## 概念

```
import { GluonElement } from '/node_modules/@gluon/gluon/gluon.js';
class MyElement extends GluonElement {
  // ...
}

customElements.define(MyElement.is, MyElement
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/ruphin/gluonjs)

## 结论

webcomponentsjs 聚合填充允许您在较旧的浏览器中运行 web 组件。没错，要使它工作，您必须跨越一些障碍，但是如果您使用 web 组件助手库来定义您的元素，那么大部分工作都会由您来完成。

在我们的下一篇文章中，上帝保佑，我们将探索用普通的浏览器 API 编写 web 组件以获得最大的控制和互操作性。

### 勘误表

*   本文的前一版本建议在模块中导入聚合填充，如下所示:`import '@webcomponents/webcomponentsjs/webcomponents-loader.js';`不要这样做。相反，应在加载任何其他模块之前，将聚合填充加载到文件`head`中。这篇文章已经用一个更新的例子改正了。
*   本文的前一版本建议不要加载特定的聚合填充。当前版本更深入地说明了为什么以及何时选择这样做。
*   本文的前一个版本使用了`this.shadowRoot.append`，它可以在支持的浏览器上工作。最好使用`this.shadowRoot.appendChild`，它也适用于聚合填充。
*   本文的前一版本展示了在`connectedCallback`中附加一个影子根的例子，而没有首先检查一个影子根是否已经存在。示例已更新。
*   自从这篇文章发表以来，[微软已经开始在 Edge](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/customelements/) 中开发 web 组件标准。派对时间！

查看本系列的下一篇文章

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们构建 Web 组件！第 3 部分:普通组件

### 本尼·鲍尔斯🇮🇱🇨🇦10 月 5 日 1818 分钟阅读

#webcomponents #customelements #javascript #html](/bennypowers/lets-build-web-components-part-3-vanilla-components-4on3)

您想就此处涉及的任何主题进行一对一的辅导吗？[![Contact me on Codementor](img/97f0e0737ce864c47f2412396b01e737.png)T2】](https://www.codementor.io/bennyp?utm_source=github&utm_medium=button&utm_term=bennyp&utm_campaign=github)