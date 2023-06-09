# 异步无等待，等待无异步

> 原文：<https://dev.to/codeprototype/async-without-await-await-without-async--oom>

假设我们有一个函数`getPromise()`,它返回一个承诺，这个承诺将在未来解析为某个值。问题是，如果我们想(I)使用`async/await`风格或者(ii)使用`then`子句调用它，我们是否需要用`async`关键字声明它。例如，在同一个代码库中，有些时候我们想这样称呼它:

```
//Snippet 1\. Call using async/await
(async() => {
    try {
        const result = await getPromise("a");   
        console.log('async/await -> ', result);
    } catch (err) {
        console.log(err);
    }
})(); 
```

...而在另一些时候，就像这样:

```
//Snippet 2\. Call using then clause
(() => {
    getPromise("b")
    .then(result => {
        console.log('then -> ', result);
    })
    .catch(err => {
        console.log(err);
    });
})(); 
```

那我们是不是应该这样声明`getPromise()`？

```
//Snippet 3\. Declare without async keyword
const getPromise = (s) => {  
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(s), 500);
    });
} 
```

...还是这样？

```
//Snippet 4\. Declare with async keyword
const getPromise = async (s) => {  
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(s), 500);
    });
} 
```

事实证明，以上所有的组合都是可能的，因为`async/await`仍然是基于承诺的:

1.  如果一个函数是用`async`关键字声明的，我们可以用`await`关键字调用它。这就像代码片段 4(用`async`声明`getPromise`和代码片段 1(用`await`调用)。这里应该没有什么惊喜。

2.  但是如果我们声明`getPromise` **而没有**`async`关键字(片段 3)，我们仍然可以用`await`关键字调用它。原因是`getpromise()`返回一个`Promise object`。如果一个函数返回一个承诺，我们可以用`await`调用它。所以片段 3 和片段 1 仍然一起工作。

3.  使用`then`子句调用怎么样？当然，在`async/await`之前，当我们只有 Promise 的时候，我们声明一个没有 async 关键字的函数**(片段 3)，我们用`then`子句调用它(片段 2)。那是当时的做法。现在仍然有可能这样做。这一点也不奇怪。**

4.  那么我们能否用 async 关键字声明`getPromise()`(片段 4)并用`then`子句调用它(片段 2)？是的，那也行得通。

5.  不要做的是以下情况。作为调用方函数的外部函数是在没有 async 关键字的情况下声明的。将导致运行时错误。

```
const getPromise = async (s) => {    
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(s), 500);
    });
}

//
(() => { //no async keyword here
  try {
    const result = await getPromise("a");   
    console.log('async/await -> ', result);
  } catch (err) {
    console.log(err);
  }
})(); 
```

6...但是下面的就可以了(这就是为什么我把这篇文章命名为`async`而没有`await`，因为这里根本没有`await`:

```
const getPromise = async (s) => {    
   return new Promise((resolve, reject) => {
     setTimeout(() => resolve(s), 500);
   });
}
(async() => {
    getPromise("b")
    .then(result => {
        console.log('then -> ', result);
    })
    .catch(err => {
    console.log(err);
    });
})(); 
```

七...最后，我们调用没有 await 关键字的`getPromise()`。在这种情况下，`result`还没有被解包，所以它仍然只是一个 Promise 对象。

```
const getPromise = async (s) => {    
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(s), 500);
  });
}

(async() => {
    try {
        const result = getPromise("a"); //no await, result has not been unwrapped 
        console.log('async/await -> ', result);
    } catch (err) {
        console.log(err);
    }
})(); 
```