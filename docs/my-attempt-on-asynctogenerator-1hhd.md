# 我在异步发电机上的尝试()

> 原文：<https://dev.to/ycmjason/my-attempt-on-asynctogenerator-1hhd>

不打算解释太多，只是分享一下我最近在实现`asyncToGenerator()`上的尝试。请务必告诉我你的想法。😀

```
function asyncToGenerator(fn) {
  const ensurePromise = v => Promise.resolve(v);

  const stepContext = (context, nextOrThrow, prev) => {
    const { value, done } = context[nextOrThrow](prev);

    if (done) return ensurePromise(value);

    return ensurePromise(value)
      .then(v => stepContext(context, 'next', v))
      .catch(err => stepContext(context, 'throw', err));
  };

  return function(...args) {
    const context = fn.apply(this, args); 
    return stepContext(context, 'next');
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用:

```
asyncToGenerator(function* () {
  const res = yield axios.get('https://www.ycmjason.com');
  console.log(res);
})(); 
```

Enter fullscreen mode Exit fullscreen mode