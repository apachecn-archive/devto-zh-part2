# 让我们构建 Web 组件！第 1 部分:标准

> 原文：<https://dev.to/bennypowers/lets-build-web-components-part-1-the-standards-3e85>

基于组件的 UI 最近非常流行。事实上，它是如此的成熟，以至于人们甚至开始将老派的 jQuery 小部件重新称为“jQuery 组件”；)

当我们说“组件”时，我们主要指的是自包含的、可重用的 UI，一旦编写好，我们就可以将它插入到我们的应用程序中的任何地方。花哨的交互按钮，特别设计的下拉式引号，或者长期受欢迎的卡片小部件都是非常适合组件的设计类型的例子。

您知道 web 有自己的本地组件模块，不需要使用任何库吗？真实故事！你可以编写、发布和重用单文件组件，这些组件可以在任何好的浏览器和框架中工作。请继续阅读，了解如何操作！

## 概述

`Web Components`是一个总括术语，指的是一组四个浏览器标准，它们共同形成了 web 的本地组件模型。

1.  [`<template>`元素](#template-elements)让你快速重用部分 <abbr title="document object model">DOM</abbr>
2.  [自定义元素](#custom-elements)将 <abbr title="JavaScript">JS</abbr> 类连接到自定义 <abbr title="HyperText Markup Language">HTML</abbr> 标签
3.  阴影 DOM 将你的羞耻隐藏在页面的其余部分
4.  用于打包和发布组件的 JavaScript 模块

这些标准中的每一个都提供了拼图的一部分。在这篇介绍性的文章中，我们将简要介绍它们，并解释它们如何在实际的 web 开发中帮助我们。

## `<template>`元素

组件的基本思想是可重用 UI。为了创建它，我们需要一种为组件定义模板的方法。如果你熟悉 React，那么你可能以前用过 <abbr title="JavaScript XML">JSX</abbr> 。如果你更倾向于 Angular 类型，你可能已经在 JavaScript 模板文本中定义了模板。

元素让我们定义 HTML 的片断，这些片断直到被 JavaScript 克隆后才被添加到文档中。浏览器只需要解析 HTML 一次(例如当文档加载时)，然后就可以在需要的时候廉价地克隆它。

下面是一个模板元素的实际例子:

```
<template id="dialog-template">
  <dialog>
    <p></p>
    <button>⚓️ All Ashore!</button>
  </dialog>
</template>

<label>
  Type a <abbr title="message"> 💌</abbr>
  <input id="input"/>
</label>

<button id="clone-it"><abbr title="Go!">🦑 Ahoy!</abbr></button>

<script>
  document.getElementById('clone-it').onclick = () => superAlert(input.value);

  function superAlert(message) {
    // get a reference to the template
    const template = document.getElementById('dialog-template');
    // clone or "stamp" the template's contents
    const clone = template.content.cloneNode(true);

    // Make any changes to the stamped content
    const diag = clone.firstElementChild;

    // <dialog> element polyfill
    dialogPolyfill.registerDialog(diag);

    diag.firstElementChild.textContent = message;
    diag.lastElementChild.onclick = function closeModal() {
      diag.close();
      diag.remove();
    }
    document.body.appendChild(diag)
    diag.showModal();
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/wistful-ant?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/wistful-ant?previewSize=100&path=index.html)

使用`<template>`元素既简单又高效。我组装了一个愚蠢的小[基准](https://jsperf.com/template-element-vs-dom-api-vs-template-literals/1)，它以三种方式构建了一个简单的表:通过克隆一个模板元素，通过直接使用 DOM <abbr title="Application Programmer Interface">API</abbr> s，以及通过设置`innerHTML`。克隆模板元素是最快的，DOM APIs 稍慢，而`innerHTML`是迄今为止最慢的。

[![Template Elements: 55877 Operations / second. DOM APIs: 51666 Operations / second. Template Literals: 44102 Operations / second](img/9cdf7a2e7ebf49466bcd83593369bbbc.png)T2】](https://jsperf.com/template-element-vs-dom-api-vs-template-literals/1)

因此,`<template>`元素让我们可以解析 HTML 一次，并根据需要多次重用它。就像我们需要的可重用组件一样！

阅读更多关于 [`<template>`元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template)及其 [DOM API](https://developer.mozilla.org/en-US/docs/Web/API/HTMLTemplateElement) at <abbr title="Mozilla Developer Network">MDN</abbr> 的内容。

## 自定义元素

我们要看的第二个标准叫做定制元素。它确实如包装盒上所说的那样:它让你定义自己的定制 HTML 标签。现在你不必满足于简单的老`<div>`和`<span>`，但是你也可以用`<super-div>`和`<wicked-span>`来标记你的页面。

自定义元素就像内置元素一样工作；将它们添加到您的文档中，给它们子元素，对它们使用常规的 DOM APIs，等等。您可以在任何使用常规元素的地方使用定制元素，[包括在流行的 web 框架中](https://custom-elements-everywhere.com)

所有自定义元素标记名称都必须包含破折号，以区别于内置元素。当你想在同一个应用程序中使用`<bobs-input>`和`<sallys-input>`时，这也有助于避免名称冲突。同样，定制元素可以有自己的定制属性、DOM 属性、方法和行为。

如何使用定制元素的一个例子:

```
<section>
  <p>Twinkle, twinkle, little <super-span animation="shine">star</super-span>.</p>
  <awesome-button exuberant>Shine it!</awesome-button>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

自定义元素被定义为 [JavaScript 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)，并通过其`define`方法在`window.customElements`对象上注册，该方法有两个参数:一个定义元素名称的字符串和一个定义其行为的 JavaScript 类。

这个例子用了一个无聊的旧`<span>`并赋予它表情符号超能力！试试看。

```
customElements.define('super-span', class SuperSpan extends HTMLElement {
  /**
   * `connectedCallback` is a custom-element lifecycle callback
   * which fires whenever the element is added to the document
   */
  connectedCallback() {
    this.addEventListener('click', this.beAwesome.bind(this))
    this.style.display = 'inline-block';
    this.setAttribute('aria-label', this.innerText);
    switch (this.innerText) {
      case 'star': this.innerText = '⭐️';
    }
  }

  /**
   * You can define your own methods on your elements.
   * @param  {Event} event
   * @return {Animation}
   */
  beAwesome(event) {
    let keyframes = [];
    let options = {duration: 300, iterations: 5, easing: 'ease-in-out'}
    switch (this.getAttribute('animation')) {
      case 'shine': keyframes = [
        {opacity: 1.0, blur: '0px', transform: 'rotate(0deg)'},
        {opacity: 0.7, blur: '2px', transform: 'rotate(360deg)'},
        {opacity: 1.0, blur: '0px', transform: 'rotate(0deg)'},
      ];
    }
    return this.animate(keyframes, options)
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/ivory-fan?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/ivory-fan?previewSize=100&path=index.html)

定制元素具有生命周期回调和观察属性等内置特性。我们将在以后的文章中讨论这些内容。剧透:你可以在 MDN 上阅读[所有关于定制元素的内容](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)

## 阴影 DOM

什么东西潜伏在文档树上，藏在阴影里，藏在无辜的节点不敢涉足的黑暗地方？

哒哒哒哒哒哒！暗影 DOM！

> 我是黑暗。我是黑夜。我是影子 DOM！

[![Batman lurking in the shadows](img/85cccd039a55e13ec1ea9304fd588725.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RrMhhoCl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq600wk8fo1vg93854mf.png)

虽然“影子 DOM”听起来很奇怪，但事实证明你已经使用它很多年了。每次您使用带有控件的`<video>`元素，或者带有数据列表的`<input>`元素，或者类似日期选择器元素的其他元素时，您都在使用 Shadow DOM。

影子 DOM 只是一个 HTML 文档片段，它对用户是可见的，同时又与文档的其余部分相隔离。类似于 iframes 如何将一个文档与另一个嵌入的文档分开，影子根将一个文档的一部分与主文档分开。

例如，视频元素中的控件实际上是一个独立的 DOM 树，像蝙蝠侠一样生活在页面的阴影中。全局样式不会影响视频控件，反之亦然。

[![Screenshot of Firefox developer tools highlighting the use of a shadow root on wego.com](img/4e9abeca566d3c4fcd2676330c20b247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UWnCUCIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zue201pl8hk31s6go5al.png) 

<figure>

<figcaption>一个在 wego.com 上使用阴影 DOM 的例子，将 DOM 与页面的其余部分隔离开来</figcaption>

</figure>

为什么孤立 DOM 是一件好事？当在任何非平凡规模的 web 应用上工作时， <abbr title="Cascading Style Sheets">CSS</abbr> 规则和选择器会很快失控。你可能为页面的一个部分编写了完美的 CSS，但是你的风格却被你的团队成员否决了。更糟糕的是，你添加到应用程序中的新内容可能会在没有人注意到的情况下破坏现有的内容！

随着时间的推移，针对这个问题已经开发了许多解决方案，从严格的命名约定到“CSS-in-JS”，但是没有一个特别令人满意。有了 shadow DOM，我们在浏览器中内置了一个全面的解决方案。

**Shadow DOM 隔离 DOM 节点**，让你自由地设计你的组件，不用担心应用程序的其他部分会破坏它们。您可以用一种简单直接的方式来设计您的组件，而不是使用晦涩难懂的类名或者把所有东西都塞进`style`属性:

```
<template id="component-template">
  <style>
    :host {
      display: block;
    }

    /* These styles apply only to button Elements
     * within the shadow root of this component */
    button {
      background: rebeccapurple;
      color: inherit;
      font-size: inherit;
      padding: 10px;
      border-radius: 4px;
      /* CSS Custom Properties can pierce the shadow boundary,
       * allowing users to style specific parts of components */
      border: 1px solid var(--component-border-color, ivory);
      width: 100%;
    }

  </style>

  <!-- This ID is local to the shadow-root. -->
  <!-- No need to worry that another #button exists. -->
  <button id="button">I'm an awesome button!</button>
</template>

<style>
  /* These styles affect the entire document, but not any shadow-roots inside of it */
  button {
    background: cornflowerblue;
    color: white;
    padding: 10px;
    border: none;
    margin-top: 20px;
  }

  /* Custom Elements can be styled just like normal elements.
   * These styles will be applied to the element's :host */
  button,
  awesome-button {
    width: 280px;
    font-size: inherit;
  }
</style>

<awesome-button></awesome-button>

<button id="button">I'm an OK button!</button>

<section id="display">
  <abbr title="click">🖱</abbr> a <abbr title="button">🔲</abbr>
</section> 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/marked-piper?path=index.html](https://glitch.com/embed/#!/embed/marked-piper?path=index.html)

影子 DOM 是 web 组件中的秘密武器。这是它们自成一体的原因。这让我们有信心将它们放入页面，而不用担心破坏应用程序的其他部分。

从 Firefox 63 开始，它可以在所有优秀的浏览器上本地使用。

阅读更多关于 MDN 上的[阴影 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)

有了这三个标准:模板、定制元素和影子 DOM，我们就拥有了编写直接在浏览器中运行的丰富组件 ui 所需的一切，而不需要任何特殊的工具或构建步骤。第四个标准是 JavaScript 模块，它使我们能够分解由定制元素组成的复杂应用程序，并发布我们的组件供他人使用。

## JavaScript 模块

当我们使用*模块*这个词时，我们指的是一个包含其自身范围的独立软件。换句话说，如果我在某个模块中定义了一个变量`foo`，我只能在那个模块内部使用这个变量。如果我想在其他模块中访问`foo`，我需要首先显式导出它。

一段时间以来，开发人员一直在寻找编写模块化 JavaScript 的方法，但直到最近(在规范中是 2015 年，在实践中是去年左右)，JavaScript 才有了自己的模块系统。

```
import { foo } from './foo.js'

const bar = 'bar'

export const baz = foo(bar) 
```

Enter fullscreen mode Exit fullscreen mode

关于模块有[lot](https://dev.to/twhite/es6-modules-2bi)[到](https://dev.to/papaponmx/my-impressions-after-trying-to-use-es-modules-in-2018---3mga) [say](https://dev.to/omensah/-building-modular-javascript-application-with-es6-module-system--3h88) ，但是对于我们的目的来说，我们可以使用它们来编写和发布 web 组件就足够了。

这里有一个简单的例子来吊你的胃口。

```
// super-span.js

const options = {duration: 300, iterations: 5, easing: 'ease-in-out'}
const keyframes = [
  {opacity: 1.0, blur: '0px', transform: 'rotate(0deg)'},
  {opacity: 0.7, blur: '2px', transform: 'rotate(360deg)'},
  {opacity: 1.0, blur: '0px', transform: 'rotate(0deg)'},
]

const template = document.createElement('template')
template.innerHTML = `
  <style>
    span {
      display: inline-block;
      font-weight: var(--super-font-weight, bolder);
    }
  </style>
  <span><slot></slot></span>
  <abbr title="click or mouse over">🖱</abbr>
`;

customElements.define('super-span', class SuperSpan extends HTMLElement {

  $(selector) {
    return this.shadowRoot && this.shadowRoot.querySelector(selector)
  }

  constructor() {
    super()
    this.shine = this.shine.bind(this)
    const root = this.attachShadow({mode: 'open'})
          root.appendChild(template.content.cloneNode(true))
    this.addEventListener('click', this.shine)
    this.addEventListener('mouseover', this.shine)
  }

  connectedCallback() {
    const slot = this.$('slot')
    const [node] = slot.assignedNodes()
    this.setAttribute('aria-label', node.textContent)
    node.textContent = '⭐️'
  }

  shine(event) {
    this.$('span').animate(keyframes, options)
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们 app 的 HTML:

```
<script type="module" src="./super-span.js"></script>
<super-span>star</super-span> 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/truthful-plow?path=index.html](https://glitch.com/embed/#!/embed/truthful-plow?path=index.html)

我的朋友们，现在是你意识到 web 组件有多棒的时候了。

现在，您可以轻松地将具有出色行为和语义的预制定制元素导入到您的文档中，而无需任何构建步骤。

```
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    Be Excellent to Each Other
    <script type="module" src="//unpkg.com/@power-elements/lazy-image/lazy-image.js?module"></script>
    <script type="module" src="//unpkg.com/@granite-elements/granite-alert/granite-alert.js?module"></script>
    <script type="module" src="//unpkg.com/@material/mwc-button/mwc-button.js?module"></script>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <header>
      <h1>Cross-platform, Framework-Agnostic, Reusable Components</h1>
    </header>
    <main>

      <granite-alert id="alert" level="warning" hide>
        <lazy-image role="presentation"
            src="//placekitten.com/1080/720"
            placeholder="//web-components-resources.appspot.com/static/logo.svg"
            fade
        ></lazy-image>
      </granite-alert>

      <mwc-button id="button" raised>🚀 Launch</mwc-button>

      <script>
        const alert = document.getElementById('alert')
        const button = document.getElementById('button')
        const message = document.getElementById('message')
        button.onclick = () => {
          alert.hide = !alert.hide;
          button.textContent = alert.hide ? '🚀 Launch' : '☠️ Close'
        }
      </script>
    </main>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/road-physician?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/road-physician?previewSize=100&path=index.html)

## 结论

Web 组件标准让我们可以分解自包含的、可重用的 UI，它可以直接在浏览器中运行，无需繁琐的构建步骤。这些组件可以在任何使用常规元素的地方使用:在普通的 HTML 中，或者在应用程序的框架驱动的模板中。

在我们的下一篇文章《上帝保佑》中，我们将了解到 [webcomponentsjs polyfills](https://www.webcomponents.org/polyfills) 如何让我们设计组件和编写应用程序，即使是在浏览器本身不支持它们的情况下。

😀感谢阅读！😁

查看本系列的下一篇文章

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们构建 Web 组件！第 2 部分:聚合填料

### 本尼·鲍尔斯🇮🇱🇨🇦9 月 29 日 1812 分钟阅读

#webcomponents #javascript #html #polyfill](/bennypowers/lets-build-web-components-part-2-the-polyfills-dkh)

您想就此处涉及的任何主题进行一对一的辅导吗？[![Contact me on Codementor](img/97f0e0737ce864c47f2412396b01e737.png)T2】](https://www.codementor.io/bennyp?utm_source=github&utm_medium=button&utm_term=bennyp&utm_campaign=github)

## 勘误表

*   本文的前一个版本展示了一个在`constructor`中访问轻量级 DOM 属性和子对象的例子。这种工作应该推迟到`connectedCallback`。
*   自从这篇文章发表以来，[微软已经开始在 Edge](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/customelements/) 中开发 web 组件标准。派对时间！