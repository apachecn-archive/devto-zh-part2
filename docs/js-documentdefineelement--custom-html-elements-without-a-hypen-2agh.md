# [JS] document.defineElement |不带分号的自定义 HTML 元素

> 原文：<https://dev.to/jochemstoel/js-documentdefineelement--custom-html-elements-without-a-hypen-2agh>

GitHub 包 [document.defineElement](https://github.com/jochemstoel/document.defineElement) 是 document.registerElement 的 polyfill，它在节点名中不需要 hypen。这使您能够注册 *<事物></事物>* 而不是 *< x 事物> < /x 事物>* 。

当然，它不一定要用作聚合填充物，你可以把它粘在你的电子应用或其他什么东西上。

## 这是什么？

DOM 的 *document.registerElement* 要求在节点名中包含一个连字符以防止冲突。我拒绝接受这个规则，这就是结果。

*   document.defineElement 是 document.registerElement 的聚合填充，已修改为允许没有连字符(-)的元素
*   document.defineElement 甚至可以在没有实现 document.registerElement 的浏览器中工作。
*   已重命名 document.defineElement 以防止与 document.registerElement 冲突。
*   document.defineElement 允许您覆盖所有现有的 DOM 节点行为。
*   document.defineElement 可用于创建动态和交互式 HTML 节点类型。

## document.defineElement()

就像 *document.registerElement* 一样，您的新类支持以下(可选)回调。下面的方法/语法与使用 registerElement 相同，应该非常简单。

```
class HTMLSomeCustomElement extends HTMLElement {
    /* Fires when an instance of the element is created. */
    createdCallback() {
        /* */
    }

    /* Fires when an instance was inserted into the document. */
    attachedCallback() {
        /* */
    }

    /* Fires when an instance was removed from the document. */
    detachedCallback() {
        /* */
    }

    /* Fires when an attribute was added, removed, or updated. */
    attributeChangedCallback(attr, oldVal, newVal) {
        /* */
    }
}

document.defineElement('custom-element', HTMLSomeCustomElement) 
/* now every <custom-element></custom-element> will be an instanceof HTMLSomeCustomElement */ 
```

Enter fullscreen mode Exit fullscreen mode

```
/* or you can do this too */
var Custom = document.defineElement('custom-element', HTMLSomeCustomElement)
document.body.appendChild(new Custom()) 
```

Enter fullscreen mode Exit fullscreen mode

```
/* or simply this works as well */
document.createElement('custom-element') 
```

Enter fullscreen mode Exit fullscreen mode

## 简单时钟，一个实际例子

下面的自定义元素将显示当前时间，并像时钟一样每秒更新一次。在这个例子中，我们没有在节点名中使用连字符。我们需要在我们的自定义界面框架中显示一个时钟的东西就是把

`<clock></clock>`

放在某个地方。

```
class HTMLSimpleClockElement extends HTMLSpanElement {

    createdCallback() {
        var self = this
        /* we could do something with this.getAttribute() for instance set interval */
        this.interval = setInterval(function() {
            self.innerText = new Date(new Date().getTime()).toLocaleTimeString()
        }, 1000)
    }

}

document.registerElement('clock', HTMLSimpleClockElement) 
```

Enter fullscreen mode Exit fullscreen mode

```
<body>
    <clock></clock>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

[![Clock Example GIF image](../Images/dc4f7630225b33f70adda2bd8859739c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WdN7in9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhhv7dvcj3wyiaut9y2c.gif)

> 您可以支持时钟上的一些属性*如刷新间隔或显示字符串模板，然后在创建或附加的回调函数中检查 *this.attributes* 。*

## 思想

我不是很有想象力。抱歉。

*   创建一个`<include></include>`元素来获取远程内容并呈现它。
*   设计一个自动连接到 WebSocket 服务器的`<chat></chat>`元素
*   带有`<user></user>`或`<like-button></like-button>`的东西

## 坏主意

*   完全覆盖`<body>`元素并打破东西
*   使 iframes 或脚本标签停止工作