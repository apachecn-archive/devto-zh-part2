# 超时提取

> 原文：<https://dev.to/stereobooster/fetch-with-a-timeout-3d6>

你可能知道(或者马上就会知道)新的 Web API `fetch`没有超时。我想这是因为它更多的是围绕流的概念来构建的。
如果你和我一样，想对 AJAX 使用`fetch`(`XMLHttpRequest`)，你会希望它支持超时。我谷歌了一下，确实有人有同样的问题。我注意到的是，大多数解决方案都是围绕`setTimeout`和`Promise.reject`构建的，大多数解决方案都懒得取消实际的请求。

所以我创建了这个片段，它不仅会超时，还会取消网络请求:

```
const timeoutableFetch = (url, options = {}) => {
  let { timeout = 5000, ...rest } = options;
  if (rest.signal) throw new Error("Signal not supported in timeoutable fetch");
  const controller = new AbortController();
  const { signal } = controller;
  return new Promise((resolve, reject) => {
    const timer = setTimeout(() => {
      reject(new Error("Timeout for Promise"));
      controller.abort();
    }, timeout);
    fetch(url, { signal, ...rest })
      .finally(() => clearTimeout(timer))
      .then(resolve, reject);
  });
}; 
```

需要 [`AbortController`](https://caniuse.com/#search=AbortController) 。WDYT？