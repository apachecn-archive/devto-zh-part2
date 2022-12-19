# 停止返回 Null！

> 原文：<https://dev.to/lysofdev/stop-returning-null-2ho2>

我们都经历过。不知疲倦的几个小时的测试和精心的开发被一个神秘的`NullPointerException`或类似的问题中止了，这一切都是因为一些未定义的值。我们该怎么办？

谁想出了`null`、`None`、`nil`等。真的讨厌人类。我明白了。我们需要一个代表不存在的值，但是没有更好的方法吗？让我们试着跳过`null`共意志列表。我将展示 Javascript 中的一些代码示例，因为这里的大多数人都理解它，这是一种特别愚蠢的语言，因为它以某种方式证明了拥有两个不存在的值`null`和`undefined`。啊，对，`undefined`。薛定谔的网猫。

我们在野外可能会遇到以下情况:

```
let counter;

...some code that may or may not define counter...

if ( counter !== undefined && counter !== null ) {
    counter++;
} 
```

Enter fullscreen mode Exit fullscreen mode

什么乱七八糟的，阿米利特？有些人甚至会说我们应该使用`typeof`来格外小心，但是我们可以用列表来解决这个问题。这里有一个基本的尝试。

```
const counters = [];

...some code that may or may not define a counter inside counters...

if ( counters.length >== 1 ) {
    counters[0]++;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的`if`语句稍微简单了一点，但是在实际执行操作之前，我们仍然需要检查。如果我们可以只执行操作而不检查`null`或者包含列表为空，这不是很好吗？好吧，让我们看看我们的老朋友`forEach`能为我们做些什么。

```
const counters = [];

...some code that may or may not define a counter inside counters...

counters.forEach( counter -> counter++ ); 
```

Enter fullscreen mode Exit fullscreen mode

然后，嘣！这次行动绝不会失败！如果`counters`为空，那么操作将运行 0 次。如果`counters`中有 1 个计数器，操作将运行 1 次。最后，如果`counters`中有`n`计数器，操作将运行`n`次。

太神奇了！我们不再需要检查未定义的值。我们可以可靠地定义容器列表并在这些列表上运行列表操作，这保证了在没有定义值时操作不会抛出异常。