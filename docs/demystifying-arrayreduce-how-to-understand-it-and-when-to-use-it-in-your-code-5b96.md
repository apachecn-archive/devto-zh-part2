# 揭开 Array.reduce()的神秘面纱:如何理解它以及何时在代码中使用它

> 原文：<https://dev.to/nickyhajal/demystifying-arrayreduce-how-to-understand-it-and-when-to-use-it-in-your-code-5b96>

ES5 和更高版本的 JavaScript 带来了几个处理数组的新方法。它们中的许多已经成为使用数组执行常见任务的最佳实践，JavaScript 使用它们会好得多。

`Array.map()`和`Array.filter()`是两个日常驱动的 ES5 增加的例子，它们是大多数 JS 开发者的主食。

工具箱中一个同样有用但通常不太为人所知的工具是`Array.reduce()`。最近，我越来越频繁地使用`reduce`,但是需要一段时间才能感觉到它的自然，我经常看到其他开发人员在努力使用它...或者根本不用它。

## Reduce 常被教错，因而被误解

在我看来，混乱的一大原因是`reduce`只是教错了。事实上，我甚至觉得名为的*是错误的(尽管，当然，reducer 的概念和名称远在它被添加到 JavaScript 之前就有了)。*

教授减速器时最常见的例子是这样的:

```
const array = [1, 2, 3, 4];
const sum = array.reduce((accumulator, currentItem) => {
    return accumulator + currentItem;
}, 0);

// sum = 10 
```

Enter fullscreen mode Exit fullscreen mode

这清楚地显示了减速器是如何工作的。您在一个数组上调用`reduce()`,并向它传递一个初始值以及一个为该数组的每一项运行的函数。

`reduce`向传递的函数发送:

1)当前项目和
2)前一次回调返回的值(或初始状态，当第一次调用时)

`reduce`的最终值是最后一次调用内部 reducer 函数时返回的值。

因此，在这种情况下，reducer 对数组的值求和并返回总值。

## 如此...将数字数组相加，这就是所有的`Reduce`吗？

这个例子我见过很多次，但很少看到更深入的解释。

“嗯，有意思。”我想，随着我一天的继续。

那么，减压器到底是怎么回事？它们到底有什么用？

这才是真正的力量:

## 当你想处理一个数组**但输出不是数组**的东西时，归约器很有用

这就是我们要说的，这也是非常有用的。

毕竟，这不正是我们在上面的例子中所做的吗？我们接收一个数组并返回一个整数。在这种情况下，我们“减少”数组是有意义的，但我们并不总是必须输出像整数这样简单的东西。

举这个例子:

```
const trips = [{type: 'car', dist: 42}, {type: 'foot', dist: 3}, {type:'flight', dist: 212}, {type: 'car', dist: 90}, {type: 'foot', dist: 7}] 

const distanceByType = trip.reduce((out, curr) => {
    const { type, dist } = curr;
    if (out[type]) {
        out[type] += dist;
    } else {
        out[type] = dist;
    }
    return out;
}, {});

// distanceByType = {car: 132, foot: 10, flight: 212}; 
```

Enter fullscreen mode Exit fullscreen mode

所以在这里，我们接受一个数组，但是返回一个 sums 对象，而不仅仅是一个。

在这里，我们开始看到减速器的本质:

## 减速器是变压器

它接受一个数组，然后把它转换成别的东西。

以这种方式思考不仅帮助我理解`reduce`是做什么的，而且*帮助我意识到什么时候我应该使用它*。

*   有一个用户数组，但是想要输出一个字符串，比如，`Mike, Becka and 3 others will be attending`？一个减速器是伟大的！

*   有一组文章，想要一个按类别排序的对象？一个减速器是伟大的！

用例还在继续。

# 往前走`Array.reduce()`！

我真的希望这能有所帮助，如果可以的话，我很乐意在评论中进一步澄清。

有您喜欢的`reduce`用例吗？请在下面评论，我会把它添加到文章中🚀