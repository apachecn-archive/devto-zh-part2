# :空和:空白

> 原文：<https://dev.to/zellwk/empty-and-blank-2lj5>

一个月前我在推特上发布关于`:empty`和`:blank`的消息时犯了一个严重的错误。我说`:empty`没用，`:blank`比`:empty`有用多了。

[![Blank is not supported by any browser](../Images/68306db052694dac1fcff379991a92c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W-EzNA8V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/empty-and-blank/tweet.png)

我错了！

`:empty`其实已经足够好了。我们甚至不需要`:blank`！

好的，首先，什么是`:empty`什么是`:blank`？

## :空且:空白

`:empty`是伪选择器。它允许您选择空的元素。

```
:empty {
  /* do something */
} 
```

Enter fullscreen mode Exit fullscreen mode

空元素是指其中没有任何内容的元素。它甚至不能有空格。

```
<!-- Example of an empty element -->
<div></div> 
```

Enter fullscreen mode Exit fullscreen mode

空元素可以有注释，只要注释填满了整个元素。

```
<!-- Empty can have comments -->
<div><!-- this is a comment --></div> 
```

Enter fullscreen mode Exit fullscreen mode

`:blank`是`:empty`的加电形式。它让你选择包含空格的元素:

```
<!-- Matched with :blank but not with :empty -->
<div> </div> 
```

Enter fullscreen mode Exit fullscreen mode

## :空:现实世界情况下的空白

如果需要,`:empty`和`:blank`都很有用:

1.  设置空元素的样式
2.  创建空状态

### 样式化一个空元素

假设你有一个`<div>`。只有发生错误时，您才会用内容填充这个`<div>`。

```
<!-- Without errors -->
<div class="error"></div>

<!-- With errors -->
<div class="error">Whoops! Something went wrong!</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里，您需要对`.error` div 进行样式化。如果不使用`:empty`，就需要依赖一个类或者属性。这感觉是多余的。

```
<!-- With errors -->
<div class="error" data-state="error">Whoops! Something went wrong!</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.error {
  display: none;
  background-color: hsl(0, 20%, 50%);
  padding: 0.5em 0.75em;
}

.error[data-state="error"] {
  display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果使用`:empty`，就不需要额外的类或者属性了。可以直接对`.error`进行样式化。你甚至不需要`display: none;`！

```
.error {
  background-color: hsl(0, 20%, 50%);
  padding: 0.5em 0.75em;
}

.error:empty {
  padding: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是给你玩的笔。(另外，试着从`.error:empty`上取下`padding: 0;`。你会看到红色的背景😉).

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的 Pen [空 demo](https://codepen.io/zellwk/pen/JaPgdN/) 。

### 创建空状态

假设您想要创建一个待办事项列表。当你的用户第一次看到待办事项列表时，他们可能会看到零个待办事项。

当 todos 为零时，显示什么？

这种零 todo 状态就是我们所说的空状态。

如果您想为您的 todo-list 创建一个空状态，您可以在您的`<ul>`之后添加一个额外的`<div>`。当你这样做的时候，你可以结合使用`:empty`和`+`(相邻兄弟)或者`~`(后续兄弟)选择器来设置空状态的样式。

```
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>
<div class="empty-state"></div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.empty-state {
  display: none;
}

ul:empty + .empty-state {
  display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

我从海登·皮克林那里学会了如何这样使用`:empty`。如果你想看看 todo-list 的例子，请查阅 [Heydon 的文章](https://inclusive-components.design/a-todo-list/)关于[包容性组件](https://inclusive-components.design)。

注意:空状态很重要。如果你需要一些令人信服的东西，可以看看关于 vision 的这篇文章。

## 为什么是:够空？

`:empty`在实践中往往就足够了。我觉得`:empty`不够好，有两个原因:

1.  开发者体验差
2.  我需要用 JavaScript 手动修剪空白

第一个原因是有道理的，但没什么大不了的。

**第二个理由不成立**。我以为我必须修剪空白，但我不需要。

我会带你看完这两个。

### 开发者体验差

让我们回到 todo-list 的例子。假设我们创建了一个 todo-list，我们有这个标记。

```
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>
<div class="empty-state"></div> 
```

Enter fullscreen mode Exit fullscreen mode

你如何检查`:empty`是否在工作？

嗯，我会用`cmd` + `x`去掉每个`<li>`。该命令会剪切整行。当我删除所有三个`<li>`时，我将以这个标记结束:

```
<ul>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在，你应该知道上面的 HTML 不会触发`:empty`。`:empty`仅在元素中没有空白时有效。

我不得不删除空白来让`:empty`工作，这意味着更多的击键。这是一个我希望我不必经历的苦差事。

不过话说回来，这是为了大利益的小问题。

### 我以为我需要用 JavaScript 手动修剪空白

我再说一遍。**如果使用`:empty`，就不需要在 JavaScript** 中手动修剪空白。我做了一个错误的假设。

让我们看一个例子，你就会明白我的意思了。我们将再次使用 todo-list 示例。

假设我们现在有这个 HTML。

```
<ul>
  <li>Item 1</li>
</ul>
<div class="empty-state"></div> 
```

Enter fullscreen mode Exit fullscreen mode

为了使空状态生效，我们需要从`<ul>`中移除最后一个`<li>`项。如果你使用普通的 JavaScript，你可以用`removeChild`来实现。

```
const ul = document.querySelector('ul')
const li = ul.children[0]

ul.removeChild(li) 
```

Enter fullscreen mode Exit fullscreen mode

我(错误地)认为`removeChild`会产生这个 HTML:

```
<ul>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

如果它生成这个 HTML，我将不得不删除列表中剩余的空格。(这是额外的 JavaScript)。

```
const ul = document.querySelector('ul')
const li = ul.children[0]

ul.removeChild(li)

if (ul.children.length === 0) {
  ul.innerHTML = ''
} 
```

Enter fullscreen mode Exit fullscreen mode

就像我说的，我错了。它没有生成上面的 HTML。相反，这是它产生的结果。

```
<ul></ul> 
```

Enter fullscreen mode Exit fullscreen mode

(这意味着我们不需要额外的 JavaScript 来修剪空白！).

声明:我检查了 Safari、Chrome 和 Firefox 上的输出。不过，我还没有检查 Edge。如果你能帮我测试一下，我会非常感激的！

这个例子的代码如下:

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的带 todolist 的 Pen [空 demo。](https://codepen.io/zellwk/pen/ZMzgJp/)

## 支持:空和:空白

`:empty`所有浏览器都支持，`:blank`浏览器支持差。这让你今天有足够的理由使用`:empty`而不是`:blank`！

[![Empty supported by every browser](../Images/bf8c028f147e1612898df072c6089780.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--apfvJqay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/empty-and-blank/empty-browser-support.png) 

<figure>

<figcaption>:空浏览器支持</figcaption>

</figure>

[![Blank is not supported by any browser](../Images/f025785290ceccfe3b32a87ffe944955.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VMRxWmd3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/empty-and-blank/blank-browser-support.png) 

<figure>

<figcaption>:空白浏览器支持(来自 CSS 招数)</figcaption>

</figure>

我希望有一天浏览器对`:blank`的支持会有所改善。

## 包装完毕

`:empty`和`:blank`让您可以轻松地样式化空元素和产生空状态。

`:blank`比`:empty`更好，因为它为我们提供了更好的开发者体验。但是我们不能用`:blank`，因为`:blank`没有足够的浏览器支持，

`:empty`往往就足够好了。你已经可以用它了。用尽所有想要的😉！

试一试，让我知道你的想法！

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。