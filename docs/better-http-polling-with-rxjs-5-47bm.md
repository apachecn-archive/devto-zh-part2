# 用 RxJS 5 实现更好的 HTTP 轮询

> 原文：<https://dev.to/hugo__df/better-http-polling-with-rxjs-5-47bm>

下面是一个函数的实现，它采用一个基于承诺的获取函数和一个决定响应是否成功(从轮询的角度)的函数，并轮询获取函数，直到`isSuccess`函数返回`true` :

```
import { Observable, from, interval } from 'rxjs';
import { switchMap, takeWhile } from 'rxjs/operators';

const POLLING_INTERVAL = 600; // in milliseconds

function poll(fetchFn, isSuccessFn, pollInterval = POLLING_INTERVAL) {
  return interval(POLLING_INTERVAL).pipe(
      switchMap(() => from(fetchFn())),
      takeWhile((response) => isSuccessFn(response))
  );
} 
```

上面利用了 RxJS 5 并使用了它新的`pipe`语法。如果我们走过它:

1.  使用`interval`创建每 600 毫秒发射一次的可观测值(默认)
2.  将该操作符输入到`switchMap`中，该操作符实际上将可观察值替换为另一个值，在这种情况下，用从`fetchFn`返回的承诺中创建的可观察值替换发出的间隔计数
3.  `takeWhile``isSuccessFn`返回真

更详细地说一下`switchMap`，这有点像在承诺链中做这件事:

```
const promiseChain = Promise
  .resolve()
  .then(
    () => someFunctionThatReturnsAPromise()
  ); 
```

现在被`.then`运算到`promiseChain`上的任何东西现在都将有`someFunctionThatReturnsAPromise`的输出被传入。

回到投票函数，这是一个如何使用它的例子:

```
import axios from 'axios;
poll(
  axios.get('https://changing-status.com/status'),
  (response) => response.data.status === 'success'
).subscribe((response) => {
  // response is HTTP response
  console.log(response.data.status);
}); 
```

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

这种基于观察的方法的优点是我们可以访问每个 HTTP 响应。如果我们需要在某个地方显示状态，这很好。
可观测量也是非常可组合的，所以当投票结束时，要将我们得到的东西转化成只发出一次的东西，我们也可以这样做:

```
import { skipWhile } from 'rxjs/operators';
const emitOnPollComplete = poll(
  axios.get('https://changing-status.com/status'),
  (response) => response.data.status === 'success'
).pipe(
  skipWhile((response) => response.data.status !== 'success')
).subscribe((response) => {
  console.log('Poll complete');
}); 
```

由 Jp Valery 拍摄的封面照片