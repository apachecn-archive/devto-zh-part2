# JavaScript 中的循环是如何工作的😭🤔

> 原文：<https://dev.to/sait/how-loops-work-in-javascript-4e3g>

## 你是否曾经对在 JavaScript 中使用不同类型的循环感到困惑

今天没问题，我们正在清除困惑

让我们用不同的循环打印 1-100 的数字。

首先，我们使用 For 循环打印 100 个数字。

```
for(let i=0; i<=100;i=i+1){
  console.log(i)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来说说上面的代码有什么用。

**for 循环采用三个可选表达式**

1) **初始化**:我们正在初始化我们的值——(设 I = 0)；

2) **条件**:在哪个条件下迭代，如果条件为真
则循环继续，否则循环停止运行。(I<= 100)；

3) **最终表达式**:循环如何在每次迭代中递增或递减(i=i+1)

在`for`循环中，我们记录每次迭代的值。

在没有 for 循环的情况下，我们需要使用
for 循环一个接一个地手工编写 100 个数字，只需要 2 行代码。

### 让我们用 *While* 循环来解决同样的问题。

```
let n=1
while(n<=100){
 console.log(n);
 n=n+1;
} 
```

Enter fullscreen mode Exit fullscreen mode

while 循环仅在某些情况为真时运行。否则不会进入循环内部的
。

*   我们首先用 1 初始化变量 n 的值。

*   while 循环只接受作为条件的单个表达式(n<=100)。

*   它最多只能运行到 n 值等于 100。

*   在第 2 行，我们在每次迭代中增加 n。

现在让我们用 if 和 while 循环来解决同一个问题。

```
let enter=true;
let n=1;
if(enter){
while(n<=100){
 console.log(n);
 n=n+1;
}
} 
```

Enter fullscreen mode Exit fullscreen mode

## if 条件也以真值为基础运行。

*   在上面的问题中，enter 为真，所以它进入循环。
*   在 if 条件中，我们放置了一个 while 循环。

如果条件在日常生活中起作用。在我们的日常生活中，我们会做出一些决定，比如如果时间是早上 9 点，我需要吃早餐。如果时间是 12 点，我需要吃午饭。

你想过如果-否则。如果时间是上午 9 点，我需要吃早餐，否则我需要做一些其他的事情(刷牙，洗澡等)..).

**希望你们喜欢这些**
👍👍👍👍👍👍👍👍

看看我其他有趣的帖子

*   [JavaScript 中有趣的函数是如何工作的](https://dev.to/saigowthamr/how-fun-funfunctions-workin-javascript-nbo)

*   [CSS 特异性如何工作🤹🏼‍♀️](https://dev.to/saigowthamr/how-css-specificity-works--2e2e)