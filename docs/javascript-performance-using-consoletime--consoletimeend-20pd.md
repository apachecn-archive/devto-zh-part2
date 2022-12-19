# 使用 console.time() & console.timeEnd()的 JavaScript 性能

> 原文：<https://dev.to/3sanket3/javascript-performance-using-consoletime--consoletimeend-20pd>

很多时候，检查代码执行所花费的时间是必要的。JavaScript 在客户端的本地机器上运行，我们不希望我们的应用程序运行缓慢，即使在硬件配置较低的设备上也是如此。

以前，我使用打印当前时间戳的方法来手动检查花费了多少时间。让我们直接进入代码:

```
let startTime = new Date().getTime();

console.log("started");

setTimeout(() => {
  console.log(
    "ended in " + (new Date().getTime() - startTime) + " milliseconds"
  );
}, 3000); 
```

输出将是:

> 开始于 3001 毫秒，结束于 3001 毫秒

但是后来我发现有内置的方法可以帮助完成同样的任务。他们是`console.time('some text')`和`console.timeEnd('some text')`。这些可以让我们的生活更容易看到流逝的时间。上面代码示例的重构版本应该是:

```
console.time("timer");

setTimeout(() => {
  console.timeEnd("timer");
}, 3000); 
```

输出将是:

> "计时器:375 毫秒"

这是一种非常干净的测试代码执行时间的非黑客方式。

希望你觉得有用。编码快乐！

感谢 Ronak Baldha 对这篇文章的评论。