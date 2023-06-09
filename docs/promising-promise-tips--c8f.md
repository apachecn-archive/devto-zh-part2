# 9 个有希望的承诺技巧

> 原文：<https://dev.to/kepta/promising-promise-tips--c8f>

承诺是很好的合作伙伴！你的同事也是这么说的。

[![prom](img/0833d026f88c7e53627bc1140ce41ab6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zlauxVhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6966254/36483828-3e361d88-16e5-11e8-9f11-cbe99d719066.png)

这篇文章会告诉你如何改善你和承诺的关系。

# 1。你可以在 a 里面返回一个承诺

让我突出最重要的提示

> **是的！你可以在 a 里面返回一个承诺.然后**

同样，返回的承诺在下一个`.then`
中自动展开

```
.then(r => {
    return serverStatusPromise(r); // this is a promise of { statusCode: 200 }
})
.then(resp => {
    console.log(resp.statusCode); // 200; notice the automatic unwrapping of promise
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 2。每一次你都创造了一个新的承诺。然后

如果你熟悉 javascript 的点链接风格，你会有宾至如归的感觉。但对于一个新人来说，这可能并不明显。

在承诺中，每当你`.then`或`.catch`时，你都在创造一个新的承诺。这个承诺是你刚刚链接的承诺和你刚刚附加的`.then` / `.catch`的组合。

让我们来看一个例子:

```
var statusProm = fetchServerStatus();

var promA = statusProm.then(r => (r.statusCode === 200 ? "good" : "bad"));

var promB = promA.then(r => (r === "good" ? "ALL OK" : "NOTOK"));

var promC = statusProm.then(r => fetchThisAnotherThing()); 
```

Enter fullscreen mode Exit fullscreen mode

上述承诺的关系可以用一个流程图来描述:
[![image](img/b06f71da18487f89f983e8e81cbcc508.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gf5-9vXv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6966254/36400725-dac92186-15a0-11e8-8b4f-6a344e6a5229.png)

这里需要注意的重要一点是，`promA`、`promB`和`promC`都是不同的承诺，但却是相关的。

我喜欢把它想象成一个巨大的管道，当父节点出现故障时，水将停止流向子节点。例如，如果`promB`失败，其他节点不会受到影响，但如果`statusProm`失败，所有节点都会受到影响，即`rejected`。

# 3。每个人的承诺都被解决/拒绝

我发现这是让承诺变得更好的最重要的事情之一。简而言之，如果一个承诺在你的应用程序的多个部分之间共享，那么当它到达`resolved/rejected`时，所有的部分都会得到通知。

> 这也意味着没有人可以改变你的承诺，所以请放心传递它，不要担心。

```
function yourFunc() {
  const yourAwesomeProm = makeMeProm();

  yourEvilUncle(yourAwesomeProm); // rest assured you promise will work, regardless of how evil uncle consumes your promise

  return yourAwesomeProm.then(r => importantProcessing(r));
}

function yourEvilUncle(prom) {
  return prom.then(r => Promise.reject("destroy!!")); // your evil uncle
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，你可以看到，设计的承诺使任何人都难以做邪恶的事情。如我上面所说，`Keep calm and pass the promise around`

# 4。Promise 构造函数不是解决方案

我看到开发人员到处利用构造器风格，认为他们是在用承诺的方式做这件事。但这是一个弥天大谎，实际原因是构造函数 API 与好的旧回调 API 和旧习惯难改非常相似。

> **如果你发现自己到处写`Promise constructors`，那你就做错了！**

为了向前迈出一步，远离回调，您需要小心地尽量减少 Promise 构造函数的使用量。

让我们跳到 a `Promise constructor` :
的实际用例

```
return new Promise((res, rej) => {
  fs.readFile("/etc/passwd", function(err, data) {
    if (err) return rej(err);
    return res(data);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

`Promise constructor`**应该只在你想把回拨转换成承诺**的时候使用。
一旦你掌握了这种创造承诺的美丽方式，将它用在其他已经承诺的地方会变得非常诱人！

让我们看一个多余的`Promise constructor`

**不对**

```
return new Promise((res, rej) => {
    var fetchPromise = fetchSomeData(.....);
    fetchPromise
        .then(data => {
            res(data); // wrong!!!
        })
        .catch(err => rej(err))
}) 
```

Enter fullscreen mode Exit fullscreen mode

💖**纠正**

```
return fetchSomeData(...); // when it looks right, it is right! 
```

Enter fullscreen mode Exit fullscreen mode

用`Promise constructor`包装一个承诺只是**的多余，违背了承诺本身的目的**。

😎 **Protip**

如果你是一个 **nodejs** 的人，我推荐去看看 [util.promisify](http://2ality.com/2017/05/util-promisify.html) 。这个小东西帮助你将节点风格的回调转化为承诺。

```
const {promisify} = require('util');
const fs = require('fs');

const readFileAsync = promisify(fs.readFile);

readFileAsync('myfile.txt', 'utf-8')
  .then(r => console.log(r))
  .catch(e => console.error(e)); 
```

Enter fullscreen mode Exit fullscreen mode

# 5。使用承诺。解决

Javascript 提供了`Promise.resolve`，这是写类似这样的东西的简写:

```
var similarProm = new Promise(res => res(5));
// ^^ is equivalent to
var prom = Promise.resolve(5); 
```

Enter fullscreen mode Exit fullscreen mode

这有多个用例，我最喜欢的一个是能够将常规(同步)javascript 对象转换成承诺。

```
// converting a sync function to an async function
function foo() {
  return Promise.resolve(5);
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以将它用作一个安全包装，包装您不确定是承诺值还是常规值的值。

```
function goodProm(maybePromise) {
  return Promise.resolve(maybePromise);
}

goodProm(5).then(console.log); // 5

goodProm(Promise.resolve(Promise.resolve(5))).then(console.log); // 5, Notice it unwraps all the layers of promises automagically! 
```

Enter fullscreen mode Exit fullscreen mode

# 6。使用承诺。拒绝

Javascript 也提供了`Promise.reject`，是这个
的简写

```
var rejProm = new Promise((res, reject) => reject(5));

rejProm.catch(e => console.log(e)) // 5 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的一个用例是用`Promise.reject`提前拒绝。

```
function foo(myVal) {
    if (!mVal) {
        return Promise.reject(new Error('myVal is required'))
    }
    return new Promise((res, rej) => {
        // your big callback to promie conversion!
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

简单来说，在任何你想拒绝承诺的地方使用`Promise.reject`。

在下面的例子中我使用了一个`.then`

```
.then(val => {
  if (val != 5) {
    return Promise.reject('Not Good');
  }
})
.catch(e => console.log(e)) // Not Good 
```

Enter fullscreen mode Exit fullscreen mode

*注意:你可以像`Promise.resolve`一样在`Promise.reject`里面放任何值。您经常在被拒绝的承诺中发现`Error`的原因是它主要用于抛出异步错误。*

# 7。使用 Promise.all

Javascript 提供了 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) ，这是……的简写。我想不出这个😁。

在伪算法中，`Promise.all`可以概括为

```
Takes an array of promises

    then waits for all of them to finish

    then returns a new Promise which resolves into an Array

    catches if even a single fails/rejects. 
```

Enter fullscreen mode Exit fullscreen mode

以下示例显示了所有承诺何时解决:

```
var prom1 = Promise.resolve(5);
var prom2 = fetchServerStatus(); // returns a promise of {statusCode: 200}

Proimise.all([prom1, prom2])
.then([val1, val2] => { // notice that it resolves into an Array
    console.log(val1); // 5
    console.log(val2.statusCode); // 200
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个显示其中一个失败的时候:

```
var prom1 = Promise.reject(5);
var prom2 = fetchServerStatus(); // returns a promise of {statusCode: 200}

Proimise.all([prom1, prom2])
.then([val1, val2] => {
    console.log(val1); 
    console.log(val2.statusCode); 
})
.catch(e =>  console.log(e)) // 5, jumps directly to .catch 
```

Enter fullscreen mode Exit fullscreen mode

*注意:`Promise.all`是聪明！在拒绝的情况下，它不会等待所有的承诺完成！。每当任何一个承诺被拒绝，它会立即中止，而不等待其他承诺完成。*

😎 **Protip**
`Promise.all`不提供批量执行承诺(并发)的方法，因为根据设计，承诺在创建时就被执行。如果你想控制执行力，我推荐试试[蓝鸟. map](http://bluebirdjs.com/docs/api/promise.map.html) 。( **感谢 [Mauro](https://dev.to/mgtitimoli) 的这个提示。** )

# 8。不要害怕被拒绝*或*

# 不要在每个后面追加多余的`.catch`。然后

我们有多害怕错误在两者之间被吞噬？

为了克服这种恐惧，这里有一个非常简单的提示:

> **使拒绝处理父函数的问题。**

理想情况下，拒绝处理应该是你的应用程序的根本，所有的承诺拒绝都会渗透到其中。

不要害怕写这样的东西

```
return fetchSomeData(...); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你想在你的职能中处理拒绝，决定你是想解决事情还是继续拒绝。

💘**解决拒绝**

解决拒绝很简单，在`.catch`中，你返回的任何东西都被认为是解决了的。然而有一个条件(双关语)，如果你在一个`.catch`中返回一个`Promise.reject`，这个承诺将被拒绝。

```
.then(() => 5.length) // <-- something wrong happenned here
.catch(e => {
        return 5;  // <-- making javascript great again
})
.then(r => {
    console.log(r); // 5
})
.catch(e => {
    console.error(e); // this function will never be called :)
}) 
```

Enter fullscreen mode Exit fullscreen mode

💔**拒绝一个拒绝**
拒绝一个拒绝很简单，**什么都不做。**如我上面所说，让它成为一些其他功能的问题。通常情况下，父函数比当前函数有更好的方法来处理拒绝。

重要的是要记住，一旦你写了一个 catch，就意味着你在处理错误。这类似于 sync `try/catch`的工作方式。

如果你确实想拦截一个拒绝:(我强烈建议不要！)

```
.then(() => 5.length) // <-- something wrong happenned here
.catch(e => {
  errorLogger(e); // do something impure
  return Promise.reject(e); // reject it, Yes you can do that!
})
.then(r => {
    console.log(r); // this .then (or any subsequent .then) will never be called as we rejected it above :)
})
.catch(e => {
    console.error(e); //<-- it becomes this catch's problem
}) 
```

Enter fullscreen mode Exit fullscreen mode

**之间的细微差别。然后是(x，y)然后是(x)。catch(x)**
`.then`接受第二个回调参数，该参数也可用于处理错误。这可能看起来类似于做类似于`then(x).catch(x)`的事情，但是这两个错误处理程序在它们捕捉的错误方面是不同的。

我将让下面的例子来说明这一点。

```
.then(function() {
   return Promise.reject(new Error('something wrong happened'));
}).catch(function(e) {
   console.error(e); // something wrong happened
});

.then(function() {
   return Promise.reject(new Error('something wrong happened'));
}, function(e) { // callback handles error coming from the chain above the current `.then`
    console.error(e); // no error logged
}); 
```

Enter fullscreen mode Exit fullscreen mode

当你想处理来自承诺的错误时,`.then(x,y)`真的很方便。`then`ing and not want handle from`.then`你刚刚追加到承诺链上。

*注意:99.9%的情况下，使用更简单的`then(x).catch(x)`会更好。*

# 9。避免。然后是地狱

这个提示非常简单，尽量避免在`.then`或`.catch`中使用`.then`。相信我，这比你想象的更容易避免。

**不对**

```
request(opts)
.catch(err => {
  if (err.statusCode === 400) {
    return request(opts)
           .then(r => r.text())
           .catch(err2 => console.error(err2))
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

💖**纠正**

```
request(opts)
.catch(err => {
  if (err.statusCode === 400) {
    return request(opts);
  }
  return Promise.reject(err);
})
.then(r => r.text())
.catch(err => console.erro(err)); 
```

Enter fullscreen mode Exit fullscreen mode

有时，我们确实需要在一个`.then`范围内有多个变量，除了创建另一个`.then`链之外别无选择。

```
.then(myVal => {
    const promA = foo(myVal);
    const promB = anotherPromMake(myVal);
    return promA
          .then(valA => {
              return promB.then(valB => hungryFunc(valA, valB)); // very hungry!
          })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我推荐使用 ES6 破坏力量混合`Promise.all`来救援！

```
.then(myVal => {
    const promA = foo(myVal);
    const promB = anotherPromMake(myVal);
    return Promise.all([prom, anotherProm])
})
.then(([valA, valB]) => {   // putting ES6 destructing to good use
    console.log(valA, valB) // all the resolved values
    return hungryFunc(valA, valB)
}) 
```

Enter fullscreen mode Exit fullscreen mode

*注意:如果你的节点/浏览器/boss/conscious 允许，你也可以使用 [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 来解决这个问题！*

我真的希望这篇文章能帮助你理解承诺。

请查看我以前的博客帖子。

*   Javascript 中的幼儿内存泄漏指南
*   [了解 Javascript 中的默认参数](https://dev.to/kepta/understanding-default-parameters-in-javascript-ali)

如果你❤️这篇文章，请分享这篇文章传播的话。

在 Twitter 上联系我 [@kushan2020](https://twitter.com/kushan2020) 。