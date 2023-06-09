# 什么时候代码“太聪明”/如何看待可读性/认知负荷？

> 原文：<https://dev.to/_bigblind/when-is-code-too-clever--how-do-you-think-about-readabilitycognitive-load-nh1>

通常，我很清楚自己写的代码是否干净易懂，但是最近，我发现自己在 JavaScript 中做了很多如下的事情:

当一个函数只有在传入某个选项的情况下才需要做一些事情的时候，我是这样做的:

```
function foo(shouldBar){
    //...

    shouldBar && bar();
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现这很清楚，但我可能会尝试戒掉这个习惯，因为

```
if(shouldBar){
    bar();
} 
```

Enter fullscreen mode Exit fullscreen mode

*   更加明确，
*   更容易理解，或者 JavaScript 经验较少的开发人员。

如何判断某一行代码是否“太聪明”？