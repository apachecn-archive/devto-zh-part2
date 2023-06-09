# JavaScript 中的单个关键字表达式

> 原文：<https://dev.to/jochemstoel/single-keyword-expressions-in-javascript-35n9>

你们很多人对此都很熟悉。对于那些不了解的人来说，这是对 JavaScript 中 ummm *单关键字表达式*的简要介绍，因为没有更好的术语。我有时会收到问题。如果这确实有一个名字，那么请务必纠正我。

用一个 *getter* 定义(通常是无数的只读)全局属性的实践，以便以后用一个关键字语句引用它们。看:

```
Object.defineProperty(global, 'exit', {
    enumerable: false, /* this means it will not show up when iterating the 'global' object keys */
    get: () => process.exit()
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们在脚本中访问 *exit* 时，它调用 *getter* 函数，该函数将退出。你自己试试！

```
/* i refuse to explain this part */
for(let i = 0; i < 10; i++) {
    if(i > 5) {
        exit;
    }
    console.log(i)
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 输出

```
0
1
2
3
4
5 
```

Enter fullscreen mode Exit fullscreen mode

观察下面的代码，它用一个 getter 定义了两个全局无数的标识符来计算脚本执行时间。

```
Object.defineProperty(global, 'start', {
    enumerable: false, 
    get: () => { 
        this.startTime = new Date().getTime()
    }
}) /* when we reference 'start', store the current time in milliseconds in 'startTime' */

Object.defineProperty(global, 'end', {
    enumerable: false, 
    get: () => {
        let now = new Date().getTime()
        let difference = now - this.startTime 
        console.log(`process took ${difference} milliseconds.`)
        process.exit()
    }
}) /* when we reference 'end', log the difference between now and 'startTime' then exit */

start;

for(i = 0; i < 1000; i++) {
    console.log(i)
}

end; 
```

Enter fullscreen mode Exit fullscreen mode

#### 输出

```
0
1
2
3
...
...
...
997
998
999
process took 1848 milliseconds. 
```

Enter fullscreen mode Exit fullscreen mode

### 事物

上例中的 *this.startTime* 属性不是全局可访问的。如果你在 getter 函数中控制台记录 *this* ，你会得到一个只有 1 个属性 *startTime* 的对象。

```
/* startTime, this.startTime, global.startTime, start.startTime  do not exist */
start;
for(i = 0; i < 1000; i++) { /* ... */ }
end; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们说的是 ECMAScript，所以示例中的*开始*和*结束*引用不需要分号。一些开发人员更喜欢它，另一些人去打仗。

要知道，仅仅引用一个属性就已经调用了 getter。

```
start
setTimeout(end, 5000) /* this will exit immediately. */ 
```

Enter fullscreen mode Exit fullscreen mode

```
start 
setTimeout(() => end, 5000) /* this will exit in 5000 milliseconds. */ 
```

Enter fullscreen mode Exit fullscreen mode

我不会提供一些更复杂的例子，我会在这里停下来，希望你能想到一些，并在评论区与我们分享。感谢您的阅读。