# JavaScript 中的分号规则很奇怪...

> 原文：<https://dev.to/dean/semicolon-rules-in-javascript-are-weird-6h1>

我想先说一下，我真的很喜欢 JavaScript。它有一些怪癖，尽管其中大多数都是你不会遇到的边缘情况。但是我发现了一个我仍然困惑的问题，我宁愿不去查 JavaScript 规范来解决这个问题...

我原来的功能:

```
function cubicBezier(x, u0, u1, u2, u3) {
    return
        u0 * (1 - x) * (1 - x) * (1 - x) +
        u1 * (1 - x) * (1 - x) * x +
        u2 * (1 - x) * x * x +
        u3 * x * x * x;
} 
```

Enter fullscreen mode Exit fullscreen mode

你们中一些经验丰富的 JavaScript 专家可能会看到这一点，并说“它将总是返回未定义的”，因为`return`被解释为`return;`，因为后面没有其他内容。这让我想知道 JavaScript 中的分号规则。

我最喜欢的编程语言是 Go，它也有可选的分号。Go 中的分号规则非常简单:

> 当输入被分解成标记时，如果该标记为，分号将被自动插入到标记流中，紧跟在一行的最后一个标记之后
> 
> *   一个标识符
> *   整数、浮点、虚拟、符文或字符串文字
> *   关键字 break、continue、fallthrough 或 return 之一
> *   运算符和标点符号之一++、-、)、]或}

顺便说一下，围棋的规则可以这样简单地解释:

> 如果该行以下列字符结尾，则放置一个分号:
> 
> *   字母或数字
> *   结束标点符号(又名`]`、`)`、`}`和结束`"` / `'`)
> *   `++`或`--`

当我看到函数返回 undefined 时，我认为 JavaScript 分号规则就像 Go 一样简单。毕竟，`return`后面什么也没有出现，所以后面加了一个分号。就这么简单，对吗？

## 嗯，我进一步调查了一下。

所以我做了几个给整数加 1 的函数，看看 JavaScript 做了什么。

```
function addOneNormal(x) {
    return x + 1
}

function addOneWeird(x) {
    return x
        +1
}

function addOneUndefined(x) {
    return
        x + 1
} 
```

Enter fullscreen mode Exit fullscreen mode

我们知道`addOneNormal`和`addOneUndefined`最终是什么。`addOneNormal`给`x`加 1，`addOneUndefined`击`return`返回未定义。那么`addOneWeird`是做什么的呢？

*(旁注:在 Go 中，这很简单，由于`return x`以字母结尾，所以放了一个分号。下一行`+1`导致编译错误，因为`+1`没有被赋值给任何东西)*

嗯，有些人会认为它和`return x + 1;`一样，尽管有些人(比如我)认为它是`return x; +1;`，其中`+1`是一个`1`，前面有一个一元加运算符。

## 结果

那么结果如何呢？`addOneWeird(5) => 6`。它成功添加了 1。太奇怪了...不是吗？那条语句看了下一行，即使光秃秃的`return`没有。

不幸的是，这些规则不能更加一致，因为向后兼容是 JavaScript 的一个要求。

无论如何，有人能解释为什么在这种情况下`+`操作符最终被解释为二进制加号而不是一元加号吗？如果`addOneUndefined`函数产生了`undefined`，那么`addOneWeird`被解释为`return x; +1;`似乎更符合逻辑。