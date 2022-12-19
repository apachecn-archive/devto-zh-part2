# 用 RxJS 在 JavaScript 中进行反应式编程。

> 原文：<https://dev.to/sagar/reactive-programming-in-javascript-with-rxjs-4jom>

RxJS 是 JavaScript 库，用于转换、合成和查询异步数据流。RxJS 既可以在浏览器中使用，也可以使用 Node.js 在服务器端使用。

我接受了一个挑战，用一种简单的方式向开发人员解释 RxJS。学习 RxJS 最难的部分是**“反应性地思考”**。

> 可以将 RxJS 视为处理异步事件的“LoDash”。

那么，反应式编程到底是什么？

[![Reactive programming](../Images/993d59e53aa7fe114b0e4d83803de720.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2N_OhV_b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pgszjn5110pixunu5uw4.jpg)

反应式编程是一种编写代码的编程范式，主要关注**异步数据流。**这是一种构建软件应用程序的不同方式，它将对发生的变化做出“反应”,而不是编写软件的典型方式，在这种方式中，我们显式地编写代码(也称为“命令式”编程)来处理这些变化。

### 流

[![stream](../Images/c3ec20786dd6c3870b2d180ce8b40e5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BDszDe7P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5tiqri5aefo9imzdm7i.jpg)

流是按时间顺序排列的一系列正在进行的事件。它可以是任何东西，如用户输入、按钮点击或数据结构。你可以听一个流，并对它做出相应的反应。您可以使用函数来合并、过滤或映射流。

流在其时间线中发出三样东西，一个值、一个错误和完整信号。我们必须捕捉这个异步事件，并相应地执行函数。

promise 和 observables 都是为解决异步问题而构建的(以避免“回调地狱”)。

**现代网络应用中异步操作的类型**

*   DOM 事件-(鼠标事件、触摸事件、键盘事件、表单事件等)
*   动画- (CSS 过渡和动画，requestAnimationFrame 等)
*   AJAX
*   [WebSockets](https://pusher.com/websockets)
*   [SSE -服务器发送的事件](https://en.wikipedia.org/wiki/Server-sent_events)
*   替代输入(语音、操纵杆等)

如果您仍然感到困惑，不要担心，这通常在这一点上没有多大意义。让我们一步一步来。

### 可观察的

[![observable](../Images/0290ba7bea01740db686b72584e25e7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IHHdkY3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fe7a7rcqoj3zib8szr4h.jpg)

*   可观测值只是一个函数，有一些特殊的特征。它接受一个“观察者”(一个带有“下一个”、“错误”和“完成”方法的对象)，并返回取消逻辑。
*   Observables 支持在应用程序中的发布者和订阅者之间传递消息。
*   与其他事件处理、异步编程和处理多个值的技术相比，Observables 具有显著的优势。
*   可观者懒惰。直到你订阅它，它才开始产生数据。
*   `subscribe()`返回一个订阅，消费者可以调用`unsubscribe()`来取消订阅并撕掉生产者。
*   RxJS 提供了许多函数，可以用来创建新的可观测量。这些函数可以简化从事件、计时器、承诺等事物中创建可观测量的过程。例如:

```
 const button = document.querySelector("button");
    const observer = {
      next: function(value) {
        console.log(value);
      },
      error: function(err) {
        console.error(err);
      },
      complete: function() {
        console.log("Completed");
      }
    };

    // Create an Observable from event
    const observable = Rx.Observable.fromEvent(button, "click");
    // Subscribe to begin listening for async result
    observable.subscribe(observer); 
```

Enter fullscreen mode Exit fullscreen mode

### 订阅

[![subscription](../Images/bba17b1ea3f0f856cf7cf51833072cde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HJ0-5sVo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7hevybycq37g57jgugx6.jpg)

*   只有当有人订阅时，一个可观察的实例才开始发布值。您通过调用实例的`subscribe()`方法进行订阅，传递一个`observer`对象来接收通知。
*   订阅有一个重要的方法`unsubscribe()`，它没有参数，只是处理订阅持有的资源。

```
 const button = document.querySelector("button");
    const observable = Rx.Observable.fromEvent(button, "click");
    const subscription = observable.subscribe(event => console.log(event));
    // Later:
    // This cancels the ongoing Observable execution which
    // was started by calling subscribe with an Observer.
    subscription.unsubscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

### 观察者

[![observer](../Images/577a8b09035ac2bc13c2bd2bb9d1fa18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SD5lFff5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iy4ya5tq9indrx8zuee5.jpg)

*   一个`observer`是具有`next()`、`error()`和`complete()`功能的对象文字。在上面的例子中，观察者是我们传递给`.subscribe()`方法的对象。
*   当一个可观察对象产生值时，它通知观察者，当一个新值被成功捕获时调用`.next()`方法，当一个错误发生时调用`.error()`。
*   当我们订阅一个可观察的，它会不断传递值给一个观察者，直到完整的信号。
*   观察者的例子。

```
 // observer is just object literal with next(), error() and complete() functions
    // Howerver, next() function is required, remaining error() and complete() functions are optional 
    const observer = {
      next: function(value) {
        console.log(value);
      },
      error: function(err) {
        console.error(err);
      },
      complete: function() {
        console.log("Completed");
      }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

### 运算符

[![operator](../Images/c8d209d0d2b870149c575cc24f0f8645.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NvfIlRCI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/egbbrtsok7k4pfyo8tjq.png)

*   操作符是建立在 Observables 基础上的函数，可以对集合进行复杂的操作。
*   算子本质上是一个纯函数，它将一个可观察量作为输入，并生成另一个可观察量作为输出。
*   有用于不同目的的操作符，它们可以被分类为创建、转换、过滤、组合、多播、错误处理、实用程序等。
*   运算符在处理集合中的下一个值之前，将每个值从一个运算符传递到下一个运算符。这不同于数组操作符(map 和 filter ),数组操作符会在每一步处理整个数组。
*   举个例子，

```
 const observable = Rx.Observable.of(1, 2, 3).map(value => value * value);

    observable.subscribe(x => console.log(x));
    // Output:
    // 1
    // 4
    // 9 
```

Enter fullscreen mode Exit fullscreen mode

*   RxJS 提供了许多运算符，但只有少数几个经常使用。有关操作符和使用示例的列表，请访问 [RxJS API 文档](http://reactivex.io/rxjs/manual/overview.html#operators)。

[![common operator list](../Images/c6e8e71df57916ad589798e2f6840231.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B619sX-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dvnx7lgy02cseedxscno.png)

### 主题

[![subject](../Images/ae37c7bef103b15ec819042183bc23aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4wT0JGF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2c54wvzfvr7c7b2bxor1.jpg)

*   RxJS Subject 是一种特殊类型的可观察对象，它允许将值**多播给许多观察者**。普通的可观察对象是单播的(每个订阅的观察者拥有可观察对象的独立执行)，**主题是组播的**。
*   RxJS 中的主体是一种特殊的混合体，可以同时充当可观察对象和观察者。
*   在下面的例子中，我们有两个观察者连接到一个主题，我们向主题提供一些值:

```
 const subject = new Rx.Subject();

    subject.subscribe({
      next: v => console.log("observerA: " + v)
    });
    subject.subscribe({
      next: v => console.log("observerB: " + v)
    });

    subject.next(1);
    subject.next(2);

    // output
    // observerA: 1
    // observerB: 1
    // observerA: 2
    // observerB: 2 
```

Enter fullscreen mode Exit fullscreen mode

### 可观察与承诺

[![Observable vs Promise](../Images/697d0241a214a51aa981ff7e3c1f8890.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SRaVrvvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43c9w4p1dw99m0uvcrqs.jpg)

为了更好地理解，我们将比较 ES6 Promise API 和 Observable library RxJS。我们将会看到相似的承诺和可观察到的东西是如何的相似，以及它们是如何的不同，以及为什么在某些情况下我们要用可观察到的东西来代替承诺。

**单值对多值**

*   如果你通过承诺提出请求并等待回应。您可以确保对同一个请求不会有多个响应。你可以创建一个承诺，它用一些值来解决。
*   Promise 总是用传递给 resolve 函数的第一个值进行解析，并忽略对它的进一步调用。
*   相反，可观测量允许你解析多个值，直到我们调用`observer.complete()`函数。
*   承诺和可观察的例子。

    ```
    // creating demoPromise using ES6 Promise API
    const demoPromise = new Promise((resolve, reject) => {
      asyncOperation((err, value) => {
        if (err) {
          reject(err); // error occured. We will catch error inside chain .catch()
        } else {
          resolve(value); // value received. we will get value inside .then() chain method
        }
      });
    });

    // creating a demoObservable using Rxjs.Observable API
    const demoObservable = Rx.Observable.create(observer => {
      asyncOperation((err, value) => {
        if (err) {
          observer.error(err); // instead of reject(err)
        } else {
          observer.next(value); // instead of resolve(value)
          observer.complete(); // optional. once your async task finished then call observer.complete()
        }
      });
    }); 
    ```

**渴望 vs 懒惰**

*   承诺是急切的，这意味着一旦调用了承诺构造函数，承诺就会开始执行您交给它的任何任务。
*   可观者懒惰。只有当有人真正订阅了一个可观察构造函数时，它才会被调用，这意味着在你订阅它之前什么都不会发生。
*   例子，

```
 // demoPromise started emmiting values but still we have not call .then() method on promise
    const demoPromise = new Promise((resolve, reject) => {
      setTimeout(() => {
        console.log('emmit value');
        resolve(100);
      }, 3000);
    });

    // demoObservable not started emmiting values unitll we subscribe to it.
    const demoObservable = new Observable(observer => {
      setInterval(() => {
        if (err) {
          observer.error('DemoError throw'); // instead of reject(err)
        } else {
          observer.next('value'); // instead of resolve(value)
          observer.complete(); // optional. once your async task finished then call observer.complete()
        }
      });
    }); 
```

Enter fullscreen mode Exit fullscreen mode

**不可取消对可取消**

*   new promise 用户经常想知道的第一件事是如何取消一个承诺。ES6 承诺还不支持取消。事实上，取消在客户端编程中是一个非常重要的场景。
*   使用像`bluebird`或`axios`这样的第三方库，他们提供承诺取消功能。
*   Observable 支持通过调用 Observable 上的`unsubscribe()`方法来取消异步任务。
*   当您订阅一个可观察对象时，您会得到一个订阅，它代表正在进行的执行。只需调用`unsubscribe()`取消执行。
*   可取消观察值的示例

```
 const observable = Rx.Observable.from([10, 20, 30]);
    const subscription = observable.subscribe(x => console.log(x));
    // Later:
    subscription.unsubscribe(); // its will stop ongoing execution 
```

Enter fullscreen mode Exit fullscreen mode

### 实际例子

**从数值中创造可观测量**

```
 const observable = Rx.Observable.of("foo", 98, false, ["john", "doe"], {
    age: 19,
    gender: "male"
  });

  observable.subscribe(val => console.log(val)); 
```

Enter fullscreen mode Exit fullscreen mode

**从价值流中创造可观测量**

```
 const observable = Rx.Observable.create( observer => {
    observer.next('Hello');
    observer.next('Its monday morning!!');
  });

  observable.subscribe(value => console.log(value));
  // output:
  // Hello
  // It's monday morning 
```

Enter fullscreen mode Exit fullscreen mode

**可从 DOM 事件中观察到的**

```
 const button = document.querySelector('button');
    const observable = Rx.Observable.fromEvent(button, 'click');
    observable.subscribe(event => console.log(event)); 
```

Enter fullscreen mode Exit fullscreen mode

**可从承诺中观察到**

```
 const promise = new Promise((resolve, reject) => {
    asyncOperation((err, value) => {
      if (err) {
        reject(err);
      } else {
        resolve(value);
      }
    });
  });

  const Observable = Rx.Observable.fromPromise(promise);

  Observable.subscribe(value => console.log(value)); 
```

Enter fullscreen mode Exit fullscreen mode

**从计时器可观察到的方法**

```
 const timer = Rx.Observable.timer(3000);

  timer.subscribe(() => console.log("timeout!!")); 
```

Enter fullscreen mode Exit fullscreen mode

**可从区间**观察到的

```
 const interval = Rx.Observable.interval(3000);

  interval.subscribe(tick => console.log(`${tick} tick`)); 
```

Enter fullscreen mode Exit fullscreen mode

**地图操作员**

```
 const observable = Rx.Observable.from(2, 4, 6, 8);

  observable.map(value => value * value).subscribe(result => console.log(result)); 
```

Enter fullscreen mode Exit fullscreen mode

**做操作员**

```
 const dogs = Rx.Observable.of("Buddy", "Charlie", "Cooper", "Rocky");

    // do operator used for debugging purpose
    dogs
      .do(dog => console.log(dog))
      .filter(dog => dog === "Cooper")
      .do(dog => console.log(dog))
      .subscribe(dog => console.log(dog)); 
```

Enter fullscreen mode Exit fullscreen mode

**去抖和节流**

*   等待 X 次，然后给我最后一个值。
*   Throttle -给我第一个值，然后等待 X 次。

```
 const input = document.querySelector("input");
    const observable = Rx.Observable.fromEvent(input, "keyup");

    observable.debounceTime(3000).subscribe(event => console.log(event));

    observable.throttleTime(1000).subscribe(event => console.log(event)); 
```

Enter fullscreen mode Exit fullscreen mode

**buffer time**——收集过去的值作为一个数组，并定期发出这些数组。

```
 const clicks = Rx.Observable.fromEvent(document, "click");
      const buffered = clicks.bufferTime(1000);
      buffered.subscribe(x => console.log(x)); 
```

Enter fullscreen mode Exit fullscreen mode

**结论**

promise 最适合 AJAX 操作，其中 Observables 对于处理异步任务非常强大。Observables 提供了一组操作符，用于创建、转换、过滤和多播异步事件。听起来很棒，不是吗？:D

**结束语**

感谢阅读。我希望你喜欢这篇文章，请随意喜欢、评论或与你的朋友分享这篇文章。为了更深入地了解 RxJS checkout 提供了参考链接。

**参考文献**

1.  [RxJS 官网](http://reactivex.io/rxjs/)
2.  [你错过的反应式编程入门](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
3.  [LearnRxJS](https://www.learnrxjs.io/)
4.  [RxJS 是什么？](https://egghead.io/lessons/rxjs-what-is-rxjs)
5.  [RxJS 快速入门 20 个实战范例](https://angularfirebase.com/lessons/rxjs-quickstart-with-20-examples/)
6.  [棱角官网](https://angular.io/guide/rx-library)
7.  [RxJS:可观测量、观测器和操作器介绍](https://toddmotto.com/rxjs-observables-observers-operators)
8.  [承诺与可观察到的事情](https://medium.com/@mpodlasin/promises-vs-observables-4c123c51fe13)