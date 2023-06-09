# 我做了一个 JS 模块，所以你现在几乎可以使用管道操作符了！

> 原文：<https://dev.to/nektro/i-made-a-js-module-so-you-can-almost-use-the-pipeline-operator-today-3j70>

我听说你喜欢函数，所以我为你做了一个函数，把你的函数和其他函数连接起来。

ECMAScript 的标准机构 TC39 目前有一个关于第一阶段的管道操作者 T1 的提议，这个提议获得了很大的关注。

假设您有以下函数声明

```
function doubleSay (str) {
    return str + ", " + str;
}
function capitalize (str) {
    return str[0].toUpperCase() + str.substring(1);
}
function exclaim (str) {
    return str + '!';
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你想一起使用这些功能，你可以:

```
let result = exclaim(capitalize(doubleSay("hello"))); 
```

Enter fullscreen mode Exit fullscreen mode

但是它按照我们编写函数的倒序来执行我们所有的函数，所以引入了*管道操作符*！

```
let result = "hello"
  |> doubleSay
  |> capitalize
  |> exclaim; 
```

Enter fullscreen mode Exit fullscreen mode

这样看起来好多了！关于确切的语法等等有很多讨论，但是在阅读这些讨论时，有人提到这实际上已经可以用 JavaScript 实现了！所以我做了一些研究，测试了它，并把它包装起来，这就是:D 的核心成果

```
function pipeline(input, ...methods) {
    const next = methods.shift();
    if (typeof next !== 'function') {
        return input;
    }
    return pipeline(next(input), ...methods);
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！然后用这个我们会做，

```
let result = pipeline(
  "hello",
  doubleSay,
  capitalize,
  exclaim
); 
```

Enter fullscreen mode Exit fullscreen mode

但这是#showdev，所以我做了一个模块，你今天可以使用它！

> 注意:在撰写本文时，对 Node 和 Firefox 的 ES 2015+模块支持还处于试验阶段，因此您的体验可能会因平台而异。欢迎关注 GitHub 上的问题和评论:)

链接:

*   GitHub:[https://github.com/Nektro/js-pipeline](https://github.com/Nektro/js-pipeline)
*   https://www.npmjs.com/package/pipeline-operator
*   https://unpkg.com/pipeline-operator/index.js

* * *

编辑:感谢建议，这现在也可以作为利用`Array.reduce`的一行程序使用了！

```
const pipeline = (input, ...methods) => methods.reduce((ac,cv) => cv(ac), input); 
```

Enter fullscreen mode Exit fullscreen mode