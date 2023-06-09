# Async，Await -抱歉，什么 JavaScript？

> 原文：<https://dev.to/marpme/async-await---sorry-what-javascript--2m4a>

Async & Await，这两个词你可能在 JavaScript 职业生涯的早期就听说过，或者你的老师告诉你利用 Async/Await 使你的函数成为一个非阻塞函数，而你完全不知道这是怎么回事！？好吧，那么让我们进入这篇文章的第一章。我们将首先关注拥有一个`async`函数的方面，然后深入探讨 await 的用法，因为它对我们开发人员来说更像是一个`syntactic sugar`。

## 异步

异步，也称为异步，是一种使代码非阻塞的常用技术。但这意味着什么呢？我们用一个基本的 NodeJS 例子来解释一下:

```
const fs = require('fs')

// 1\. Example, the common sync'd code execute 
// logic: everything will stop here until it has finally loaded the file (imagine a file of 2GB)
const myFilesContent = fs.readFileSync('./path/to/file')

// 2\. Example Async (callback based) 
// logic: Start loading the file, and "call" me back later when you are done
fs.readFile('./path/to/file', (error, buffer) => {
    // ah we got that file read into our memory and it's read for usage
});

// 2\. Example Async (promised based) 
// logic: Start loading the file and after finishing loading, then please execute the code
new Promise((resolve, reject) => fs.readFile('./path/to/file', (error, buffer) => {
    // function without a return either reject if error are happening
    // or resolve the to resolving object
    // The promise will wait until either of those two function has been called!
    if(err){
        reject(err)
    }
    resolve(buffer)
})).then((fileBuffer) => {
    // thank you for providing me with your buffer.
}).catch(error => {
    // ahh, snap something wrong happened!
}) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这看起来很简单，但是:

*   异步部分在哪里？
*   Javascript 中没有使用异步函数的指示符吗？

基本上，是的，有，但它们很少被使用，因为它们是在最新的标准化周期中添加的。这就是为什么您将会遇到的大多数项目可能会对异步操作应用`callback based`或`promised based`执行的原因。

最终，当你遇到回调和/或承诺时，你要记住这样的异步结构。

让我们看看`syntactic sugar`世界，在那里我们可以使用关键字`async`来指示一个函数充当异步函数。我们还将更深入地了解它在后台实际做了什么。*(趣闻:`async functions`会把你的产出包装成新的承诺)*

```
// async function way of doing asynchronous fetching
const fetchNewsletter = async options => {
  // fetch your newsletter and await its arrival
  // Caution: fetch is already a function which returns a promise
  const response = await fetch(
    'https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty',
    options
  );
  // check if the response is ok, like the normal way you would do it
  if (!response.ok) {
    throw new Error('Ups.');
  }
  // Caution: .json() returns a new Promised value!
  // the returned value can be everything and will be wrapped into a new promise
  return await response.json();
};

// the above shown function will look something like this:
const fetchNewsletterPromised = options =>
  fetch(
    'https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty',
    options
  ).then(response => {
    if (!response.ok) {
      throw new Error('Ups.');
    }

    return response.json();
  });

// syntactic sugar function
fetchNewsletter()
  .then(newsletter => {
    // have fun reading it later ;)
  })
  .then(error => {
    // ahhh snap!
  });

// non syntactic sugar function
fetchNewsletterPromised()
  .then(newsletter => {
    // have fun reading it later ;)
  })
  .then(error => {
    // ahhh snap!
  }); 
```

Enter fullscreen mode Exit fullscreen mode

作为一个备注，async 函数的初始代码似乎比承诺的要长一点，但在我看来这不是问题，因为它读起来很舒服，而且比有一个`then`的链更透明，如果你有一个以上的链要解决，这可能会变得非常非常令人不安，并产生更多的开销。

第一个函数中的`await`语法是做什么的？这就解开了我们在那里调用的 fetch 函数的承诺值。这里有一个不言自明的例子:

```
const XMASParty = async promisedPresent => {
  const myRealPresent = await promisedPresent;
  const mood = await myRealPresent.isWishedOne
    ? enjoyPresent(myRealPresent)
    : enjoyItNeverTheLess(myRealPresent);

  return mood;
}; 
```

Enter fullscreen mode Exit fullscreen mode

然而，我真的支持我们现在的趋势，我相信我们正在以一种非常好的方式修正 JavaScript 的大部分核心概念。尽管对于新手来说，在 JavaScript 中有近 3 种排列异步代码的模式是不寻常的。此外，它们还会导致语言中的许多陷阱，因为在进入 Async/Await 之前，您必须深入理解承诺。