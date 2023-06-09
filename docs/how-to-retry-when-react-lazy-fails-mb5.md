# React lazy 失败时如何重试

> 原文：<https://dev.to/goenning/how-to-retry-when-react-lazy-fails-mb5>

React 16.6 已经发布，现在通过使用 lazy 和悬念在 React 应用程序中进行代码拆分比以往任何时候都更容易。

如果你不知道我在说什么，你绝对应该看看这篇[https://reactjs.org/blog/2018/10/23/react-v-16-6.html](https://reactjs.org/blog/2018/10/23/react-v-16-6.html)

在对一个使用 lazy 的生产应用程序进行了几天的监控之后，我注意到了一些类似这样的客户端错误:

```
Loading chunk 6 failed. (error: https://.../6.4e464a072cc0e5e27a07.js)
Loading CSS chunk 6 failed. (https://.../6.38a8cd5e9daba617fb66.css) 
```

Enter fullscreen mode Exit fullscreen mode

**为什么？！**

我其实也不知道为什么，只能假设这和用户网络有关。也许他们用的是慢速 3G，网络出现了故障？这并不罕见，对吧？

好吧，如果这是真的，我们怎么解决？

当网络请求失败时，我们可以做每个人都会做的事情:重试！😄

**如何？**

你知道进口吗...)我们在懒人上用的函数只是一个返回承诺的函数？也就是说，你可以像对待其他承诺一样把它锁起来。

下面你可以找到一个重试功能的基本实现。

```
function retry(fn, retriesLeft = 5, interval = 1000) {
  return new Promise((resolve, reject) => {
    fn()
      .then(resolve)
      .catch((error) => {
        setTimeout(() => {
          if (retriesLeft === 1) {
            // reject('maximum retries exceeded');
            reject(error);
            return;
          }

          // Passing on "reject" is the important part
          retry(fn, retriesLeft - 1, interval).then(resolve, reject);
        }, interval);
      });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

> 来源:https://gist.github.com/briancavalier/842626

现在我们只需要将它应用到我们的惰性导入中。

```
// Code split without retry login
const ProductList = lazy(() => import("./path/to/productlist"));

// Code split with retry login
const ProductList = lazy(() => retry(() => import("./path/to/productlist"))); 
```

Enter fullscreen mode Exit fullscreen mode

如果浏览器无法下载该模块，它将重试 5 次，每次尝试之间有 1 秒钟的延迟。如果在 5 次尝试之后仍导入它，则会抛出一个错误。

仅此而已！🎉

谢谢！