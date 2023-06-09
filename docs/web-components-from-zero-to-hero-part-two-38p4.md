# Web 组件:从零到英雄，第二部分

> 原文：<https://dev.to/thepassle/web-components-from-zero-to-hero-part-two-38p4>

# Web 组件:从零到英雄，下篇

## *用 lit-html 增压 web 组件*

*   [Lit-html](#-lit-html)
*   [实践中的 Lit-html](#-lit-html-in-practice)
*   [增压我们的组件](#-supercharging-our-component)
*   [属性、特性和事件](#-attributes-properties-and-events)
*   [结束](#-wrapping-up)

如果您已经阅读了本博客系列的第一部分,那么现在您应该知道 web 组件的基础知识了。如果您还没有，我建议您回到第一部分并赶上进度，因为我们将再次回顾，并在第一部分中涉及的许多概念的基础上进行构建。

在这篇博文中，我们将用一个名为 [lit-html](https://github.com/Polymer/lit-html) 的渲染库来增强我们的待办事项应用程序。但是在我们开始之前，有几件事我们需要讨论一下。如果你已经密切关注，你会注意到我之前提到我们的 web 组件是一个*原始的* web 组件。我这样做了，因为 web 组件是*低级*，并且不包括模板或其他设计的特性*。Web 组件总是旨在作为标准的集合，完成平台还不允许的非常具体的事情。*

我想引用 Justin Fagnani 的话来说，所有的 web 组件所做的就是给开发人员一个当时的*和一个当*时的*。*时的*为元素创建、实例化、连接、断开等。*其中*是元素实例和阴影根。你怎么做取决于你自己。*

另外，lit-html 是*而不是*一个框架。它只是一个利用标准 javascript 语言特性的 javascript 库。库和框架之间的区别经常是一个有争议的话题，但是我想用[戴夫·切尼](https://twitter.com/davecheney/status/1058502133530542080)的比喻来定义它:

> 框架是基于好莱坞模式的；别打电话给我们，我们会打给你的。

Lit-html 也是极其轻量级的，只有 2kb 的<，并且使*变得很快*。

既然我们已经解决了这个问题，让我们看看 lit-html 是如何工作的。

## 🔥Lit-html

> *   【x】了解 Lit-html
> *   [] Lit-html in practice
> *   [] Pressurize our web components
> *   [] Properties, attributes and events
> *   [] packaging

Lit-html 是一个渲染库，允许您使用 javascript 模板文字编写 html 模板，并高效地将这些模板渲染和重新渲染到 DOM。带标签的模板文字是 ES6 的一个特性，它可以跨越多行，并且包含 javascript 表达式。一个带标签的模板文字可能看起来像这样:

```
const planet = "world";

html`hello ${planet}!`; 
```

标记的模板文字只是标准的 ES6 语法。而这些标签其实只是函数！考虑下面的例子:

```
function customFunction(strings) {
    console.log(strings); // ["Hello universe!"]
}

customFunction`Hello universe!`; 
```

它们还可以处理表达式:

```
const planet = "world";

function customFunction(strings, ...values) {
    console.log(strings); // ["Hello ", "! five times two equals "]
    console.log(values); // ["world", 10]
}

customFunction`Hello ${planet}! five times two equals ${ 5 * 2 }`; 
```

如果我们查看源代码，我们可以看到 lit-html 也是这样工作的:

```
/**
 * Interprets a template literal as an HTML template that can efficiently
 * render to and update a container.
 */
export const html = (strings: TemplateStringsArray, ...values: any[]) =>
    new TemplateResult(strings, values, 'html', defaultTemplateProcessor); 
```

现在如果我们像这样写:

```
const planet = "world";

function customFunction(strings, ...values) {
    console.log(strings); // ["<h1>some static content</h1><p>hello ", "</p><span>more static content</span>"]
    console.log(values); // ["world"]
}

customFunction`
    <h1>some static content</h1>
    <p>hello ${planet}</p>
    <span>more static content</span>    
`; 
```

您会注意到，当我们将`strings`和`values`记录到控制台时，我们已经将模板的静态内容和动态部分分开了。当我们想要跟踪变化，并用相应的数据更新我们的模板时，这是很棒的，因为它允许我们只观察*动态部分*的变化。这与 VDOM 的工作方式也有很大的不同，因为我们已经知道*`<h1>`和`<span>`是静态的，所以我们不需要对它们做任何事情。我们只对动态部分感兴趣，它可以是任何 javascript 表达式。*

 *因此，lit-html 采用您的模板，用名为`Part` s 的通用占位符替换所有表达式，并在结果中生成一个`<template>`元素。所以我们现在有了一个 HTML 模板，它知道应该把接收到的数据放在哪里。

```
<template>
    <h1>some static content</h1>
    <p>hello {{}}</p> <-- here's our placeholder, or `Part`
    <span>more static content</span>    
</template> 
```

Lit 会记住这些占位符的位置，这使得更新变得简单而高效。Lit 还将有效地重用`<template>` s:

```
const sayHello = (name) => html`
    <h1>Hello ${name}</h1>
`;

sayHi('world');
sayHi('universe'); 
```

这两个模板将共享完全相同的`<template>`以提高效率，唯一不同的是我们传递的数据。如果您密切关注，您会记得我们在本博客系列的第一部分中使用了相同的技术。

我们模板的动态`Part`可以是*任何* javascript 表达式。Lit-html 甚至不需要做任何魔法来评估我们的表达式，javascript 只是为我们做了这些。以下是一些例子:

简单:

```
customFunction`<p>${1 + 1}</p>`; // 2 
```

条件句:

```
customFunction`<p>${truthy ? 'yes' : 'no'}</p>`; // 'yes' 
```

我们甚至可以使用数组和嵌套:

```
customFunction`<ul>${arr.map(item => customFunction`<li>${item}</li>`)}</ul>`; 
```

## 🚀实践中的 Lit-html

> *   【x】了解 Lit-html
> *   [x] LIT-HTML in practice
> *   [Supercharge] Our web components
> *   [Attributes, characteristics and events
> *   [packaging]

让我们看看这在实践中是如何工作的:

[![updating](img/40be9397aa800dc031bd8945a41e0884.png)T2】](https://stackblitz.com/edit/web-components-zero-to-hero-counter?file=demo-element.js)

你可以在这里或者在 [github](https://github.com/thepassle/webcomponents-from-zero-to-hero/tree/part-two/counter-demo) 上看到完整的演示。

```
import { html, render } from 'lit-html';

class DemoElement extends HTMLElement {
  constructor() {
    super();
    this._counter = 0;
    this._title = "Hello universe!";
    this.root = this.attachShadow({ mode: "open"});
    setInterval(() => {this.counter++}, 1000);
  }

  get counter() {
    return this._counter;
  }

  set counter(val) {
    this._counter = val;
    render(this.template(), this.root);
  }

  template() {
    return html`
      <p>Some static DOM</p>
      <h1>${this.counter}</h1>
      <h2>${this._title}</h2>
      <p>more static content</p>
    `;
  }
}

window.customElements.define('demo-element', DemoElement); 
```

如果你已经阅读了本系列的第一篇博文，这应该看起来很熟悉。我们制作了一个简单的 web 组件，每秒钟递增一个计数器，我们实现了 lit-html 来处理我们的渲染。

有趣的地方在这里:

```
 return html`
      <p>Some static DOM</p>
      <h1>${this.counter}</h1>
      <h2>${this._title}</h2>
      <p>more static content</p>
    `; 
```

DOM 中的输出:

[![litoutput](img/8d5267ed5bfb1a9104be565d52a2a8c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ocDIgETP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thepassle.nl/SGTEST/litrenderingoutput.png)

我们现在可以看到 lit *only* 是如何更新我们代码中已经改变的部分(`this.counter`)的，甚至不去理会静态部分。它完成所有这一切不需要任何框架魔法或 VDOM，并且库大小不到 2kb！您还可能注意到输出中有一堆 HTML 注释；不用担心，这就是 lit-html 跟踪静态和动态部分的方式。

## ⚡️增压我们的分量

> *   【x】了解 Lit-html
> *   [x] LIT-HTML in practice
> *   【x】超级收费我们的网组件
> *   [] Properties, attributes and events
> *   [packaging]

现在我们知道了 lit-html 是如何渲染的，让我们把它付诸实践。你可以在这里和 [github](https://github.com/thepassle/webcomponents-from-zero-to-hero/tree/part-two) 上看到完整的代码[。我们将一步一步地完成这一过程，但首先让我们对我们的增压组件有一个概述:](https://stackblitz.com/edit/web-components-zero-to-hero-part-two)

`to-do-app.js` :

```
import { html, render } from 'lit-html';
import './to-do-item.js';

class TodoApp extends HTMLElement {
    constructor() {
        super();
        this._shadowRoot = this.attachShadow({ 'mode': 'open' });

        this.todos = [
        { text: 'Learn about Lit-html', checked: true },
        { text: 'Lit-html in practice', checked: false },
        { text: 'Supercharge our web component', checked: false },
        { text: 'Attributes, properties, and events', checked: false },
        { text: 'Wrapping up', checked: false }
    ];

        render(this.template(), this._shadowRoot, {eventContext: this});

        this.$input = this._shadowRoot.querySelector('input');
    }

    _removeTodo(e) {
      this.todos = this.todos.filter((todo,index) => {
          return index !== e.detail;
      });
    }

    _toggleTodo(e) {
      this.todos = this.todos.map((todo, index) => {
          return index === e.detail ? {...todo, checked: !todo.checked} : todo;
      });
    }

    _addTodo(e) {
      e.preventDefault();
      if(this.$input.value.length > 0) {
          this.todos = [...this.todos, { text: this.$input.value, checked: false }];
          this.$input.value = '';
      }
    }

    template() {
        return html`
            <style>
                :host {
                    display: block;
                    font-family: sans-serif;
                    text-align: center;
                }
                button {
                    border: none;
                    cursor: pointer;
                    background-color: Transparent;
                }
                ul {
                    list-style: none;
                    padding: 0;
                }
            </style>
            <h3>Raw web components + lit-html</h3>
            <br>
            <h1>To do</h1>
            <form id="todo-input">
                <input type="text" placeholder="Add a new to do"></input>
                <button @click=${this._addTodo}>✅</button>
            </form>
            <ul id="todos"> ${this.todos.map((todo, index) => html`
                    <to-do-item 
                        ?checked=${todo.checked} .index=${index} text=${todo.text} @onRemove=${this._removeTodo} @onToggle=${this._toggleTodo}>    
                    </to-do-item>
                  `
              )} </ul>
        `;
    }

    set todos(value) {
        this._todos = value;
        render(this.template(), this._shadowRoot, {eventContext: this});
    }

    get todos() {
        return this._todos;
    }
}

window.customElements.define('to-do-app', TodoApp); 
```

大致了解了吗？太好了！您会发现我们的代码中有相当多的地方发生了变化，所以让我们仔细看看。

您可能已经注意到的第一件事是，我们处理组件呈现的方式已经完全改变了。在我们的旧应用程序中，我们必须强制性地创建一个`template`元素，设置它的 innerHTML，克隆它，并将其附加到我们的 shadowroot。当我们想要更新我们的组件时，我们必须创建一堆元素，设置它们的属性，添加它们的事件侦听器，并将它们附加到 DOM 中。全靠手工。光是读这个我就头疼。相反，我们所做的是将所有的渲染委托给 lit-html。

现在我们只声明我们的模板一次，我们可以在模板中声明性地设置属性、特性和事件*，需要时只需调用 lit-html 的`render`函数。lit-html 的伟大之处在于它在渲染方面*快速*和*高效*；它只查看动态表达式，并且只改变*需要*更新的内容。而这一切都没有框架的开销！*

 *您还会注意到，我们将我们的`_addTodo`、`_removeTodo`和`_toggleTodo`方法改为一些不可变的更新模式。这很好，因为每次我们`set`了`todos`的值，我们将触发我们组件的渲染。这是一个重要的概念，我们将在本博客系列的第三部分，也是最后一部分，进一步探讨。

## 🔨属性、特性和事件

> *   【x】了解 Lit-html
> *   [x] LIT-HTML in practice
> *   【x】超级收费我们的网组件
> *   [x] Attributes, characteristics, events
> *   [packaging

让我们继续来看看 lit-html 是如何处理属性、特性和事件的。

```
${this.todos.map((todo, index) => {
    return html`
        <to-do-item 
            ?checked=${todo.checked} .index=${index} text=${todo.text} @onRemove=${this._removeTodo} @onToggle=${this._toggleTodo}>    
        </to-do-item>
    `;
})} 
```

您可能在我们组件的更新版本中看到过这种奇怪的语法，并想知道它的含义。Lit-html 允许我们以声明的方式*在模板中设置我们的属性、特性和事件处理程序，而不是强制性地设置它们。既然我们已经在本系列的第一部分学习了所有关于属性、特性和事件的知识，这应该很容易理解。如果你需要复习，[我帮你搞定了](https://github.com/thepassle/webcomponents-from-zero-to-hero/tree/master/part-one#-reflecting-properties-to-attributes)。*

让我们一步一步地了解所有这一切。

### 💅属性

```
text=${todo.text} 
```

我们在 lit-html 中设置了*属性*...就像在标准 HTML 中设置属性一样。唯一的区别是我们在模板字符串中使用了一个动态值。我知道，非常虎头蛇尾。我们以前必须像这样设置我们的属性:`el.setAttribute('text', todo.text);`。

> ✨ *嘿！听着！*
> 
> 常规属性仍然只限于字符串类型！

### ☑️布尔属性

```
?checked=${todo.checked} 
```

正如您在上一篇博文中所记得的，布尔属性的处理方式通常有点不同...

> #### ✨ *闪回* ✨
> 
> ...
> 
> *这意味着只有以下示例可接受真值:*
> 
> ```
> <div hidden></div>
> <div hidden=""></div>
> <div hidden="hidden"></div> 
> ```
> 
> *和一个假:*
> 
> ```
> <div></div> 
> ```

非常方便的是，lit-html 允许我们通过在属性名前面加上一个`?`来轻松地将我们的属性指定为一个*布尔型*属性，然后确保该属性要么出现在元素上，要么不出现。

之前，我们将布尔属性设置为:

```
if(todo.checked){
    el.setAttribute('checked', '');
} 
```

当我们的条件为假时，就把它完全省略了。

### 📂性能

```
.index=${index} 
```

如果我们想要传递一些丰富的数据，比如数组或对象，或者在本例中是一个数值，我们可以简单地使用点前缀。

以前，要在组件上设置*属性*，我们必须强制查询组件，并设置属性。多亏了 lit-html，我们可以在模板中处理所有这些。

之前我们将属性设置为:

```
el.index = index; 
```

### 🎉事件

```
@onRemove=${this._removeTodo} 
```

最后，我们可以通过给事件侦听器加上前缀`@`来声明性地指定它们。每当`to-do-item`组件触发一个`onRemove`事件时，`this._removeTodo`就会被调用。很简单。

再给你一个例子，我们如何处理点击事件:

```
<button @click=${this._handleClick}></button> 
```

> ✨ *嘿！听着！*
> 
> 注意我们是如何在我们的`render()`函数:`render(this.template(), this._shadowRoot, {eventContext: this});`中指定了一个`eventContext`。这确保了我们在事件处理程序中总是有对`this`的正确引用，这样我们就不必在`constructor`中手动`.bind(this)`我们的事件处理程序。

## 💭包扎

> *   【x】了解 Lit-html
> *   【x】Lit-html 实践
> *   [x] Pressurize our web components
> *   [x] Attributes, characteristics and events
> *   [x] packaging

如果您一路走到了这里，那么您正在成为真正的 Web 组件英雄。您已经了解了 lit-html，lit-html 如何呈现，如何使用属性、特性和事件，以及如何实现 lit-html 来处理 Web 组件的呈现。

干得好！我们增强了我们的 web 组件，它现在可以有效地呈现待办事项，但是我们仍然有一堆样板代码，还有很多属性和属性管理要处理。如果有更简单的方法来处理这一切，那就太好了...

...什么？

...它是一只鸟吗？🐦

...它是一架飞机吗？✈️

它是...

### 💥床上用品💥

我们将在本博客系列的下一部分，也是最后一部分讨论这个问题。感谢阅读！**