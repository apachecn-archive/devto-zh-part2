# 用普通 JavaScript 构建 Web 组件

> 原文：<https://dev.to/aspittel/building-web-components-with-vanilla-javascript--jho>

回到 2015 年，我正在学习我的第一个前端框架——angular js。我的想法是，我正在用自定义特性构建自己的 HTML 标签。当然，这并不是真正发生的事情，但它有助于降低学习曲线。

现在，您实际上可以使用 web 组件构建自己的 HTML 标签！它们仍然是一个实验性的功能——它们可以在 Chrome 和 Opera 中工作，可以在 FireFox 中启用，但在 Safari 和 Edge 中仍未实现。一旦它们完全推出，它们将成为用纯普通 JavaScript 构建可重用组件的更棒的工具——不需要库或框架！

## 学习过程

我很难找到用普通 JS 编写的关于 web 组件的文章和例子。有很多关于 Polymer 的例子和文章，Polymer 是一个用于编写 web 组件的框架，其中包括针对尚不支持 web 组件的浏览器的 polyfills。这个框架听起来很棒，我可能会在未来尝试使用它，但我想在这个特定的项目中只使用普通的 JavaScript。

为了构建我的项目，我最终主要使用了影子 DOM 上的 [MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 文档。我还浏览了 CodePen 和 WebComponents 网站，尽管我在这两个网站上都没有找到太多与我想要构建的相似的内容。

我也非常喜欢 Joseph Moore 关于 web 组件的文章，那篇文章是在我做这个项目的时候发表的。它涵盖了使用 web 组件的一些好处:它们适用于所有框架，并且易于实现和理解，因为它们只使用普通的 JavaScript。

## 最终项目

在我的很多项目中，我都使用类似的设计方案来打造个人品牌，这样我就不用再设计新的了！特别是，我使用了一个标题，每个字母是不同的颜色，上面有一个下落的动画。我的个人网站 [alispit.tel](https://www.alispit.tel) 就是一个很好的例子！我的简历、会议幻灯片上也有这段文字，我还计划在不久的将来将它用于其他网站！它的问题是 CSS 不允许你针对单个字符——除了第一个字符。因此，每个字母都必须用一个`span`包起来。写起来会很痛苦，所以我认为这是使用 webcomponent 的最佳地方！

[![display of the rainbow letters](img/c839ba5397cf7290d32f70fe1fab2337.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--98bWCSOg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbrljpvky5xxb80xyyk3.png)

因为我很难找到关于编写 web 组件的人的文章，所以我将在这里深入研究代码。

首先，让 web 组件呈现的 HTML 代码如下所示:

```
 <rainbow-text text="hello world" font-size="100"></rainbow-text> 
```

web 组件被称为`rainbow-text`，它有两个属性:文本(组件呈现的内容)和字体大小。也可以使用`slots`和`templates`插入内容；然而，在我的用例中，它们会增加额外的开销。我想输入文本，然后输出一系列用字符分隔的 HTML 元素，所以最简单的方法是通过属性传入文本——特别是用 Shadow DOM。

那么，什么是影子 DOM 呢？它实际上不是新的，也不是特定于 web 组件的。影子 DOM 引入了一个有自己作用域的 DOM 元素子树。它还允许我们隐藏子元素。例如，`video`元素实际上是 HTML 元素的集合；然而，当我们创建一个并检查它时，我们只看到了`video`标签！对我来说，shadow DOM 最酷的部分是样式被限定了范围！如果我在文档中添加了一个样式，比如修改所有的`div`，这个样式不会影响阴影 DOM 中的任何元素。相反，阴影 DOM 中的样式不会影响外部文档 DOM 中的元素。这是我最喜欢的 Vue 特性之一，所以我非常兴奋我可以不用框架就能实现类似的东西！

现在让我们转到实现定制元素的 JavaScript 代码。首先，编写一个 JavaScript 类来扩展内置的`HTMLElement`类。我使用了 ES6 类，但是如果您愿意，也可以使用旧的面向对象的 JavaScript 语法。我真的很喜欢使用 ES6 类，尤其是因为我已经习惯了 React！语法感觉既熟悉又简单。

我做的第一件事是编写`connectedCallback`生命周期方法。当元素被渲染时，这个函数被自动调用——类似于 React 中的`componentDidMount`。你也可以使用一个类似于其他 ES6 类的`constructor`;然而，我真的不需要，因为我没有设置任何默认值或任何东西。

在`connectedCallback`中，我首先通过调用`this.createShadowRoot()`实例化了元素的影子 DOM。现在，`rainbow-text`元素是它自己的影子 DOM 的根，所以它的子元素将被隐藏，并有自己的样式和外部 JavaScript 变化范围。然后，我根据传入的 HTML 属性在类中设置属性。在类内部，你可以认为`this`指的是`rainbow-text`元素。不用运行`document.querySelector('rainbow-text').getAttribute('text')`，只需运行`this.getAttribute('text')`就可以从元素中获取`text`属性。

```
class RainbowText extends HTMLElement {
  connectedCallback () {
    this.createShadowRoot()
    this.text = this.getAttribute('text')
    this.size = this.getAttribute('font-size')
    this.render()
  } 
```

`render`是我写的一个方法，在`connectedCallback`中被调用。如果对你的代码有帮助，你也可以使用`disconnectedCallback`和`attributeChangedCallback`生命周期方法！我只是把它分离出来，以便遵守桑迪·梅斯的规则，我非常虔诚地遵守这些规则！这个方法中与普通 DOM 操作不同的一点是，我将自己创建的元素附加到了`shadowRoot`而不是`document`或者直接附加到元素上！这只是将元素附加到影子 DOM，而不是文档的根 DOM。

```
 render () {
    const div = document.createElement('div')
    div.classList.add('header')
    this.shadowRoot.appendChild(div)
    this.addSpans(div)
    this.addStyle()
  } 
```

然后，我将每个字母的跨度添加到 DOM 中，这与普通的 JavaScript 代码基本相同:

```
 addSpanEventListeners (span) {
    span.addEventListener('mouseover', () => { span.classList.add('hovered') })
    span.addEventListener('animationend', () => { span.classList.remove('hovered') })
  }

  createSpan (letter) {
    const span = document.createElement('span')
    span.classList.add('letter')
    span.innerHTML = letter
    this.addSpanEventListeners(span)
    return span
  }

  addSpans (div) {
    [...this.text].forEach(letter => {
      let span = this.createSpan(letter)
      div.appendChild(span)
    })
  } 
```

最后，我将样式添加到阴影 DOM 中:

```
 addStyle () {
    const styleTag = document.createElement('style')
    styleTag.textContent = getStyle(this.size)
    this.shadowRoot.appendChild(styleTag)
  } 
```

这个方法向 shadow DOM 添加了一个`style`标签来修改其中的元素。我使用一个函数将标题的字体大小插入到包含所有 CSS 的模板文字中。

编写完组件后，我必须注册我的新元素:

```
try {
  customElements.define('rainbow-text', RainbowText)
} catch (err) {
  const h3 = document.createElement('h3')
  h3.innerHTML = "This site uses webcomponents which don't work in all browsers! Try this site in a browser that supports them!"
  document.body.appendChild(h3)
} 
```

我还为使用非 webcomponent 友好浏览器的用户添加了一个警告！

下面是这个元素如何出现在控制台中的:
[![](img/08324053024d8ead40a6526fbb6485fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cptd5hXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4umhrrmgbynsbmq3jgmz.png)

## 下一步

我喜欢使用 web 组件！能够在没有框架的情况下创建可重用组件的想法非常棒。我建立的这个对我真的很有帮助，因为我经常使用这个多颜色的名字。我将在其他文档中包含`script`。不过，我不会将我的个人站点转换为使用该组件，因为我希望它能得到跨浏览器的支持。也没有一个清晰的状态或数据管理系统，这对于 web 组件的目标是有意义的；然而，这使得其他前端框架仍然是必要的。出于这些原因，我想我会继续使用前端框架；然而，一旦完全支持它们，它们将会非常好用！

[完整代码](https://github.com/aspittel/rainbow-word-webcomponent/blob/master/script.js)
[示例使用](https://www.alispit.tel/) -(不使用 webcomponents)

**我的一部分[关于学习新事物](https://medium.com/on-learning-new-things/learning-new-things-f4db7f16724)系列**