# 颠倒你的假设！

> 原文：<https://dev.to/codevault/invert-your-ifs-2m0g>

你们都已经看过字面上的**和堆积如山的代码**，这里更新一下:

```
if (candies.length > 0) {
    if (store.isOpen()) {
        if (store.allCandyIsLow()) {
            for (let candy of candies) {
                store.refill(candy);
            }
            notification('Store ' + store.Name + ' had its candies refilled.');
        } else {
            console.error('There is still candy in the store');
        }
    } else {
        console.error('No stores to put the candy into');
    }
} else {
    console.error('No candies');
} 
```

Enter fullscreen mode Exit fullscreen mode

这变得很难管理，因为

*   代码块的主要用途是嵌套在所有 if 语句中，而
*   例外/错误案例就在你面前
*   它们的条件和误差尽可能彼此远离

这损害了代码的**可读性**、**灵活性**和**可维护性**(添加或更改条件的顺序变得困难)。

这里有一个实用的解决方案，可以在开发过程中的任何时候应用，只需执行这些简单的步骤，直到不再有任何嵌套主代码的异常情况:

*   从 if 语句中取出代码
*   颠倒状况
*   将代码放入 if 块的 else 中
*   添加一条 return 语句(或大块语句之后的任何语句)

上面的代码看起来应该是这样的:

```
if (candies.length <= 0) { // or === 0 in this case
    console.error('No candies');
    return;
}
if (!store.isOpen()) {
    console.error('No stores to put the candy into');
    return;
}
if (!store.allCandyIsLow()) {
    console.error('There is still candy in the store');
    return;
}

for (let candy of candies) {
    store.refill(candy);
}
notification('Store ' + store.Name + ' had its candies refilled.'); 
```

Enter fullscreen mode Exit fullscreen mode

尽管代码块本身变大了，但是

*   不再有任何嵌套的 if 语句
*   主代码紧跟在所有异常情况之后
*   您可以看到每个异常情况做了什么，并根据需要进行添加/修改

你怎么想呢?有没有更好的解决办法？这一个有我们遗漏的一些关键缺陷吗？问吧！