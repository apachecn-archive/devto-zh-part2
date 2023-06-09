# JavaScript:承诺和为什么 Async/Await 赢得了这场战斗

> 原文：<https://dev.to/nickparsons/javascript-promises-and-why-asyncawait-wins-the-battle-1g8a>

[![Async/Await](img/304ed730175db44b3c0699f7b0b531a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V9dp4nWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p9i8wektckvua2omfw4v.png)

异步函数在 JavaScript 中有好有坏。好的一面是异步函数是非阻塞的，因此速度很快——尤其是在 Node.js 上下文中。缺点是处理异步函数可能很麻烦，因为有时您必须等待一个函数完成，以便在继续下一个执行之前得到它的“回调”。

有几种方法可以发挥异步函数调用的优势，并正确地处理它们的执行，但是有一种方法比其他方法更好(剧透:它是 Async/Await)。在这篇快速阅读中，你将了解到[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)的来龙去脉和[异步/等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)的使用，以及我们对两者如何比较的看法。

**享受**！

# 承诺与回访🥊

作为一名 JavaScript 或 Node.js 开发人员，正确理解承诺和回调之间的区别*和*它们如何一起工作是至关重要的。

两者之间有微小但重要的差异。在每个承诺的核心，都有一个回调来解决某种数据(或错误),这种数据(或错误)会在被调用的承诺出现。

**回调处理程序:**

```
function done(err) {
    if (err) {
        console.log(err);
        return;
    }

    console.log('Passwords match!');
} 
```

调用`validatePassword()`函数:

```
function validatePassword(password) {
    if (password !== 'bambi') {
        return done('Password mismatch!');
    }

    return done(null);
} 
```

**下面的代码片段显示了验证密码的完整的端到端检查(它是静态的，必须匹配“bambi”，我小时候最喜欢的卡通人物)** :

```
// provided a string (password)
function validatePassword(password) {
    // create promise with resolve and reject as params
    return new Promise((resolve, reject) => {
        // validate that password matches bambi (the deer)
        if (password !== 'bambi') {
            // password doesn't match, return an error with reject
            return reject('Invalid Password!');
        }

        // password matches, return a success state with resolve
        resolve();
    });
}

function done(err) {
    // if an err was passed, console out a message
    if (err) {
        console.log(err);
        return; // stop execution
    }

    // console out a valid state
    console.log('Password is valid!');
}

// dummy password
const password = 'foo';

// using a promise, call the validate password function
validatePassword(password)
    .then(() => {
        // it was successful
        done(null);
    })
    .catch(err => {
        // an error occurred, call the done function and pass the err message
        done(err);
    }); 
```

代码被很好地注释了，但是，如果你感到困惑，catch 只在从 promise 调用了`reject()`的事件中执行。由于密码不匹配，我们调用`reject()`，因此“捕捉”错误并将其发送给`done()`函数。

# 承诺🤞

与传统的基于回调的方法相比，承诺为执行、组合和管理异步操作提供了一种更简单的替代方法。它们还允许您使用类似于同步 try/catch 的方法来处理异步错误。

**承诺还提供了三种独特的状态**:

1.  **Pending** -承诺的结果尚未确定，因为将产生其结果的异步操作尚未完成。
2.  **已完成**——异步操作已完成，承诺有值。
3.  **被拒绝** -异步操作失败，承诺永远无法兑现。在拒绝状态下，一个承诺有一个*原因*，表明操作失败的原因。

当承诺待定时，它可以转换到“已履行”或“已拒绝”状态。然而，一旦承诺被实现或拒绝，它将永远不会过渡到任何其他状态，其价值或失败原因也不会改变。

## 下行👎

承诺不做的一件事是解决所谓的“回调地狱”，它实际上只是一系列嵌套的函数调用。当然，打一个电话没问题。对于许多调用，您的代码变得难以阅读和维护。

## 在承诺中循环🎡

为了避免 JavaScript 的深度嵌套回调，人们会假设您可以简单地循环遍历承诺，将结果返回给一个对象或数组，当它完成时就会停止。可惜没那么容易；由于 JavaScript 的异步特性，如果您遍历每个承诺，当您的代码完成时，不会调用“done”事件。

处理这种情况的正确方法是使用 [Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 。该功能在被标记为完成之前等待所有完成(或第一次拒绝)。

## 错误处理💣

用多个嵌套的 Promise 调用进行错误处理就像蒙着眼睛开车一样。祝你好运，找出是哪个承诺出了错。最好的办法是完全删除`catch()`方法，并选择加入一个全局错误处理程序(交叉手指)，如下所示:

**浏览器:**

```
window.addEventListener('unhandledrejection', event => {
    // can prevent error output on the console:
    event.preventDefault();

    // send error to log server
    log('Reason: ' + event.reason);
}); 
```

**Node.js:**

```
process.on('unhandledRejection', (reason) => {
    console.log('Reason: ' + reason);
}); 
```

> 注意:以上两个选项是确保捕捉错误的仅有的两种方法。如果您错过添加一个`catch()`方法，它将被代码吞噬。

# 异步/等待？🤔

Async/Await 允许我们编写看起来同步的异步 JavaScript。在这篇文章的前几部分，你已经了解了承诺——它们被*认为*是为了简化异步流和避免回调——但是它们没有。

## 回调地狱？🔥

回调-地狱是用于描述以下场景的术语:

> 注意:例如，这里有一个 API 调用，它将从一个数组中获取 4 个特定的用户。

```
// users to retrieve
const users = [
    'W8lbAokuirfdlTJpnsNC5kryuHtu1G53',
    'ZinqxnohbXMQdtF6avtlUkxLLknRxCTh',
    'ynQePb3RB2JSx4iziGYMM5eXgkwnufS5',
    'EtT2haq2sNoWnNjmeyZnfUmZn9Ihfi8w'
];

// array to hold response
let response = [];

// fetch all 4 users and return responses to the response array
function getUsers(userId) {
    axios
        .get(`/users/userId=${users[0]}`)
        .then(res => {
            // save the response for user 1
            response.push(res);

            axios
                .get(`/users/userId=${users[1]}`)
                .then(res => {
                    // save the response for user 2
                    response.push(res);

                    axios
                        .get(`/users/userId=${users[2]}`)
                        .then(res => {
                            // save the response for user 3
                            response.push(2);

                            axios
                                .get(`/users/userId=${users[3]}`)
                                .then(res => {
                                    // save the response for user 4
                                    response.push(res);
                                })
                                .catch(err => {
                                    // handle error
                                    console.log(err);
                                });
                        })
                        .catch(err => {
                            // handle error
                            console.log(err);
                        });
                })
                .catch(err => {
                    // handle error
                    console.log(err);
                });
        })
        .catch(err => {
            // handle error
            console.log(err);
        });
} 
```

咻，那太难看了，而且在代码中占据了**吨**的空间。Async/Await 是 JavaScript 最新也是最棒的东西，它不仅让我们避免了回调地狱，还确保了我们的代码是干净的，错误被正确捕获。我发现 Async/Await 最吸引人的地方是它建立在承诺之上(非阻塞等)。)，但允许代码可读，并且读起来好像是同步的。这就是力量所在。

> 注意:这里有一个从数组中检索 4 个用户的同一套 API 调用的例子，用了一半以上的代码:

```
// users to retrieve
const users = [
    'W8lbAokuirfdlTJpnsNC5kryuHtu1G53',
    'ZinqxnohbXMQdtF6avtlUkxLLknRxCTh',
    'ynQePb3RB2JSx4iziGYMM5eXgkwnufS5',
    'EtT2haq2sNoWnNjmeyZnfUmZn9Ihfi8w'
];

// array to hold response
let response = [];

async function getUsers(users) {
    try {
        response[0] = await axios.get(`/users/userId=${users[0]}`);
        response[1] = await axios.get(`/users/userId=${users[1]}`);
        response[2] = await axios.get(`/users/userId=${users[2]}`);
        response[3] = await axios.get(`/users/userId=${users[3]}`);
    } catch (err) {
        console.log(err);
    }
} 
```

很棒，对吧？💃

因为 Async/Await 是建立在承诺之上的，所以您甚至可以将`Promise.all()`与 Await 关键字:
一起使用

```
async function fetchUsers() {
  const user1 = getUser1();
  const user2 = getUser2();
  const user3 = getUser3();

  const results = await Promise.all([user1, user2, user3]);
} 
```

> 注意:Async/await 由于其同步特性而稍慢。在一行中多次使用它时应该小心，因为 await 关键字会停止执行它后面的所有代码——就像在同步代码中一样。

## 如何开始使用异步/等待？💻

使用 Async/Await 非常容易理解和使用。事实上，在 Node.js 的最新版本中,*已经可以直接使用了，并且很快就可以在浏览器中使用了。现在，如果你想在客户端使用它，你需要使用 [Babel](https://babeljs.io/) ，一个易于使用和设置的网络传输器。*

### 异步

让我们从 async 关键字开始。可以放在函数之前，像这样:

```
async function returnTrue() {
  return true;
} 
```

### 等待

关键字 await 让 JavaScript 一直等到承诺完成并返回结果。这里有一个例子:

```
let value = await promise; // only works inside of an async function 
```

### 完整示例

```
// this function will return true after 1 second (see the async keyword in front of function)
async function returnTrue() {

  // create a new promise inside of the async function
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve(true), 1000) // resolve
  });

  // wait for the promise to resolve
  let result = await promise;

  // console log the result (true)
  console.log(result);
}

// call the function
returnTrue(); 
```

## 为什么 Async/Await 更好？😁

既然我们已经讨论了 Async/Await 必须提供的承诺，让我们回顾一下为什么我们( [Stream](https://getstream.io) )认为 Async/Await 是我们代码库的最佳选择。

1.  Async/Await 允许使用更少的代码行、更少的输入和更少的错误来创建一个干净简洁的代码库。最终，它使复杂的嵌套代码变得可读。
2.  用 try/catch 处理错误(在一个地方，而不是在每个调用中)
3.  错误堆栈是有意义的，与您从承诺中收到的不明确的堆栈相反，后者很大，很难定位错误的来源。最好的是，错误指向错误来自的函数。

# 最后的想法📃

我可以说 Async/Await 是过去几年 JavaScript 中添加的最强大的特性之一。

只花了不到一天的时间就理解了语法，并看到了我们的代码库在这方面是多么的混乱。将我们所有的基于 Promise 的代码转换成 Async/Await 总共花了大约两天时间，这实际上是一次彻底的重写——这正好说明了使用 Async/Await 时需要的代码是多么少。

最后，感谢你阅读这篇文章。如果你对我整天在 Stream 上做的事情感兴趣，你应该试试我们的 [5 分钟 API 教程](https://getstream.io/try-the-api)——我保证这是值得的。更多精彩的帖子，你也可以在 Twitter 上关注我——[@ nick Parsons](https://twitter.com/@nickparsons)。

**编码快乐！🤓**