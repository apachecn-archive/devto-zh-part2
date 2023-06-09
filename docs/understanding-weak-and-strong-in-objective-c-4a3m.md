# 理解目标 C 中的弱和强

> 原文：<https://dev.to/onmyway133/understanding-weak-and-strong-in-objective-c-4a3m>

积木很棒。为了避免“保留循环”,你经常会看到软弱的自己像这样跳舞

```
__weak __typeof__(self) weakSelf = self;
    self.block = ^{
        __typeof__(self) strongSelf = weakSelf;
        [strongSelf doSomething];
        [strongSelf doSomethingElse];
    }; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建块时

#### 块被复制时是对象

块是在堆栈上创建的，当它们的堆栈帧返回时，块就会消失。在堆栈上，块对它访问的任何东西的存储或生命周期没有影响。

如果堆栈帧返回后块需要存在，它们可以被复制到堆中，这个操作是一个显式操作。通过这种方式，块将像 Cocoa 中的所有对象一样获得引用计数。当它们被复制时，它们会带走它们捕获的范围，保留它们引用的任何对象

#### 块可以从封闭范围中捕获值

除了包含可执行代码之外，块还具有从其封闭范围捕获状态的能力。注意，block 捕获变量及其装饰符(即弱限定符)，

= >这就解释了为什么你需要把自我声明为 _ _ 软弱

## 当块被执行时

当执行 block 时，weakSelf 对于第一个方法(doSomething)可能为非 nil，但对于第二个方法(doSomethingElse)可能不是

首先，您可能会认为这是一个在块内使用 self 来避免保留周期警告的技巧。事实并非如此。对 self 的强引用是在块执行时创建的，而在块中使用 self 是在块声明时计算的，因此保留了对象。

然而，对于最佳实践，您应该使用弱引用创建对象的强引用。这也不会创建一个保留循环，因为块中的强指针只会在块完成之前存在(它唯一的作用域是块本身)。

= >这解释了为什么你需要声明另一个 _ _ 强大的自我

## 更多解释

这是我对读者尼基塔的回答

1.  正如许多人指出的那样，“块是在堆栈上创建的，当它们的堆栈帧返回时，块就会消失。在堆栈中，块对它所访问的任何内容的存储或生存期都没有影响。即使块(在堆栈上声明)增加了对它访问的所有对象的引用计数，这也是没有用的，因为当函数返回时，这个块将被丢弃

2.  当块被复制时(你看到人们通常为块声明属性(copy))，它将增加对它访问所有对象的引用计数。

为什么？因为块意味着在以后执行，所以它需要保持对它访问的所有对象的强引用。块可以被执行多次，所以它不会在这次运行后释放 self。

当你清空这个块时，它将被 dealloc，因此它将减少对它访问的所有对象的引用计数。

AFNetworking 在被调用后就消失了，所以你不必在块内使用 weak self[http://www.fantageek.com/1376/afnetworking-gotcha-2/](http://www.fantageek.com/1376/afnetworking-gotcha-2/)

1.  所以有些情况下，你不必在块 a 中使用 weakself。确保该块没有被复制，你只需声明并运行它 b。确保该块在被调用后为零

## 引用

*   [我终于明白了软弱的自己和坚强的自己](http://dhoerl.wordpress.com/2013/04/23/i-finally-figured-out-weakself-and-strongself/)
*   [使用模块](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)
*   [目标-C 块警告](http://albertodebortoli.github.io/blog/2013/08/03/objective-c-blocks-caveat/)
*   [物镜-引擎盖下的 C 块](http://albertodebortoli.github.io/blog/2013/04/21/objective-c-blocks-under-the-hood/)
*   看街区内部:第一集
*   看街区内部:第二集
*   看街区内部:第三集
*   [从 C 声明符到 Objective-C 块语法](http://nilsou.com/blog/2013/08/21/objective-c-blocks-syntax/)
*   [Objective-C 程序块的语法](http://arigrant.com/blog/2014/1/18/the-syntax-of-objective-c-blocks)
*   [指嵌套块内的弱自我](http://stackoverflow.com/a/17105368/1418457)
*   [捕捉我(自己)](http://blackpixel.com/blog/2014/03/capturing-myself.html)

* * *

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️