# 函数式编程基础第 2 部分:高阶函数

> 原文：<https://dev.to/ysael/functional-programming-basics-part-2-higher-order-function-2ckl>

### 那么最高阶的函数是什么呢？

为了被称为`Higher order function`，一个人必须要么接受另一个函数作为参数，要么返回一个函数。

拿函数当参数？？？什么？不会吧！！

让我们从编写一个简单的不是来自最高阶的函数开始:

```
const addYtoX = (x, y) => x + y 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们尝试将这个函数传递给另一个函数:

```
const ofTheHighestOrder = (f, x, y) =>  f(x, y)

console.log (ofTheHighestOrder(addYtoX, 1, 2)) // 3 
```

Enter fullscreen mode Exit fullscreen mode

哇，成功了！！如你所见，我们可以将函数传递给其他函数，并像使用任何其他属性一样使用它们！

让我们重新命名我们的殿下，使其更具宣示性:

```
 const performOperation = (operation, x, y) =>  operation(x, y)

    console.log (performOperation(addYtoX, 1, 2)) // 3 
```

Enter fullscreen mode Exit fullscreen mode

有道理吗？我希望你能在这一点上看到高阶函数的价值和潜力？

让我们看另一个例子:

```
 const addYtoX = (x, y) => x + y
    const multiplyYbyX = (x, y) => x * y
    const performOperation = (operation, x, y) =>  operation(x, y)

    console.log (performOperation(addYtoX, 1, 2)) // 3
    console.log (performOperation(multiplyYbyX, 1, 2)) // 2 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。！我们还可以做更多的事情，但是现在我们就此打住，看看高阶函数的第二种变化；返回一个函数的函数...嗯，这听起来很奇怪，让我们写一个，看看这是否可行！

```
 const ofTheHighestOrder = () => {
        return poke = () => '... your highness?? '
    }

    console.log(ofTheHighestOrder()) // function poke() 
```

Enter fullscreen mode Exit fullscreen mode

酷！正如你所看到的，我们能够从一个函数返回一个函数！！！让我们再和这个家伙玩一会儿:

```
 const ofTheHighestOrder = () => {
        return poke = () => '... your highness?? '
    }

    const willPoke = ofTheHighestOrder()

    console.log(willPoke()) // ... your highness?? 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们可以使用返回一个函数的函数来创建其他函数，这也使它们成为最高阶的函数。

现在看来，这似乎毫无用处，但这为更多的可能性打开了大门，我们将在以后的剧集中探索这些可能性。

在那之前，保持高度！