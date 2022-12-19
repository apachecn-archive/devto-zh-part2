# 为什么有些语言会打乱一些常见的、习惯性的约定？

> 原文：<https://dev.to/perigk/why-do-some-languages-disrupt-some-common-and-habitual-conventions-2ial>

那几天，我潜入了戈朗。我注意到，这种语法大多数时候是违背“常识”的。

例如，他们是这样声明一个数组和一个函数的:

```
var books [5]string

func printListInts(intz ...int) {
    fmt.Println(intz)
} 
```

Enter fullscreen mode Exit fullscreen mode

大多数语言都是这样做的

```
books = string[5] // The length on the right
function foo(int ...mylist) { // Type on the left
} 
```

Enter fullscreen mode Exit fullscreen mode

我很高兴看到语言逻辑的结构变化，但是为什么他们要做如此“琐碎”和“无意义”的变化呢？