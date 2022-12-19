# 模仿 Jasmine 中的命名空间函数构造函数

> 原文：<https://dev.to/dechamp/mocking-a-namespaced-function-constructor-in-jasmine-dcb>

## TL；速度三角形定位法(dead reckoning)

去底部看看它是如何做到的代码。

## [T1】简介](#intro)

因此，正如我在我的另一篇帖子[中提到的，什么是陈述性与命令性](https://dev.to/dechamp/what-is-declarative-vs-imperative-3fjf)，我将努力为我必须研究的任何时候创建一个帖子。

我已经有一年没有摆弄 Jasmine 了，但是现在回到了一个使用它的项目上。我自己更喜欢开玩笑，但有时你必须在项目使用的东西上工作。

我最头疼的是试图模仿我的一个 IFFE [立即调用的函数表达式](https://stackoverflow.com/questions/8228281/what-is-the-function-construct-in-javascript)，它有一个名称空间。

我选择遵循项目中实践的模式。我喜欢 IFFE 的，但有时他们不适合使用。这是另一天的讨论。

不管是谁，我都很难记起如何嘲笑这个坏男孩，幸运的是，我的好朋友马克·麦地那来帮忙了。

好了，这是帮助你理解我所说的设置。

## 设置

在工作中，我们倾向于保持我们的代码命名空间，使用 IFFE 是一种简单的方法来锁定它，将逻辑封装在命名空间内，同时阻止对代码的任何外部访问，除非故意暴露。

对于这个例子，我们的命名空间将是“dummy ”,我们的函数构造函数将被称为“CoolFunction ”,并带有一个“mountId”参数。

**注意:**这是针对 ecmascript 5 做的项目约束，但是你可以自由地将它背后的思想应用到你正在编写的任何版本中。

```
;(function(window) {
    // leaving off var/let/const 
    // (depending on version of javascript you're writing for) will make it global.
    dummy = dummy || {};
    dummy.CoolFunction = CoolFunction;

    function CoolFunction(mountId) {
        function mount: ...
        function unmount: ...
    }    

})(window); 
```

Enter fullscreen mode Exit fullscreen mode

作为一名优秀的程序员，我正在测试我所有的代码。这包括我的初始化脚本，它调用我的“CoolFunction”。这就是嘲讽发挥作用的地方。

下面是我们如何模拟上面的代码。

## 解

```
var coolFunctionSpy, mountSpy, unmountSpy;

beforeEach(function() {
    // setup your method spies first
    mountSpy = jasmine.createSpy('mount');
    unmountSpy = jasmine.createSpy('unmount');    

    // now create a spy on the main function constructor and return the object of values
    coolFunctionSpy = jasmine.createSpy('coolFunction').and.returnValue({
        mount: mountSpy,
        unmount: unmountSpy
    });

    // now assign that to your object, that you are using 
    // to overwrite the main namespace. Make sure you do not add `var` 
    // as we want to overwrite the global.
    wpt = {
        CoolFunction: coolFunctionSpy
    }
}

describe("our tests", function() {
    it("allows us to test the spied methods", function () {
        // Calls `new dummy.CoolFunction('theId')` with in.
        sut.init(); 

        // passes, because it is called in sut.init()
        expect(mountSpy).toHaveBeenCalled();         

        // passes, because it is not called in sut.init()
        expect(unmountSpy).not.toHaveBeenCalled(); 
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的重要一点是，不要在命名空间赋值前添加 var。你想要覆盖全局，如果你给它加上`var`，它将不起作用。

我希望这能帮助其他人。

## 反馈

我说错了吗？有建议？需要澄清吗？随意评论。

谢谢大家！

-说吧

* * *

瓦里马德有限责任公司。

目前的项目有[https://charactergenerator4000.com](https://charactergenerator4000.com)和 [https://coder.exchange](https://coder.exchange) 。请检查它们，并让我们知道你的想法。