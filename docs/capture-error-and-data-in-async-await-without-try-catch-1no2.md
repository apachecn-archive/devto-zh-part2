# 在不使用 try-catch 的 async-await 中捕获错误和数据

> 原文：<https://dev.to/sadarshannaiynar/capture-error-and-data-in-async-await-without-try-catch-1no2>

席卷 Javascript 社区的一件事是 async-await 的引入。它很简单，看起来比当时的承诺更好，也比回调地狱更具可读性和可调试性。但是困扰我的一件事是 try-catch 的用法。起初我认为这不是一个问题，但正如命运安排的那样，我正在处理链式 API 调用，问题突然出现了，每个 API 调用都有特定的错误消息，必须打印出来。我很快意识到我正在创造一个试捕地狱。

让我们考虑这个基于参数`rejectPromise`
在 2 秒后解决或拒绝的承诺

```
// api.js

const fetchData = async (duration, rejectPromise) => (
  new Promise((resolve, reject) => {
    setTimeout(() => {
      if (rejectPromise) {
        reject({
          error: 'Error Encountered',
          status: 'error'
        })
      }
      resolve({
        version: 1,
        hello: 'world',
      });
    }, duration);
  })
);

module.exports = {
  fetchData,
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以我对 async-await 的典型用法是这样的。

```
const { fetchData } = require('./api');

const callApi = async () => {
  try {
    const value = await fetchData(2000, false);
    console.info(value);
  } catch (error) {
    console.error(error);
  }
}

callApi();

/* 
 OUTPUT: 
 { version: 1, hello: 'world' } (rejectPromise=false)

 { error: 'Error Encountered', status: 'error' } (rejectPromise=true)

 */ 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，当`rejectPromise`参数为`false`时，await 解析为`{ version: 1, hello: 'world' }`，当参数为`true`时，它拒绝承诺，并调用 catch，错误为`{ error: 'Error Encountered', status: 'error' }`。

这是 async-await 的典型实现。现在我们将利用 promise 函数 then-catch 来简化这个过程。让我们写一个包装器来做这件事。

```
// wrapper.js

const wrapper = promise => (
  promise
    .then(data => ({ data, error: null }))
    .catch(error => ({ error, data: null }))
);

module.exports = wrapper; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到包装器将一个承诺作为输入，并通过 then-catch 返回解析/拒绝的值。因此，让我们修改我们在 try-catch 中编写的原始代码，以利用包装器。

```
const { fetchData } = require('./api');
const wrapper = require('./wrapper');

const callApi = async () => {
  const { error, data } = await wrapper(fetchData(2000, false));
  if (!error) {
    console.info(data);
    return;
  }
  console.error(error);
}

callApi();

/* 
 OUTPUT: 
 { version: 1, hello: 'world' } (rejectPromise=false)

 { error: 'Error Encountered', status: 'error' } (rejectPromise=true)

 */ 
```

Enter fullscreen mode Exit fullscreen mode

瞧，同样的输出，但这种方式可以更好地理解代码。