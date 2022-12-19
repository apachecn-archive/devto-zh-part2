# 普通 JS 代码片段

> 原文：<https://dev.to/learosema/snippets-for-vanilla-js-coding-19cg>

在用 VanillaJS 编码的时候，我一般会为`document.querySelector`和`document.querySelectorAll`创建快捷方式。我也喜欢把`D`说成`document` :
的捷径

```
const D = document
const $ = D.querySelector.bind(D)
const $$ = (selector, startNode = D) => [...startNode.querySelectorAll(selector)] 
```

Enter fullscreen mode Exit fullscreen mode

当你在 Devtools 中打开 JS 控制台时，知道`$`和`$$`函数已经内置，也就是[命令行 API](https://developers.google.com/web/tools/chrome-devtools/console/command-line-reference) 也是很好的。

对于美元函数，您已经可以使用类似于 jQuery:
语法

```
<button id="button">click me!</button> 
```

Enter fullscreen mode Exit fullscreen mode

```
$('#button').onclick = () => {
  alert('You clicked me!')
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想玩多个元素，那么`document.querySelectorAll`的快捷方式`$$`开始起作用。

```
<button> button 1 </button>
<button> button 2 </button>
<button> button 3 </button> 
```

Enter fullscreen mode Exit fullscreen mode

```
$$('button').map(btn => {
  btn.style.backgroundColor = 'red'
}) 
```

Enter fullscreen mode Exit fullscreen mode

当涉及到事件处理时，拥有一个`on`方法会很有用:

```
Array.prototype.on = function(type, listener, options) {
  this.map(el => {
    if (el instanceof Element) {
      el.addEventListener(type, listener, options)
    }
  })
  return this // for chaining
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，你可以一次在多个元素上注册多个事件处理程序:

```
$$('button').on('click', e => {
  const btn = e.target
  alert('You clicked ' + btn.textContent)
}).on('mouseover', e => {
  const btn = e.target
  btn.style.backgroundColor = 'red'
}).on('mouseout', e => {
  const btn = e.target
  btn.style.backgroundColor = 'blue'
}) 
```

Enter fullscreen mode Exit fullscreen mode