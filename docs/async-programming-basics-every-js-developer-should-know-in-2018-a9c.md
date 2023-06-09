# 每个 JS 开发者都应该知道的异步编程基础知识

> 原文：<https://dev.to/siwalikm/async-programming-basics-every-js-developer-should-know-in-2018-a9c>

> 这篇文章的目标读者是刚开始用 javascript 进行异步编码的人，所以我们会通过避免大词、箭头函数、模板文字等来保持简单。

回调是现代函数式 javascript 中最常用的概念之一，如果您曾经使用过 jQuery，那么您很可能已经在不知不觉中使用过回调了(我们稍后会谈到它)。

## **回调函数**到底是什么？

简单来说，回调函数是作为参数传递给另一个函数的函数。然后在传递回调函数的函数内部执行该函数，并将最终结果返回给调用者。

```
// I'm sure you've seen a JQuery code snippet like this at some point in your life!
// The parameter we're passing to the `click` method here is a callback function.

$("button").click(function() {
    alert('clicked on button`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

简单吧？现在让我们实现一个回调函数来获得一个假想游戏中的分数。

`// levelOne() is called a high-order function because // it accepts another function as its parameter. function levelOne(value, callback) { var newScore = value + 5; callback(newScore); } // Please note that it is not mandatory to reference the callback function (line #3) as callback, it is named so just for better understanding. function startGame() { var currentScore = 5; console.log('Game Started! Current score is ' + currentScore); // Here the second parameter we're passing to levelOne is the // callback function, i.e., a function that gets passed as a parameter. levelOne(currentScore, function (levelOneReturnedValue) { console.log('Level One reached! New score is ' + levelOneReturnedValue); }); } startGame();`

一旦进入`startGame()`函数，我们调用`levelOne()`函数，参数为 currentScore 和我们的回调函数()。

当我们在`startGame()`函数的范围内以异步方式调用`levelOne()`时，javascript 执行函数`levelOne()`，主线程继续执行我们代码的剩余部分。

这意味着我们可以做各种各样的操作，比如从 API 获取数据，做一些数学运算等等。，一切都很耗时，因此我们不会为此阻塞主线程。一旦函数(`levelOne()`)完成了它的操作，它就可以执行我们之前传递的回调函数。

这是函数式编程的一个非常有用的特性，因为回调让我们可以异步处理代码，而不必等待响应。例如，您可以使用回调函数对慢速服务器进行 ajax 调用。完全忘记它，继续编写剩下的代码。一旦 ajax 调用得到解决，回调函数就会自动执行。

但是如果在一个链中要执行多个级别的回调，那么回调可能会变得很糟糕。让我们以上面的例子为例，给我们的游戏增加几个关卡。

`function levelOne(value, callback) { var newScore = value + 5; callback(newScore); } function levelTwo(value, callback) { var newScore = value + 10; callback(newScore); } function levelThree(value, callback) { var newScore = value + 30; callback(newScore); } // Note that it is not needed to reference the callback function as callback when we call levelOne(), levelTwo() or levelThree(), it can be named anything. function startGame() { var currentScore = 5; console.log('Game Started! Current score is ' + currentScore); levelOne(currentScore, function (levelOneReturnedValue) { console.log('Level One reached! New score is ' + levelOneReturnedValue); levelTwo(levelOneReturnedValue, function (levelTwoReturnedValue) { console.log('Level Two reached! New score is ' + levelTwoReturnedValue); levelThree(levelTwoReturnedValue, function (levelThreeReturnedValue) { console.log('Level Three reached! New score is ' + levelThreeReturnedValue); }); }); }); } startGame();`

等等，刚刚发生了什么？我们为电平逻辑增加了两个新功能，`levelTwo()`和`levelThree()`。在 levelOne 的回调函数(第 22 行)中，使用回调函数调用 levelTwo()函数。和 levelOne 的回调结果。对 levelThree()函数重复同样的操作。

[![callback meme](img/965c160258c03170486be763683c7df0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y8wwSdu---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ASf_k39ju9c2UxW-V.jpg)

现在想象一下，如果我们必须在另外 10 层实现相同的逻辑，这段代码会变成什么样。你已经慌了吗？嗯，我是！随着嵌套回调函数数量的增加，阅读代码变得更加困难，调试也更加困难。

这通常被亲切地称为**回调地狱**。有办法走出这个回调地狱吗？

## 我**答应**有更好的办法

Javascript 从 ES6 开始支持承诺。承诺基本上是表示异步操作的最终完成(或失败)及其结果值的对象。

```
// This is how a sample promise declaration looks like. The promise constructor
// takes one argument which is a callback with two parameters, `resolve` and
// `reject`. Do something within the callback, then call resolve if everything
// worked, otherwise call reject.

var promise = new Promise(function(resolve, reject) {
  // do a thing or twenty
  if (/* everything turned out fine */) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们试着用承诺重写回调地狱的例子。

`function levelOne(value) { var promise, newScore = value + 5; return promise = new Promise(function(resolve) { resolve(newScore); }); } function levelTwo(value) { var promise, newScore = value + 10; return promise = new Promise(function(resolve) { resolve(newScore); }); } function levelThree(value) { var promise, newScore = value + 30; return promise = new Promise(function(resolve) { resolve(newScore); }); } var startGame = new Promise(function (resolve, reject) { var currentScore = 5; console.log('Game Started! Current score is ' + currentScore); resolve(currentScore); }); // The response from startGame is automatically passed on to the function inside the subsequent then startGame.then(levelOne) .then(function (result) { // the value of result is the returned promise from levelOne function console.log('You have reached Level One! New score is ' + result); return result; }) .then(levelTwo).then(function (result) { console.log('You have reached Level Two! New score is ' + result); return result; }) .then(levelThree).then(function (result) { console.log('You have reached Level Three! New score is ' + result); });`

我们重写了一级(一级/二级/三级)函数，从函数 param 中移除回调函数，而不是调用它们内部的回调函数，而是替换为承诺。

一旦 startGame 被解析，我们可以简单地对它调用一个`.then()`方法并处理结果。我们可以用`.then() chaining`把多个承诺一个接一个的串起来。

这使得整个代码可读性更好，更容易理解正在发生的事情，以及接下来会发生什么等等。

承诺往往更好的深层原因是它们更容易组合，这大致意味着组合多个承诺“就行了”，而组合多个回调往往不行。

此外，当我们有一个单一的回调与一个单一的承诺，这是真的没有重大差异。当你有无数的回调和无数的承诺时，基于承诺的代码看起来会更好。

好了，我们已经成功地逃离了回调地狱，并使我们的代码可读性更好。但是如果我告诉你有一种方法可以让它更干净，更易读呢？

## **(一)等待**吧

从 ECMA2017 开始，javascript 就支持 Async- await。它们允许您编写基于承诺的代码，就像它是同步代码一样，但不会阻塞主线程。它们使您的异步代码不那么“聪明”，而更具可读性。

老实说，异步等待只不过是承诺之上的语法糖衣，但它使异步代码看起来和行为更像同步代码，这正是它的力量所在。

如果在函数定义之前使用了关键字`async`，那么可以在函数中使用`await`。当您`await`一个承诺时，该功能会以非阻塞的方式暂停，直到承诺完成。如果承诺兑现，你就能拿回价值。如果承诺拒绝，则抛出被拒绝的值。

现在让我们看看用异步等待重写后我们的游戏逻辑是什么样子的！

`function levelOne(value) { var promise, newScore = value + 5; return promise = new Promise(function(resolve) { resolve(newScore); }); } function levelTwo(value) { var promise, newScore = value + 10; return promise = new Promise(function(resolve) { resolve(newScore); }); } function levelThree(value) { var promise, newScore = value + 30; return promise = new Promise(function(resolve) { resolve(newScore); }); } // the async keyword tells the javascript engine that any function inside this function having the keyword await, should be treated as asynchronous code and should continue executing only once that function resolves or fails. async function startGame() { var currentScore = 5; console.log('Game Started! Current score is ' + currentScore); currentScore = await levelOne(currentScore); console.log('You have reached Level One! New score is ' + currentScore); currentScore = await levelTwo(currentScore); console.log('You have reached Level Two! New score is ' + currentScore); currentScore = await levelThree(currentScore); console.log('You have reached Level Three! New score is ' + currentScore); } startGame();`

我们的代码立刻变得可读性更好，但是还有更多需要异步等待。

错误处理是 Async-await 最突出的特性之一。最后，我们可以通过 try 和 catches 用同一个构造处理同步和异步错误，这对于没有重复 try-catch 块的承诺来说是一种痛苦。

《美好的承诺世界》的下一个最佳改进是代码调试。当我们编写基于箭头函数的承诺时，我们不能在箭头函数中设置断点，所以调试有时很困难。但是对于 async-waits，调试就像你如何做一段同步代码一样。

我相信现在您已经对 javascript 中的异步编程有了更好的理解。如果你有问题，请在下面告诉我。如果你觉得这很有帮助，[在 Twitter 上给我大声喊出来](https://twitter.com/intent/tweet?text=Checkout%20%22Async%20programming%20basics%20every%20JS%20developer%20should%20know%20in%202018%22%0Aby%20@siwalikm%20%F0%9F%98%8D%20https://dev.to/siwalikm/,sync-programming-basics-every-js-developer-should-know-in-2018-a9c)！

编码快乐！✌️