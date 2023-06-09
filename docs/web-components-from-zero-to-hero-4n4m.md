# Web 组件:从零到英雄

> 原文：<https://dev.to/thepassle/web-components-from-zero-to-hero-4n4m>

# Web 组件:从零到英雄

## *编写原始 web 组件简介*

*   什么是 web 组件？
*   [组件生命周期](#-a-components-lifecycle)
*   [构建我们的待办事项应用](#-building-our-to-do-app)
*   [设置属性](#-setting-properties)
*   [设置属性](#-setting-attributes)
*   [将属性反映到属性](#-reflecting-properties-to-attributes)
*   [事件](#-events)
*   [浏览器支持和聚合填充](#-browser-support-and-polyfills)
*   [结束](#wrapping-up)

Web 组件越来越受欢迎。随着 Edge 团队最近宣布实现自定义元素和 Shadow DOM，所有主流浏览器都将很快本机支持 web 组件。Github、网飞、Youtube 和 ING 等公司甚至已经在生产中使用 web 组件。整洁！然而，令人惊讶的是，这些成功的大公司中没有一家实现了(你猜对了)*待办事项应用！*

所以今天，我们将制作一个待办应用，因为这个世界还没有足够多的待办应用的实现。你可以在这里看看我们将要制作的[。](https://thepassle.github.io/webcomponents-from-zero-to-hero/)

在我们开始之前，我想补充一点声明，这篇博客旨在更好地掌握 web 组件的基础知识。Web 组件是低级的，在不使用任何助手库的情况下，可能不应该用来编写成熟的应用程序，也不应该将它们与成熟的框架相比较。

## 🙋什么是 web 组件？

> *   [x] Make a demo
> *   [Boring things]
> *   [set attribute]
> *   [set attribute]
> *   [reflect attribute] to attribute
> *   [event]
> *   [Summary]

首先: [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)是一组标准，允许我们编写模块化、可重用和封装的 HTML 元素。它们最好的一点是:因为它们是基于 web 标准的，我们不需要安装任何框架或库就可以开始使用它们。您现在就可以开始使用普通的 javascript 编写 web 组件了！

但是在我们开始动手之前，让我们看一下让我们编写 web 组件的规范。

### 自定义元素

自定义元素 api 允许我们创作自己的 DOM 元素。使用 api，我们可以定义一个自定义元素，并通知解析器如何正确地构造该元素，以及该类的元素应该如何对变化做出反应。你有没有想要自己的 HTML 元素，比如`<my-cool-element>`？现在你可以了！

### 阴影 DOM

Shadow DOM 为我们提供了一种封装组件样式和标记的方法。它是一个附属于 DOM 元素的子 DOM 树，以确保我们的样式不会泄露，或者被任何外部样式覆盖。这使得它非常适合模块化。

### ES 模块

ES 模块规范以基于标准、模块化、高性能的方式定义了 JS 文档的包含和重用。

### HTML 模板

HTML `<template>` 标签允许我们编写可重用的 DOM 块。在模板内部，脚本不会运行，图像不会加载，样式/标记也不会呈现。模板标签本身甚至不被认为是在文档中，*直到它被激活*。HTML 模板很棒，因为对于我们元素的每个实例，只使用了一个模板。

现在我们知道了 web 组件利用了哪些规范，让我们来看看定制元素的生命周期。我知道，我知道，我们很快就会找到密码的！

## ♻️一个组件的生命周期

让我们看一下定制元素的生命周期。考虑以下元素:

```
class MyElement extends HTMLElement {
    constructor() {
        // always call super() first
        super(); 
        console.log('constructed!');
    }

    connectedCallback() {
        console.log('connected!');
    }

    disconnectedCallback() {
        console.log('disconnected!');
    }

    attributeChangedCallback(name, oldVal, newVal) {
        console.log(`Attribute: ${name} changed!`);
    }

    adoptedCallback() {
        console.log('adopted!');
    }
}

window.customElements.define('my-element', MyElement); 
```

Enter fullscreen mode Exit fullscreen mode

### 构造函数()

每当创建一个元素时，`constructor`就会运行，但是在元素被附加到文档之前，*。我们将使用`constructor`来设置一些初始状态、事件监听器和创建影子 DOM。*

### connectedCallback()

当元素被插入 DOM 时，调用`connectedCallback`。这是运行设置代码的好地方，比如获取数据，或者设置默认属性。

### disconnectedCallback()

每当从 DOM 中移除元素时，就会调用`disconnectedCallback`。清理时间！我们可以使用`disconnectedCallback`删除任何事件监听器，或者取消间隔。

### attributeChangedCallback(名称，旧值，新值)

每当你的元素的*观察到的属性*改变时，就会调用`attributeChangedCallback`。我们可以通过实现一个静态的`observedAttributes` getter 来观察元素的属性，比如:

```
static get observedAttributes() {
    return ['my-attr'];
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，任何时候`my-attr`属性被改变，`attributeChangedCallback`就会运行。在这篇博文的后面，我们将对此进行更深入的探讨。

> ✨ *嘿！听着！*
> 
> 只有列在`observedAttributes` getter 中的属性在`attributeChangedCallback`中受到影响。

### 采用了回调()

每次将自定义元素移动到新文档时，都会调用 adoptedCallback。只有当您的页面中有`<iframe>`元素时，您才会遇到这种用例。

### 登记本元素

最后，虽然不是生命周期的一部分，但我们将元素注册到`CustomElementRegistry`中，就像这样:

```
window.customElements.define('my-element', MyElement); 
```

Enter fullscreen mode Exit fullscreen mode

`CustomElementRegistry`是一个接口，它提供了注册定制元素和查询注册元素的方法。registries 的`define`方法的第一个参数将是元素的名称，所以在这种情况下，它将注册`<my-element>`，第二个参数传递我们创建的类。

> ✨ *嘿！听着！*
> 
> 注意我们如何命名我们的 web 组件是很重要的。自定义元素名称必须始终包含连字符。比如:`<my-element>`正确，`<myelement>`不正确。这样做是为了避免元素名称冲突，并区分定制元素和常规元素。
> 
> 自定义元素也不能自结束，因为 HTML 只允许少数元素自结束。这些被称为 *void 元素*，像`<br/>`或`<img/>`，或者不允许子节点的元素。
> 
> 允许自结束元素需要改变 HTML 解析器，这是一个问题，因为 HTML 解析是安全敏感的。为了能够实现 XSS 安全的 HTML 生成，HTML 制作者需要能够依赖于给定的 HTML 片段是如何解析的。

## ⚒打造我们的待办 app

> *   [x] Be a demo
> *   [x] Boring stuff
> *   [set attribute]
> *   [set attribute]
> *   [reflect attribute] to attribute
> *   [event]
> *   [Summary]

既然我们已经完成了所有无聊的事情，我们终于可以开始动手制作我们的待办事项应用程序了！点击[此处](https://thepassle.github.io/webcomponents-from-zero-to-hero/)查看最终结果。

让我们从概述我们将要构建的内容开始。

*   一个`<to-do-app>`元素:

    *   包含待办事项数组作为属性
    *   添加待办事项
    *   删除待办事项
    *   切换待办事项
*   一个`<to-do-item>`元素:

    *   包含描述*属性*
    *   包含一个索引*属性*
    *   包含选中的*属性*

太好了！让我们为我们的待办事项应用奠定基础:

`to-do-app.js` :

```
const template = document.createElement('template');
template.innerHTML = `
<style>
    :host {
    display: block;
    font-family: sans-serif;
    text-align: center;
    }

    button {
    border: none;
    cursor: pointer;
    }

    ul {
    list-style: none;
    padding: 0;
    }
</style>
<h1>To do</h1>

<input type="text" placeholder="Add a new to do"></input>
<button>✅</button>

<ul id="todos"></ul>
`;

class TodoApp extends HTMLElement {
    constructor() {
        super();
        this._shadowRoot = this.attachShadow({ 'mode': 'open' });
        this._shadowRoot.appendChild(template.content.cloneNode(true));
        this.$todoList = this._shadowRoot.querySelector('ul');
    }
}

window.customElements.define('to-do-app', TodoApp); 
```

Enter fullscreen mode Exit fullscreen mode

我们要一步一步来。我们首先通过调用`const template = document.createElement('template');`创建一个`<template>`，然后在其中设置一些 HTML。我们只在模板*上设置一次*的 innerHTML。我们使用模板的原因是因为克隆模板比调用组件的所有实例的`.innerHTML`要便宜得多。

接下来，我们可以开始定义我们的元素了。我们将使用我们的`constructor`到*连接*我们的 shadowroot，并且我们将它设置为`open`模式。然后我们将*克隆*我们的模板到我们的 shadowroot。酷！我们现在已经使用了 2 个 web 组件规范，并且成功地创建了一个封装的子 DOM 树。

这意味着我们现在有了一个不会泄露任何样式或者覆盖任何样式的 DOM 树。考虑下面的例子:

[![encapsulation](img/44d95da467811c5dabab7cce6c8076dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--96ixDcGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/llF84Na.png)

我们有一个全局的`h1`样式，使得光线 DOM 中的任何 h1 为红色。但是因为我们在影子根中有 h1，所以它不会被全局样式覆盖。

注意在我们的`to-do-app`组件中，我们使用了一个`:host`伪类，这是我们如何从内部给组件添加样式的。需要注意的重要一点是，`display`总是被设置为`display: inline;`，这意味着你不能在你的元素上设置宽度或高度。因此，确保设置一个`:host`显示样式(例如，块、内嵌块、伸缩)，除非你更喜欢默认的内嵌。

> ✨ *嘿！听着！*
> 
> 阴影 DOM 可能会有点混乱。请允许我稍微扩展一下术语:
> 
> ### 光 DOM:
> 
> 光 DOM 存在于组件的阴影 DOM 之外，基本上是任何不是*而不是*阴影 DOM 的东西。例如，上面的`<h1>Hello world</h1>`生活在光明世界。光 DOM 这个术语是用来区别于阴影 DOM 的。使用轻量级 DOM 制作 web 组件完全没问题，但是您错过了影子 DOM 的伟大特性。
> 
> ### 打开阴影 DOM:
> 
> 由于最新版本(V1)的阴影 DOM 规范，我们现在可以使用`open`或`closed`阴影 DOM。Open shadow DOM 允许我们在 light DOM 旁边创建一个子 DOM 树，为我们的组件提供封装。我们的影子 DOM 仍然可以被 javascript 穿透，比如:`document.querySelector('our-element').shadowRoot`。影子 DOM 的一个缺点是 web 组件仍然相对年轻，许多外部库没有考虑到它。
> 
> ### 闭影 DOM:
> 
> 封闭的影子根不太适用，因为它会阻止任何外部 javascript 穿透影子根。封闭的影子 DOM 使您的组件对您自己和您的最终用户来说不太灵活，通常应该避免。
> 
> 使用封闭阴影 DOM 的元素的一些例子是`<video>`元素。

## 📂设置属性

酷毙了。我们已经制作了第一个 web 组件，但是到目前为止，它完全没有用。如果能够向它传递一些数据并呈现一个待办事项列表就好了。

让我们实现一些 getters 和 setters。

`to-do-app.js` :

```
class TodoApp extends HTMLElement {
    ...

    _renderTodoList() {
        this.$todoList.innerHTML = '';

        this._todos.forEach((todo, index) => {
            let $todoItem = document.createElement('div');
            $todoItem.innerHTML = todo.text; 
            this.$todoList.appendChild($todoItem);
        });
    }

    set todos(value) {
        this._todos = value;
        this._renderTodoList();
    }

    get todos() {
        return this._todos;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些 getters 和 setters，我们可以将一些丰富的数据传递给我们的元素了！我们可以查询我们的组件并像这样设置数据:

```
document.querySelector('to-do-app').todos = [
    {text: "Make a to-do list", checked: false}, 
    {text: "Finish blog post", checked: false}
]; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经成功地在组件上设置了一些*属性*，现在看起来应该是这样的:

[![todolist](img/8c36db10cc3bf09a71a8e421f160b45d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4-gWFRVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/I0IkxNp.png)

太好了！除了它仍然是无用的，因为我们不能不使用控制台与任何东西互动。让我们快速实现一些功能，将新的待办事项添加到我们的列表中。

```
class TodoApp extends HTMLElement {
    ...

    constructor() {
        super();
        this._shadowRoot = this.attachShadow({ 'mode': 'open' });
        this._shadowRoot.appendChild(template.content.cloneNode(true));

        this.$todoList = this._shadowRoot.querySelector('ul');
        this.$input = this._shadowRoot.querySelector('input');

        this.$submitButton = this._shadowRoot.querySelector('button');
        this.$submitButton.addEventListener('click', this._addTodo.bind(this));
    }

    _addTodo() {
        if(this.$input.value.length > 0){
            this._todos.push({ text: this.$input.value, checked: false })
            this._renderTodoList();
            this.$input.value = '';
        }
    }

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这应该很容易做到，我们在我们的`constructor`中设置了一些`querySelectors`和`addEventListeners`，在一个点击事件中，我们希望将输入推送到我们的待办事项列表中，呈现它，并再次清除输入。湿疹（eczema 的简写）👏。

[![add](img/10ae35ec4f8731eec3bd52c131c94aa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xAbqCyK7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/v7Qzi8b.png)

## 💅设置属性

> *   [x] Be a demo
> *   [x] Boring stuff
> *   [x] Setting attributes
> *   [Set attributes
> *   [reflection attribute] to attribute
> *   [Event
> *   [Summary]

这就是事情变得令人困惑的地方，因为我们将探索*属性*和*属性*之间的差异，并且我们还将成为*，将属性反映到属性*。抓紧了！

首先，让我们创建一个`<to-do-item>`元素。

`to-do-item.js` :

```
const template = document.createElement('template');
template.innerHTML = `
<style>
    :host {
    display: block;
    font-family: sans-serif;
    }

    .completed {
    text-decoration: line-through;
    }

    button {
    border: none;
    cursor: pointer;
    }
</style>
<li class="item">
    <input type="checkbox">
    <label></label>
    <button>❌</button>
</li>
`;

class TodoItem extends HTMLElement {
    constructor() {
        super();
        this._shadowRoot = this.attachShadow({ 'mode': 'open' });
        this._shadowRoot.appendChild(template.content.cloneNode(true));

        this.$item = this._shadowRoot.querySelector('.item');
        this.$removeButton = this._shadowRoot.querySelector('button');
        this.$text = this._shadowRoot.querySelector('label');
        this.$checkbox = this._shadowRoot.querySelector('input');

        this.$removeButton.addEventListener('click', (e) => {
            this.dispatchEvent(new CustomEvent('onRemove', { detail: this.index }));
        });

        this.$checkbox.addEventListener('click', (e) => {
            this.dispatchEvent(new CustomEvent('onToggle', { detail: this.index }));
        });
    }

    connectedCallback() {
        // We set a default attribute here; if our end user hasn't provided one,
        // our element will display a "placeholder" text instead.
        if(!this.hasAttribute('text')) {
            this.setAttribute('text', 'placeholder');
        }

        this._renderTodoItem();
    }

    _renderTodoItem() {
        if (this.hasAttribute('checked')) {
            this.$item.classList.add('completed');
            this.$checkbox.setAttribute('checked', '');
        } else {
            this.$item.classList.remove('completed');
            this.$checkbox.removeAttribute('checked');
        }

        this.$text.innerHTML = this._text;
    }

    static get observedAttributes() {
        return ['text'];
    }

    attributeChangedCallback(name, oldValue, newValue) {
        this._text = newValue;
    }
}
window.customElements.define('to-do-item', TodoItem); 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，由于我们使用的是 ES 模块，我们可以再次使用`const template = document.createElement('template');`，而无需覆盖我们之前制作的模板。

让我们把`to-do-app.js`中的`_renderTodolist`函数改成这个:

```
class TodoApp extends HTMLElement {

        ...

        _renderTodoList() {
            this.$todoList.innerHTML = '';

            this._todos.forEach((todo, index) => {
                let $todoItem = document.createElement('to-do-item');
                $todoItem.setAttribute('text', todo.text);
                this.$todoList.appendChild($todoItem);
            });
        }

        ...

    } 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这里发生了很多不同的事情。让我们开始吧。以前，当传递一些*丰富的数据*(一个数组)给我们的`<to-do-app>`组件时，我们这样设置它:

```
document.querySelector('to-do-app').todos = [{ ... }]; 
```

Enter fullscreen mode Exit fullscreen mode

我们这样做是因为`todos`是元素的*属性*。*属性*处理方式不同，不允许富数据，实际上只允许一个字符串类型作为 HTML 的限制。属性更加灵活，可以处理复杂的数据类型，如对象或数组。

区别在于属性是在 HTML 元素上定义的。当浏览器解析 HTML 时，会创建一个相应的 DOM 节点。这个节点是一个对象，因此它有*属性*。比如浏览器解析:`<to-do-item index="1">`时，会创建一个 [HTMLElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement) 对象。该对象已经包含了几个属性，如`children`、`clientHeight`、`classList`等，以及一些方法，如`appendChild()`或`click()`。我们也可以实现我们自己的属性，就像我们在`to-do-app`元素中做的那样，我们给了它一个`todos`属性。

这里有一个这样的例子。

```
<img src="myimg.png" alt="my image"/> 
```

Enter fullscreen mode Exit fullscreen mode

浏览器将解析这个`<img>`元素，创建一个 [DOM 元素对象](https://www.w3schools.com/jsref/dom_obj_all.asp)，并方便地为我们设置`src`和`alt`的属性。应该提到的是，这个属性反射对于*所有的*属性都不成立。(例如:一个`<input>`元素上的`value`属性不反映。`<input>`的`value` *属性*将始终是`<input>`的当前文本内容，而`value` *属性*将是初始文本内容。)我们将很快更深入地将属性反映到属性。

所以我们现在知道 alt 和 src *属性*是作为字符串类型处理的，如果我们想把我们的待办事项数组像这样传递给我们的`<to-do-app>`元素:

```
<to-do-app todos="[{...}, {...}]"></to-do-app> 
```

Enter fullscreen mode Exit fullscreen mode

我们不会得到想要的结果；我们期待一个数组，但实际上，这个值只是一个看起来像数组的字符串。

> ✨ *嘿！听着！*
> 
> *   目标是只接受富数据(对象、数组)作为属性。
> *   不要将丰富的数据属性反映到属性中。

*设置*属性的工作方式也不同于属性，注意我们没有实现任何 getters 或 setters。我们将我们的`text`属性添加到`static get observedAttributes` getter 中，以允许我们观察`text`属性的变化。我们实现了`attributesChangedCallback`来应对这些变化。

此时此刻，我们的应用程序应该是这样的:

[![todos](img/d0415d7cd7b2e8547a26e32e60c78a95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T8z07q1B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/j9k2CQk.png)

### 布尔属性

我们还没有完成属性。当我们完成一些待办事项时，能够检查它们是很好的，我们也将为此使用属性。不过，我们必须以稍微不同的方式处理布尔属性。

> 元素上布尔属性的存在代表`True`值，属性的不存在代表`False`值。
> 
> 如果该属性存在，其值必须是空字符串，或者是与该属性的规范名称匹配的 ASCII 不区分大小写的值，并且没有前导空格或尾随空格。
> 
> 布尔属性不允许值“true”和“false”。要表示一个假值，必须完全省略该属性。`<div hidden="true">`不正确。

这意味着只有以下示例可接受真值:

```
<div hidden></div>
<div hidden=""></div>
<div hidden="hidden"></div> 
```

Enter fullscreen mode Exit fullscreen mode

还有一个为假:

```
<div></div> 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们为我们的`<to-do-item>`元素实现`checked`属性！

将您的`to-do-app.js`更改为:

```
_renderTodoList() {
    this.$todoList.innerHTML = '';

    this._todos.forEach((todo, index) => {
        let $todoItem = document.createElement('to-do-item');
        $todoItem.setAttribute('text', todo.text);

    // if our to-do is checked, set the attribute, else; omit it.
        if(todo.checked) {
            $todoItem.setAttribute('checked', '');                
        }

        this.$todoList.appendChild($todoItem);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`to-do-item`改为:

```
 class TodoItem extends HTMLElement {

    ...

    static get observedAttributes() {
        return ['text', 'checked'];
    }

    attributeChangedCallback(name, oldValue, newValue) {
        switch(name){
            case 'text':
                this._text = newValue;
                break;
            case 'checked':
                this._checked = this.hasAttribute('checked');
                break;
        }
    }

    ...

} 
```

Enter fullscreen mode Exit fullscreen mode

不错！我们的应用程序应该如下所示:

[![checked](img/a58c1f735fcbd77cf6f45bba418bde0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QuElsVhd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/SO3rCJU.png)

## ♺把属性反映到属性上

> *   [x] Be a demo
> *   [x] Boring stuff
> *   [x] Setting attributes
> *   [x] Setting attributes
> *   Map attributes to attributes.
> *   [Event
> *   [Summary]

太棒了，我们的应用进展顺利。但是如果我们的最终用户能够查询我们的`to-do-item`组件的`checked`的状态，那就更好了。我们目前只将它设置为一个*属性*，但是我们希望它也可以作为一个*属性*使用。这被称为*，将属性反映到属性*。

我们所要做的就是添加一些 getters 和 setters。将以下内容添加到您的`to-do-item.js` :

```
get checked() {
    return this.hasAttribute('checked');
}

set checked(val) {
    if (val) {
        this.setAttribute('checked', '');
    } else {
        this.removeAttribute('checked');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每次我们改变属性时，值总是同步的。

## 🎉事件

> *   [x] Make a demo
> *   [x] Boring stuff
> *   [x] Setting attributes
> *   [x] Setting attributes
> *   [x] Mapping attributes to attributes
> *   [event]
> *   [Summary]

唷，现在我们已经完成了困难的部分，是时候开始有趣的部分了。我们的应用程序目前以我们想要的方式处理和公开数据，但它实际上还没有删除或切换待办事项。我们来处理一下。

首先，我们必须跟踪我们的`to-do-item`的`index`，让我们设置一个属性！

`to-do-item.js` :

```
static get observedAttributes() {
    return ['text', 'checked', 'index'];
}

attributeChangedCallback(name, oldValue, newValue) {
    switch(name){
        case 'text':
            this._text = newValue;
            break;
        case 'checked':
            this._checked = this.hasAttribute('checked');
            break;
        case 'index':
            this._index = parseInt(newValue);
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们是如何将字符串类型值解析为整数的，因为属性只允许字符串类型，但是我们希望最终用户能够将 index *属性*作为整数。我们现在也有了一个很好的例子，如何处理字符串/数字/布尔属性，以及如何将属性和特性作为它们的实际类型来处理。

所以让我们给`to-do-item.js` :
添加一些 getters 和 setters

```
set index(val) {
    this.setAttribute('index', val);
}

get index() {
    return this._index;
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`to-do-app.js`中的`_renderTodoList`函数改为:

```
_renderTodoList() {
    this.$todoList.innerHTML = '';

    this._todos.forEach((todo, index) => {
        let $todoItem = document.createElement('to-do-item');
        $todoItem.setAttribute('text', todo.text);

        if(todo.checked) {
            $todoItem.setAttribute('checked', '');                
    }

        $todoItem.setAttribute('index', index);

        $todoItem.addEventListener('onRemove', this._removeTodo.bind(this));

        this.$todoList.appendChild($todoItem);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何设置`$todoItem.setAttribute('index', index);`的。我们现在有一些状态来跟踪待办事项的索引。我们还设置了一个事件监听器来监听`to-do-item`元素上的`onRemove`事件。

接下来，当我们单击 remove 按钮时，我们必须让*触发*事件。将`to-do-item.js`的`constructor`修改为:

```
constructor() {
    super();
    this._shadowRoot = this.attachShadow({ 'mode': 'open' });
    this._shadowRoot.appendChild(template.content.cloneNode(true));

    this.$item = this._shadowRoot.querySelector('.item');
    this.$removeButton = this._shadowRoot.querySelector('button');
    this.$text = this._shadowRoot.querySelector('label');
    this.$checkbox = this._shadowRoot.querySelector('input');

    this.$removeButton.addEventListener('click', (e) => {
        this.dispatchEvent(new CustomEvent('onRemove', { detail: this.index }));
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

> ✨ *嘿！听着！*
> 
> 我们可以设置`{ detail: this.index, composed: true, bubbles: true }`让事件从组件阴影 DOM 中冒泡出来。

并在`to-do-app.js` :
中增加`_removeTodo`功能

```
_removeTodo(e) {
    this._todos.splice(e.detail, 1);
    this._renderTodoList();
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。我们可以删除待办事项:

[![remove](img/cdd0cf92bb23616b9e7dda516d7d50b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lo7KYh8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uudJaJH.png)

最后，让我们也创建一个切换功能。

`to-do-app.js` :

```
class TodoApp extends HTMLElement {
    ...

    _toggleTodo(e) {
        const todo = this._todos[e.detail];
        this._todos[e.detail] = Object.assign({}, todo, {
            checked: !todo.checked
        });
        this._renderTodoList();
    }

    _renderTodoList() {
        this.$todoList.innerHTML = '';

        this._todos.forEach((todo, index) => {
            let $todoItem = document.createElement('to-do-item');
            $todoItem.setAttribute('text', todo.text);

            if(todo.checked) {
                $todoItem.setAttribute('checked', '');                
            }

            $todoItem.setAttribute('index', index);
            $todoItem.addEventListener('onRemove', this._removeTodo.bind(this));
            $todoItem.addEventListener('onToggle', this._toggleTodo.bind(this));

            this.$todoList.appendChild($todoItem);
        });
    }

    ...

} 
```

Enter fullscreen mode Exit fullscreen mode

和`to-do-item.js` :

```
class TodoItem extends HTMLElement {

    ...

    constructor() {
        super();
        this._shadowRoot = this.attachShadow({ 'mode': 'open' });
        this._shadowRoot.appendChild(template.content.cloneNode(true));

        this.$item = this._shadowRoot.querySelector('.item');
        this.$removeButton = this._shadowRoot.querySelector('button');
        this.$text = this._shadowRoot.querySelector('label');
        this.$checkbox = this._shadowRoot.querySelector('input');

        this.$removeButton.addEventListener('click', (e) => {
            this.dispatchEvent(new CustomEvent('onRemove', { detail: this.index }));
        });

        this.$checkbox.addEventListener('click', (e) => {
            this.dispatchEvent(new CustomEvent('onToggle', { detail: this.index }));
        });
    }

    ...

} 
```

Enter fullscreen mode Exit fullscreen mode

[![toggle](img/8cff753a6852ebecdaf876b8b530f197.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PtyPbL39--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mE9dcX8.png)

成功！我们可以创建、删除和切换待办事项！

## 👻浏览器支持和聚合填充

> *   [x] Make a demo
> *   [x] Boring stuff
> *   [x] Setting attributes
> *   [x] Setting attributes
> *   [x] Mapping attributes to attributes
> *   [x] Events

在这篇博文中，我想说的最后一件事是浏览器支持。在撰写本文时，[微软 Edge 团队最近宣布](https://twitter.com/MSEdgeUpdates/status/1049404076499320835)他们将实现[定制元素](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/customelements/)以及[影子 DOM](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/shadowdom/) ，这意味着**所有**主流浏览器将很快支持 web 组件。

在那之前，你可以使用由谷歌维护的[webcomponentsjs](https://github.com/webcomponents/webcomponentsjs)poly fills。只需导入聚合填充:

```
<script src="https://unpkg.com/@webcomponents/webcomponentsjs@2.0.0/webcomponents-bundle.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我使用了 unpkg，但是也可以用`NPM`安装 webcomponentsjs。为了确保 polyfills 已经成功加载，我们可以等待触发`WebComponentsReady`事件，就像这样:

```
window.addEventListener('WebComponentsReady', function() {
    console.log('Web components ready!');
    // your web components here
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 💫包扎

> *   [x]制作一个演示
> *   [x]无聊的东西
> *   [x]设置属性
> *   [x]设置属性
> *   [x]将属性反映到属性
> *   [x]事件
> *   [x]包起来
> 
> 搞定了。

如果你一路走到这里，恭喜你！您已经了解了 web 组件规范、(亮/开/闭)阴影 DOM、模板、属性和属性之间的区别，以及将属性映射到属性。

但是正如你可能知道的，我们写的很多代码可能感觉有点笨拙，我们已经写了相当多的 boiler plate(getter、setters、queryselectors 等)，并且很多事情已经被强制性地处理了。我们对待办事项列表的更新也不是很高效。

Web 组件很整洁，但是我不想把所有的时间都花在编写模板和设置命令性的东西上，我想写声明性的代码！“你哭了。

输入 [lit-html](https://polymer.github.io/lit-html/) ，我们将在下一篇博文中介绍。