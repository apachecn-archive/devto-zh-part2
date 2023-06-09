# 函数式编程基础第 1 部分:纯函数

> 原文：<https://dev.to/ysael/functional-programming-basics-part-1-pure-function-e55>

### 什么是纯度？

函数的纯度被定义为对于相同的输入总是返回相同的输出，并且没有副作用。

那么，对于同样的输入，同样的输出意味着什么呢？

良好的...让我们看一些例子...

假设我们有这样的代码:

```
let y = 2
const addYtoX = (x) => x + y 
```

Enter fullscreen mode Exit fullscreen mode

这个函数不被认为是纯函数，因为输出可能会被`y`的值修改，这意味着我们不能保证它会为相同的输入返回相同的输出。

```
let y = 2;
const addYtoX = (x) => x + y

console.log(addYtoX(3)) // 5

y = 1 
console.log(addYtoX(3)) // 4 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`y`的值会影响函数的输出，即使我们使用相同的输入来调用它...这绝对是不纯的。

让我们看看同一个函数的纯版本:

```
 const addYtoX = (x, y) => x + y 
```

Enter fullscreen mode Exit fullscreen mode

这就对了。！！

我可以任意多次调用这个家伙，并且我可以确保相同的输入得到相同的输出。

那很酷，但是副作用呢？

良好的...让我们看看相同的功能，但增加了副作用！

```
 const addYtoX = (x, y) => {
    makeSomeNoodles()
    return x + y
}; 
```

Enter fullscreen mode Exit fullscreen mode

我希望你能猜到副作用是由`makeSomeNoodles`函数调用引起的对吗？

这有什么不好？良好的...这个函数应该把 x 和 y 相加，现在它在计算我的值之前做面条？我不知道在那个仪式上发生了什么....也许它实际上在我的简单函数可以执行之前就崩溃了，或者更糟！！！

无论如何，我希望你明白基本的意思。:)

直到下次...保持纯洁！