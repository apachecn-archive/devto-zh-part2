# Javascript 获取，失败后重试。

> 原文：<https://dev.to/ycmjason/javascript-fetch-retry-upon-failure-3p6g>

> 由于某些原因，上一篇文章没有出现在我的个人资料中，所以这篇文章被重新发表。

所以最近碰到了网络随机工作/故障的情况。因为它影响了我的测试结果的一致性，我决定实现一个`fetch_retry`函数，它在失败时重试`fetch`达`n`次。

# 简介

Javascript 中的 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 很棒。我希望你会同意，它提供了一个简单但足够健壮的接口来处理我们的 AJAX 请求。

然而，网络并不总是像我们希望的那样工作，它可能会随机出现故障。为了抓住这个问题，让我们实现一个函数`fetch_retry(url, options, n)`，它执行`fetch(url, options)`，但是在失败时重试`n`次。从而增加成功的机会。

# 让我们想想

重试听起来像是一个循环。为什么我们不写一个 for/while 循环来实现呢？或许，像下面这样的？

```
function fetch_retry(url, options, n) {
    for(let i = 0; i < n; i++){
        fetch(url, options);
        if(succeed) return result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**没有！** Fetch 是一个异步函数，这意味着程序在继续之前不会等待结果！无论之前的调用是否成功，取操作都将被同时调用！

这不是我们想要的。这不是失败后重试，这是同时提取`n`次！(也就是说，如果写得正确，它也可能增加成功的机会。或许带着类似 [`Promsie.any`](http://bluebirdjs.com/docs/api/promise.any.html) 的东西？虽然我不是蓝鸟的忠实粉丝。我觉得原生承诺已经足够好了。)

如果你不知道 Javascript 中的异步函数和`Promise`，在继续阅读之前，请看这个由[杰西卡·克尔](https://dev.to/jessitron)制作的[这里](https://www.youtube.com/watch?v=h2CsiEum0CU)！

# 简述取物

所以`fetch`返回一个`Promise`。我们通常这样称呼它。

```
fetch(url, { method: 'GET' }).then(res => console.log('done'));
console.log('fetching...'); 
```

Enter fullscreen mode Exit fullscreen mode

如果你对`Promise`理解正确，你应该预料到结果是:

```
fetching...
done 
```

Enter fullscreen mode Exit fullscreen mode

如果网络由于某种原因出现故障，`Promise`会拒绝，我们可以捕获如下错误:

```
fetch(url, { method: 'GET' }).catch(err => /* ... */); 
```

Enter fullscreen mode Exit fullscreen mode

# 那么如何实现呢？

## `fetch_retry`是干什么的？

我们从思考我们希望函数`fetch_retry`做什么开始。我们知道它必须以某种方式调用 fetch，所以让我们把它写下来。

```
function fetch_retry(url, options, n) {
    fetch(url, options)
        .then(function(result) {
            /* on success */
        }).catch(function(error) {
            /* on failure */
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在显然`fetch_retry`必须是一个异步函数，因为我们不能真正从异步函数中定义一个同步函数。(或者我们可以吗？启发我。)

定义:*所以这意味着`fetch_retry`应该返回一个`Promise`，如果`n`尝试中的任何尝试成功，则解析；如果所有`n`尝试失败，则拒绝。*

所以现在我们来回一个`Promise`。

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) { // <--- we know it is asynchronous, so just return a promise first!
        fetch(url, options)
            .then(function(result) {
                /* on success */
            }).catch(function(error) {
                /* on failure */
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如果`fetch`成功了呢？

因此，如果获取成功，我们显然可以通过调用`resolve`函数来解析我们返回的承诺。于是代码变成:

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options)
            .then(function(result) {
                /* on success */
                resolve(result); // <--- yeah! we are done!
            }).catch(function(error) {
                /* on failure */
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 万一`fetch`失败了怎么办？

面对失败我们该怎么办？由于我们之前讨论过的异步属性，在这里执行 for/while 循环实际上没有什么帮助。但是有一件事我们可以做 for/while 循环所做的。有印象吗？是啊！**递归！**

我在做递归时的两条经验法则:

1.  不要递归地思考。不要试图递归地遵循你的代码。
2.  信念的飞跃，假设你定义的递归函数有效。

这两点根本是一样的！如果你有信心的飞跃，你就不会递归地思考代码。

好吧，那么让我们试着抓住**信心的飞跃**并假设`fetch_retry`会神奇地工作。

如果有效，那么在`on failure`中，如果我们调用`fetch_retry(url, options, n - 1)`会发生什么？

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options)
            .then(function(result) {
                /* on success */
                resolve(result);
            })
            .catch(function(error) {
                /* on failure */
                fetch_retry(url, options, n - 1) // <--- leap of faith, this will just work magically! Don't worry!
                    .then(/* one of the remaining (n - 1) fetch succeed */)
                    .catch(/* remaining (n - 1) fetch failed */);
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

`fetch_retry(url, options, n - 1)`将通过**的信念跳跃**神奇地工作，并将返回一个`Promise`，根据我们之前讨论的定义，如果任何尝试(来自`n - 1`的尝试)成功，它将解决，如果所有的`n - 1`尝试失败，它将拒绝。

那么现在，在递归调用之后我们做什么呢？请注意，由于`fetch_retry(url, options, n - 1)`会神奇地工作，这意味着我们此时已经完成了所有的`n`获取。在`on failure`的情况下，如果`fetch_retry(url, options, n - 1)`解析，则简单解析，如果拒绝，则拒绝。

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options)
            .then(function(result) {
                /* on success */
                resolve(result);
            })
            .catch(function(error) {
                fetch_retry(url, options, n - 1)
                    .then(resolve)  // <--- simply resolve
                    .catch(reject); // <--- simply reject
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们快到了！我们知道这个递归调用需要一个基本案例。当考虑基本情况时，我们看函数参数，并决定在什么情况下我们可以立即给出结果。

答案是当`n === 1`和`fetch`失效时。在这种情况下，我们可以简单地拒绝来自`fetch`的错误，而不递归地调用`fetch_retry`。

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options)
            .then(function(result) {
                /* on success */
                resolve(result);
            })
            .catch(function(error) {
                if (n === 1) return reject(error); // <--- base case!
                fetch_retry(url, options, n - 1)
                    .then(resolve)
                    .catch(reject);
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

## 收拾东西

### 冗余功能

在我们的“成功时”函数中，我们只是简单地调用`resolve(result)`。所以这个函数实例是多余的，我们可以简单地使用`resolve`作为“成功”函数。所以代码会变成:

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options).then(resolve) // <--- Much cleaner!
            .catch(function(error) {
                if (n === 1) return reject(error);
                fetch_retry(url, options, n - 1)
                    .then(resolve)
                    .catch(reject);
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

### 多余的承诺

现在我们在这里做的另一件蠢事是这一行:

```
fetch_retry(url, options, n - 1).then(resolve).catch(reject) 
```

Enter fullscreen mode Exit fullscreen mode

你看出问题出在哪里了吗？

让我把它放在上下文中，我们本质上是这样做的:

```
new Promise(function(resolve, reject) {
    fetch_retry(url, options, n - 1).then(resolve).catch(reject)
}); 
```

Enter fullscreen mode Exit fullscreen mode

所以这个新的承诺在这种情况下是多余的，因为如果`fetch_retry`解决，它就解决，如果`fetch_retry`拒绝，它就拒绝。所以基本上它的行为和`fetch_retry`的行为完全一样！

所以上面的代码在语义上和`fetch_retry`本身是一样的。

```
fetch_retry(url, options, n - 1)
// sementically the same thing as the following
new Promise(function(resolve, reject) {
    fetch_retry(url, options, n - 1).then(resolve).catch(reject)
}); 
```

Enter fullscreen mode Exit fullscreen mode

它需要更多的知识来清理代码。我们可以用下面的方式链接。因为`promise.then`也是回一个承诺！

```
Promise.resolve(3).then(function(i) {
    return i * 2;
}).then(function(i) {
    return i === 6; // this will be true
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们可以将处理后的值向前传递给下一个`then`，以此类推。如果值是一个`Promise`，那么下一个`then`将接收返回的`Promise`解析的任何内容。见下文:

```
Promise.resolve(3).then(function(i) {
    return i * 2;
}).then(function(i) {
    return Promise.resolve(i * 2); // also work!
}).then(function(i) {
    return i === 12; // this is true! i is not a Promise!
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样的想法也适用于`catch`！感谢[科伦廷](https://dev.to/viki53)的大声呼喊！这意味着我们甚至可以解决一个被拒绝的承诺。下面是一个例子:

```
Promise.resolve(3).then(function(i) {
    throw "something's not right";
}).catch(function(i) {
    return i
}).then(function(i) {
    return i === "something's not right";
}; 
```

Enter fullscreen mode Exit fullscreen mode

那么我们如何用这些知识来清理呢？我们的代码似乎更复杂。

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options).then(resolve)
            .catch(function(error) {
                if (n === 1) return reject(error);
                fetch_retry(url, options, n - 1)
                    .then(resolve)  // <--- we try to remove this
                    .catch(reject); // <--- and this
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我们可以用`fetch_retry`返回的承诺来解决返回的承诺！而不是`fetch_retry(...).then(resolve).catch(reject)`。我们可以做`resolve(fetch_retry(...))`！于是代码变成:

```
function fetch_retry(url, options, n) {
    return new Promise(function(resolve, reject) {
        fetch(url, options).then(resolve)
            .catch(function(error) {
                if (n === 1) return reject(error);
                resolve(fetch_retry(url, options, n - 1)); // <--- clean, isn't it?
            })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以更进一步，通过解析`catch`中的承诺来移除`Promise`的显式创建。

```
function fetch_retry(url, options, n) {
    return fetch(url, options).catch(function(error) {
        if (n === 1) throw error;
        return fetch_retry(url, options, n - 1);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

引用 MDN 中的一些词语，并将其调整为更通俗的术语:

> 如果处理程序抛出错误或返回本身被拒绝的承诺，则 catch(处理程序)返回的承诺被拒绝；否则，它被解决。

### ES6

我可以预见一些 JS 专家会因为我没有使用箭头函数而恨我。我没有为不习惯的人使用箭头功能。这里是用箭头函数写的 ES6 版本，我就不多解释了。

```
const fetch_retry = (url, options, n) => fetch(url, options).catch(function(error) {
    if (n === 1) throw error;
    return fetch_retry(url, options, n - 1);
}); 
```

Enter fullscreen mode Exit fullscreen mode

开心吗？

### ES7

是啊是啊，一旦 ES7 异步/等待命中，`Promise`很快就会变得落后。所以这里有一个异步/等待版本:

```
const fetch_retry = async (url, options, n) => {
    try {
        return await fetch(url, options)
    } catch(err) {
        if (n === 1) throw err;
        return await fetch_retry(url, options, n - 1);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

哪个看起来更整洁，对吗？

事实上，我们不必在 ES7 中使用递归，我们可以使用简单的 for 循环来定义它。

```
const fetch_retry = async (url, options, n) => {
    let error;
    for (let i = 0; i < n; i++) {
        try {
            return await fetch(url, options);
        } catch (err) {
            error = err;
        }
    }
    throw error;
};

// or (tell me which one u like better, I can't decide.)

const fetch_retry = async (url, options, n) => {
    for (let i = 0; i < n; i++) {
        try {
            return await fetch(url, options);
        } catch (err) {
            const isLastAttempt = i + 1 === n;
            if (isLastAttempt) throw err;
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

最后，我们看了同一个函数的 4 个不同版本。其中三个是递归的，只是以不同的风格和口味编写。最后一个是 for 循环。让我们回顾一下:

## 原始版本

```
function fetch_retry(url, options, n) {
    return fetch(url, options).catch(function(error) {
        if (n === 1) throw error;
        return fetch_retry(url, options, n - 1);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

## ES6

```
const fetch_retry = (url, options, n) => fetch(url, options).catch(function(error) {
    if (n === 1) throw error;
    return fetch_retry(url, options, n - 1);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## ES7 异步/等待递归

这是我最喜欢的。

```
const fetch_retry = async (url, options, n) => {
    try {
        return await fetch(url, options)
    } catch(err) {
        if (n === 1) throw err;
        return await fetch_retry(url, options, n - 1);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## ES7 异步/等待循环

```
const fetch_retry = async (url, options, n) => {
    let error;
    for (let i = 0; i < n; i++) {
        try {
            return await fetch(url, options);
        } catch (err) {
            error = err;
        }
    }
    throw error;
};

// or (tell me which one u like better, I can't decide.)

const fetch_retry = async (url, options, n) => {
    for (let i = 0; i < n; i++) {
        try {
            return await fetch(url, options);
        } catch (err) {
            const isLastAttempt = i + 1 === n;
            if (isLastAttempt) throw err;
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在评论里告诉我你的反馈！:D