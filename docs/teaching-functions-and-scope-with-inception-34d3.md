# 教学功能和范围

> 原文：<https://dev.to/tttaaannnggg/teaching-functions-and-scope-with-inception-34d3>

这个会有点乱。它仍然非常原始，我对它感到兴奋/充满活力，但不完全确定如何构建它，但这里有:

我参加了一个关于递归的 Codesmith 研讨会。我并不真正理解它是如何传递嵌套位之外的值的，但最终发现 return 语句不断地将一个结果提升一个级别，只是由它之前的嵌套函数中的 return 语句返回。

因为我看过《盗梦空间》,我被赋予了一种非常性感、非常令人兴奋的方式去想象它；这就像是高潮的场景，所有“醒来”的经历都完美地同步了。

我想我应该再看一遍《盗梦空间》,看看我还能从中得到什么，我没有失望。

我们可以把架构师/梦想家看作一个函数定义。架构师设置参数并定义行为。主题被传递到梦里，就像一个论点被输入到一个函数里一样。

给梦加关卡/调用函数会怎么样？我们以某种 f(x)为例。

当你调用一个函数时，你创建了一个局部作用域，一个在你现有世界中的新世界(一个梦)。它带有本地执行上下文(事情发生的地方)和本地内存，本地内存存储传递到函数中或在函数中声明的变量和函数的本地定义。当它运行完里面的所有代码后，它就消失了。你醒了。很简单，对吧？

好吧，让我们把事情搞砸一点。记住，我们可以从其他函数内部调用函数，对吗？高阶函数调用低阶函数。这就是你的梦中情人。我们调用函数，同样的事情再次发生，只是嵌套更深一层。f(g(x))，或者(f∘g)(x)，对于熟悉函数合成的人来说。

第二个局部执行上下文(梦中梦)在第一个局部执行上下文(梦)内部打开，它存在于全局执行上下文(现实)中。从第一级传递到第二级的任何参数现在都存储在第二级嵌套函数的本地内存中。

```
//global scope out here
f(x){
    //local scope 1 / dream 1 here
    some code...

    //and here's where our protagonists whip out the briefcase and stick themselves with the IV to call another function and establish another level to the dream.
    g(x)
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，很明显，进入下一个级别
是微不足道的

```
// "reality" is out here in the global scope
f(x){
    // dream level 1 (the van chase) is here inside of f(x), or the first function
    g(x){
        // dream level 2 (the hotel) is here inside of g(x), or the second nested function
        h(x){
            // and then we have dream level 3, the snowbase.
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

雪基地(梦境 3 级)需要在酒店解决(梦境 2 级)之前解决，酒店需要在货车追逐解决(梦境 1 级)之前解决，然后它会把我们踢出全局执行上下文(现实)。在 Inception 中，它是同时发生的，但是在同步编程中，每个表达式都是按顺序计算的，直到它需要等待下一个表达式被解析。f(g(h(x)))解析 f(x)，直到碰到需要解析 g(x)的部分，以此类推。

最终，我们在 h(x)(梦的第三层)得到的答案是一个能让我们一路向上解析表达式的答案。

让我们做到这一点的是“踢”，我们能够让人们从梦中醒来，而不会丢失他们已经获得的信息。在我们的例子中，这是通过 return 语句完成的。

```
// "reality" is out here in the global scope
f(x){
    // dream level 1 (the van chase) is here inside of f(x), or the first function
    return g(x){
        // dream level 2 (the hotel) is here inside of g(x), or the second nested function
        return h(x){
            // and then we have dream level 3, the snowbase.
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在运行结束时没有从函数中返回任何东西，那么局部作用域就会关闭，存储在局部内存中的所有东西都会被抛出/被垃圾收集。我想我们可以想象这是“地狱边缘”，除了在《盗梦空间》中，你可以很容易地摆脱它。对我们来说不是这样。

这就是我所知道的，老实说，如果你理解《盗梦空间》,你可能会从理解作用域和递归开始，但是 maaaaaaaaaybe 可能这是某种可以作为教学工具的东西。