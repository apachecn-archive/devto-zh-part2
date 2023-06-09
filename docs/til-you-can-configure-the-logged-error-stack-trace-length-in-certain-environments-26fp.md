# TIL:您可以在某些环境中配置记录的错误堆栈跟踪长度

> 原文：<https://dev.to/stefanjudis/til-you-can-configure-the-logged-error-stack-trace-length-in-certain-environments-26fp>

调试 JavaScript 应用程序可能很难。今天我读了一篇关于调试函数 JavaScript 的文章，发现了一个小细节。

让我们假设您的应用程序内部出现了一个错误。

```
main();

function main() {
  one();
}

function one() {
  two();
}

function two() {
  three();
}

// you get the idea :)

function ten () {
  eleven()
}

function eleven () {
  throw new Error("I can't see the whole stack strace")
} 
```

Enter fullscreen mode Exit fullscreen mode

当您在 Chrome 或 Node.js 中执行此操作时，您会得到以下打印到控制台的堆栈跟踪。

```
Uncaught Error: I can't see the whole stack strace
    at eleven (<anonymous>:49:9)
    at ten (<anonymous>:45:3)
    at nine (<anonymous>:41:3)
    at eight (<anonymous>:37:3)
    at seven (<anonymous>:33:3)
    at six (<anonymous>:29:3)
    at five (<anonymous>:25:3)
    at four (<anonymous>:21:3)
    at three (<anonymous>:17:3)
    at two (<anonymous>:13:3) 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，堆栈跟踪的前两个函数调用(`main`和`one`)被省略了。原来，您可以使用`Error.stackTraceLimit`来配置打印堆栈跟踪的长度。当您面临应用程序中隐藏的错误时，这种配置使您能够丰富日志记录。

```
Error.stackTraceLimit = 20; 
```

Enter fullscreen mode Exit fullscreen mode

当您增加该值时，您将看到记录的整个跟踪。

```
Uncaught Error: I can't see the whole stack strace
    at eleven (<anonymous>:50:9)
    at ten (<anonymous>:46:3)
    at nine (<anonymous>:42:3)
    at eight (<anonymous>:38:3)
    at seven (<anonymous>:34:3)
    at six (<anonymous>:30:3)
    at five (<anonymous>:26:3)
    at four (<anonymous>:22:3)
    at three (<anonymous>:18:3)
    at two (<anonymous>:14:3)
    at one (<anonymous>:10:3)
    at main (<anonymous>:6:3)
    at <anonymous>:2:1 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用`Error.stackTraceLimit`，你必须意识到这样一个事实:根据 MDN 的说法，[是一个非标准，快速检查就会发现它在 Firefox 中也不受支持。我没有检查边缘。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Microsoft_Extensions/Error.stackTraceLimit)

此外，Chrome 和 Safari 也支持它，但使用不同的默认值。Chrome 配`10`，Safari 配`100`。当你环顾四周，你还会看到 [CodeSandbox](https://codesandbox.io) 增加了对`50`的限制。

在我看来，这种配置没有什么突破性的，但有一天当我在浏览器或 Node.js 应用程序中调试大型 JS 应用程序时，它可能会变得很方便。不过，它不会取代调试器。:)