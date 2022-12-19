# 不要让那个函数异步。

> 原文：<https://dev.to/clovis1122/dont-make-that-function-async-9m6>

今天是写 Javascript 代码的好日子。ES2015 和该语言每年都会推出的最新功能让它成为一种享受。在 ES2017 中，Javascript 获得了 Async/Await，这提供了一种更简洁、更具表现力的语法来处理异步代码。它在 Node 的最新版本中可用，并且可以使用 Babel 针对浏览器进行传输。

然而，尽管这可能很棒，但是将一个函数标记为 async 不会神奇地使您的代码更有性能。我经常遇到这种类型的代码:

```
async function saveUser(userData) {
  const user = await this.userRepository.saveUserInDatabase(userData);
  return user;
} 
```

Enter fullscreen mode Exit fullscreen mode

哦，*又是那个*类型的异步函数。

### 等待函数时会发生什么。

当使用关键字`await`时，Javascript 将暂停函数的执行，并将控制权返回给调用者，直到异步操作完成。在前面的例子中，我们的 saveUser 函数将保持待机状态，等待对方法`saveUserInDatabase(userData)`的调用所返回的承诺，以重新获得控制权并返回结果。

[![](../Images/99d68d3b608e01ec3ee75857970f9af1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6uSTt-Kd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnudzojp8zjbiyk5cuuw.png)

现在想象一下，我们让*的每个函数都是异步的*。每个函数都必须等待每个单独的函数被解析，然后该函数将取回控制权，只是将控制权交还给该函数的调用者。在这种情况下，节点进程必须启动/停止并保存/恢复每个函数的执行状态。这实际上降低了代码的性能！

想象一下，如果我们的系统中有几层，每一层都在等待另一层完成，以暂时获得控制权并再次归还。您的调用堆栈可能会变成这样:

[![](../Images/b3f7961dace36759cf9c00dc1fd22b2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4oMMQxXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9owrs9akdefu8fqw8r17.png)

### 异步创建函数的建议。

一般来说，异步功能只是一个承诺。它们代表一个尚未计算的未来值(也可以视为一个正在进行的事件)。尽可能延迟承诺的计算，直到需要该值为止，这是一个很好的做法。我认为异步函数是一个好主意，如果:

*   **您需要另一个异步函数的值:**例如，您可能希望在数据库层等待一个查询完成，以便您可以包装结果并将其转换为您的域的实体。
*   **在完成一个异步函数之后，你需要执行额外的步骤:**例如，如果我们想马上给他发送一封电子邮件，等待用户保存在数据库中是可以的。我们可以返回一个象征用户电子邮件的承诺。

```
async function saveUser(userData) {
  const user = await this.userRepository.saveUserInDatabase(userData); // We need the user.
  return this.sendUserEmail(user); // this.sendUserEmail is another async function.
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **您需要处理异常:**有时您想要运行异步操作，但是您需要对该层的最终失败做出响应。例如，如果我们必须在失败时删除用户配置文件图片，我们可以在创建用户时等待数据库层执行。

*   **它让代码更有表现力:**可能有一段时间，我们需要在性能和更干净的代码之间做出权衡:)。

### 裹足不前。

Async/Await 是 Javascript 世界中最好的东西之一。它是表达异步操作的一种强大而富于表现力的方式。有了这些优点，过度使用它会损害性能、可读性并造成复杂性——这种复杂性有时是不值得努力的。创建函数时，**尽快将控制权交还给函数的调用者**是个好主意。

编码快乐！