# Javascript 对象的字面语法我从未见过

> 原文：<https://dev.to/hitochan777/javascript-object-literal-syntax-i-had-never-seen-54bl>

我在做[流星教程](https://www.meteor.com/tutorials/blaze/forms-and-events)的时候遇到了下面这段代码。

```
{
  'submit .new-task'(event) {
    // some code here
  },
} 
```

我感到非常困惑，因为我从未见过一个对象文字包含一些字符串，后跟一个匿名函数。在我阅读了关于对象初始化器的 [MDN web 文档后，我知道上面的代码等价于下面的:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Method_definitions) 

```
{
  'submit .new-task': function(event) {
    // some code here
  },
} 
```

语法对我来说看起来很奇怪，可能是因为我不习惯。