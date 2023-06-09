# PlazarJS 组件示例-第 1 部分

> 原文：<https://dev.to/proticm/plazarjs-components-by-example---part-1-2i39>

你好，我的开发伙伴。我希望欢迎您阅读关于叫做 PlazarJS 的新事物的系列文章的第一篇。

你可能已经读过我以前的文章，我在那里提到过它，并与 Vue 和 React 进行了 Hello World 比较，但请继续关注我的这篇文章，因为我们将深入一点，只关注 PlazarJS。

当我决定引入 PlazarJS 时什么将是我的切入点，什么将是首先要谈论的事情时，我问自己什么现在流行，在任何 JavaScript 框架中人们最喜欢什么？嗯，我想到了答案:*“组件！”*。

我真的认为我没有错，因为在其他伟大的特性中，组件代表了所有当前流行框架的指导方针。
首先，我将尝试简要回答关于 PlazarJS 的*【为什么】*和*【什么】*问题，让我们开始吧。

### 为什么要创造 PlazarJS？

让我们看看，在我几年前的第一个 Angular 项目中，我想它是 *AngularJS v1.2.22* ，所以这几年多来，我爱上了 thigs 是如何完成和实现的。我是 MVC 概念的忠实粉丝，所以我很快就接受了视角和做事的方式。后来，当这些组件出现时，一切都变得更好了。所以，我猜我开始研究 PlazarJS 的第一个原因是自学。我过去和现在都渴望获得更多关于软件行业的知识。

过了一段时间，当我看到我正在编写的代码的可重用性和潜力时，我开始思考:*“嘿，有人可能真的愿意在他自己的项目上使用这个”*，当我觉得已经准备好了，我决定将项目分享给社区。所以，第二个原因是我想回报给我这么多东西的社区。

我就说到这里，因为我还有更多要写的。我们不希望这个帖子变成小说！

### 什么是 PlazarJS？

> PlazarJS 是一个轻量级、通用的 JavaScript 框架。它旨在从应用程序开发的简单性和速度方面丰富开发人员的体验。该框架本身没有依赖性，通过学习面向对象的原则(OOP ),它可以很容易地用于创建一个大型的单页应用程序，或者它可以集成到需要动态工作流的网页的一部分。它是用普通的 JavaScript 编写的，非常灵活。

上面的文字引自官方文档网站，如果我们有意义地阅读它，我们可以提取关于框架的以下结论:

*   *轻量级*–在页面上放置一个脚本标签，一切就绪；还增加了 umd 支持
*   *多功能*–可以创建内嵌模板，从服务器下载模板，或者连接到预渲染的 html。支持客户端和服务器呈现
*   *不依赖*–只需要 JavaScript
*   *Modern*——它用 ECMAScript 6 编写，用 babel 编译，由 lerna/rollup/uglifyjs combo 维护
*   OOP(面向对象的编程)——容易扩展和继承的组件，我说的不仅仅是混合，我们可以扩展一个类型，在子类型中覆盖它的方法，并从覆盖中调用父方法
*   灵活 -可以满足您的需求，表现得像一个库或一个整体框架
*   可伸缩的(Scalable)——通常，这与 OOP 一起使用；你可以通过扩展框架核心来扩展你的应用

现在，当我们对框架有了一个基本的答案和理解，我们就可以用一个例子来支持一切。正如本文开头所承诺的，我们将创建一个示例组件。

该示例将包含一个布局组件，其中页眉、正文和页脚作为子组件。页眉和正文将以声明的方式添加，这意味着它们将由其父组件创建，页脚将在以后动态添加，例如，在初始化布局组件时。我们的主要组件模板将被预先呈现在页面上，其他的将被声明为内联模板。另一种选择是从服务器上下载一个模板，但是我将在另一篇文章中讨论这个问题。

让我们从在页面上放置我们的布局组件模板开始(我们说过它将被预渲染，对吗？):

```
// For the purpose of this example, we will configure the component 
// to select its html element by CSS class.
// Note that we could use any CSS selector
<section class="layout-component"></section> 
```

好了，现在我们已经准备好了主模板，我们可以开始设计我们的子组件模板了。比方说，在我们的页眉中我们想要一个菜单，在正文中我们想要一个通过 viewmodel 绑定的欢迎文本，在页脚中我们想要一个版权信息。

表头模板:

```
<header>
    <ul> // Note that this menu could be yet another component
        <li>Home</li> 
        <li>...</li>
        // more dynamic items
    </ul>
</header> 
```

正文模板:

```
<main>
    <p>{text}</p>
</main> 
```

页脚模板:

```
<footer>
    <span>Copyright &copy; 2018-present, John Doe</span>
</footer> 
```

现在喝一口咖啡，让自己活跃起来。我们要一起包装它。在此之前，提到一个负责定义应用程序中每种类型的函数是至关重要的。这个函数位于全局名称空间`pz`下，它被称为`define`。通过调用它，我们将创建我们的组件。此外，在定义一个类、一个 mixin 或任何其他自定义类型时会用到这个方法，但是我会在我的下一篇文章中介绍这个方法。

这个方法将确保相应的类型被定义并作为类型定义存储在我们的应用程序中。稍后，在运行时，我们可以使用像`pz.getDefinitionOf`和`pz.getInstanceOf`这样的方法来重用或提取我们的类型或实例，但是这些方法是另一篇文章的主题。

要定义一个组件，我们需要将我们定义的类型的`ownerType`设置为`component`。除了`class`和`mixin`之外，`component`类型是我们可以使用的核心框架类型之一。在我的下一篇文章中，我将介绍自定义类型以及如何扩展它们。

下面的代码演示了我们的布局组件及其子组件:

```
pz.define('header-component', {
    ownerType: 'component',
    template: '<header><ul></ul></header>',
    menuItems:[],
    init: function() {
        this.base(arguments); // this will call the init method of the parent type
        pz.forEach(this.menuItems, function(menuItem) {
            var li = pz.dom.parseTemplate('<li><a href="' + menuItem.href + '">' + 
                    menuItem.text + '<a></li>');
            var list = pz.dom.findElement(this.html, 'ul');
            pz.dom.append(list, li);
        }, this); // "this" is passed as a scope variable, it's not required
        // here, we could have used a viewmodel to bind the items via data-each binding
    }
});

pz.define('body-component', {
    ownerType: 'component',
    template: '<main><p>{text}</p></main>',
    viewModel: {
        text: 'Hi, I wish to welcome you to the PlazarJS example. I hope you will like it'
    }
});

pz.define('footer-component', {
    ownerType: 'component',
    template: '<footer><span>Copyright &copy; 2018-present, John Doe</span></footer>'
});

pz.define('layout-component', {
    ownerType: 'component',
    templateSelector: 'section.layout-component',
    autoLoad: true,
    components:[{
        type: 'header-component',
        menuItems:[{
            text: 'Home',
            href: '#'
        },{
            text: 'About',
            href: '#'
        },{
            text: 'Contact',
            href: '#'
        }]
    }, {
        type: 'body-component'
    }],
    init: function() {
        this.base(arguments);
        this.addChild({
            type: 'footer-component'
        })
    }
}).create(); 
```

因为我们没有定义 SPA，并且我们可以通过使用一个名为`pz.defineApplication`的覆盖来实现，所以我们调用了静态方法`create`。这个方法要做的是，它将在组件被定义后立即创建组件。子组件的初始化和创建由布局组件处理。

静态方法`create`只在定义中可用，希望我会在另一篇文章中介绍它。

如果我们看一下页面源代码，我们会看到我们的组件在我们的布局组件中按照预期呈现。

好了，我们已经到了这篇文章的结尾。为了帮助您理解 PlazarJS 框架的工作原理，我尽量做到简洁明了。

查看官方[文档](http://www.plazarjs.com/)网站，或者，如果你愿意，访问 [github 页面](https://github.com/ProticM/plazar-js)。

感谢您的阅读，祝您好运。