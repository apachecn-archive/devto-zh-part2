# JavaScript 的 indexOf()的一个巧妙小技巧

> 原文：<https://dev.to/werninator/a-neat-little-trick-with-javascripts-indexof-4dj5>

这是我在浏览一些插件时学到的小技巧，我不得不再次编辑或扩展这些插件

```
var arr = [ 1, 2, 3, 'foo' ];

// old way
if (arr.indexOf('foo') > -1) {
    console.log('"foo" is in "arr"!');
}

// new way
if (~arr.indexOf('foo')) {
    console.log('"foo" is in "arr"!');
} 
```

Enter fullscreen mode Exit fullscreen mode

我想知道- *这是如何工作的*？这是因为这个我从未使用过或见过的小操作符:

### 按位非运算符`~`

它翻转一个数字的所有位，我不确定它是如何工作的，但似乎`-1`是唯一一个以虚假数字结束的情况，或者更确切地说是一个虚假表达式:`0`

这意味着，我们可以利用它并在 indexOf 中使用它(虽然还没有看到任何其他用途)。

```
// you can also negate the statement, no separate parantheses needed
if (!~dailyRoutine.indexOf('☕️')) {
    console.log('Not a life worth living');
} 
```

Enter fullscreen mode Exit fullscreen mode

对此你怎么看？我认为它看起来很整洁，节省了代码中的一些字符，但它可能会让后来阅读它的人感到困惑(或者像我一样查找它，lol)