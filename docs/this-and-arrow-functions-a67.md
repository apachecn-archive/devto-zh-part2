# “这”和箭头功能

> 原文：<https://dev.to/wangonya/this-and-arrow-functions-a67>

箭头函数是在 ES6 中引入的，作为编写 Javascript 函数的新语法。由于它们的短语法，它们鼓励使用小函数，这使得代码看起来更干净(并且`() =>`看起来更酷)😄).

作为一个刚刚开始理解 ES6 语法的初学者，我开始到处使用箭头函数**，却没有真正理解它们是如何工作的。正如您所料，我最终遇到了一些问题，尤其是关于`this`关键字。**

 **`this`有时可能会有点混乱，因为它的值根据函数的执行上下文和模式(严格或非严格)而变化。关于这个问题的来龙去脉已经写了很多，所以我只关注一件事:

#### `this`如何在箭头函数中工作

在一个常规函数中，`this`指的是当对象被定义为一个对象的方法时。为此我们可以做:

```
const brunch = {
    food: 'Dim sum',
    beverage: 'Jasmine tea',
    order: function() {
        return `I'll have the ${this.food} with ${this.beverage} please.`
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

调用`brunch.order()`将返回`"I'll have the Dim sum with Jasmine tea please."`

让我们编辑它，并为`order:` :
使用一个箭头函数

```
const brunch = {
    food: 'Dim sum',
    beverage: 'Jasmine tea',
    order: () => {
        return `I'll have the ${this.food} with ${this.beverage} please.`
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这次调用`brunch.order()`返回`"I'll have the undefined with undefined please."``this.food`和`this.beverage`都返回`undefined`。

它与正常功能一起工作，那么发生了什么事？在正常函数中，`this`是我们的`order`对象。当使用一个箭头函数时，`this`没有绑定到任何东西，它只是从父作用域继承而来，在这个例子中是窗口。在 arrow 函数中的`return`之前添加一个`console.log(this)`会返回一个`Window`对象，所以它会寻找`Window.food`和`Window.beverage`，这两个对象显然都是`undefined`。

因此，箭头函数不适合作为对象方法。

另一个常见的问题是事件处理程序。这里有一个例子:

```
// change button colour on click
<style>
.on {background: red;}
</style>

<button id="click">Toggle</button>

<script>
const button = document.querySelector('#click');
button.addEventListener('click', function() {
    console.log(this); // button
    this.classList.toggle('on');
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`this`指的是按钮。点击按钮，如预期的那样切换颜色。将功能改为箭头功能:

```
// change button colour on click
<style>
.on {background: red;}
</style>

<button id="click">Toggle</button>

<script>
const button = document.querySelector('#click');
button.addEventListener('click', () => {
    console.log(this); // Window { ... }
    this.classList.toggle('on');
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

而`this`成为浏览器的`window`属性。点击该按钮将给出一个`TypeError`错误。如果你依赖于事件处理程序中的`this`，一个常规函数可能是必要的。

因此，尽管箭头函数可能很酷也很流行，但是最好理解它们是如何工作的，并且知道什么时候该用什么时候不该用。**