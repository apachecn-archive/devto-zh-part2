# 新手异步 JavaScript

> 原文：<https://dev.to/adroitcoder/asynchronous-javascript-for-newbies-k61>

当编写 JavaScript 时，我们经常处理异步代码，这对于新手来说可能会很困惑。在我们进入异步 JavaScript 之前，让我们先谈谈同步代码。

## 什么是同步码？

同步代码在单线程上运行。1 个动作完成后再进行下一个动作。

```
 thread -
          |
          V    
          Start|Line1<---------A--------->|Finish
          Start|Line2<---------B--------->|Finish
          Start|Line3<---------C--------->|Finish 
```

Enter fullscreen mode Exit fullscreen mode

## 异步代码

与同步代码不同，异步代码现在运行，稍后完成。
从下图可以看出，fn2 行没有完成，但在执行了 fn6 后完成了。

```
 thread -                                                Separate thread outside you code.
          |                                             /           V                                            /
          Start|fn1<--------A---------->|Finish       /
          Start|fn2<--------B-----------|-------------|
          Start|fn3<--------C---------->|Finish       |
          Start|fn4<--------D---------->|Finish       V 
          Start|fn5<--------E---------->|Finish       | 
          Start|fn6<--------F---------->|Finish       V  
      Callback |fn2<--------B---------->|<------------| 
```

Enter fullscreen mode Exit fullscreen mode

请注意，JavaScript 是单线程的，只对您编写的代码执行，但是从您的代码发出的请求会被交给一个单独的线程。比如说一个 Api 调用。打住，我知道你在想什么；什么是“回调”？别担心，接下来我们将讨论“回拨”。

## 什么是回调？

回调是作为参数传递给另一个函数的函数，然后在 out 函数中调用它来完成某种操作。

```
 function Callback(){
 alert("You called Me");
}

setTimeout(callback,300);

// You called Me 
```

Enter fullscreen mode Exit fullscreen mode

这是一个同步回调，接下来我们将讨论

##### ——异步回调。

##### ——回调地狱。

##### ——Ajax 请求。

##### ——承诺。

##### -发电机。