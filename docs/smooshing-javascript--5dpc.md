# 平滑 javascript

> 原文：<https://dev.to/kayis/smooshing-javascript--5dpc>

*[封面图片由大卫在 Flickr 上拍摄，由我裁剪。](https://www.flickr.com/photos/davidcharns)T3】*

作为一名 JavaScript 开发人员，你可能听说过 smoosh-gate。一些 [TC39](https://github.com/tc39) 的人(他们负责设计定义 JavaScript 语言行为的 ECMAScript 标准)有了将 `flatten`重命名为`smoosh`、`flatMap`重命名为`smooshMap`的想法。这背后的原因是，如果不使用 MooTools，一些使用 MooTools 的网站就会崩溃。这些方法现在只是一个提议，并不是标准的一部分。

无论如何，我不太了解 MooTools 或标准，但我想解释一下这些功能实际上是做什么的。

## 为什么

大多数时候，在编写 JavaScript 时，你可能正在摆弄你最喜欢的函子、数组和承诺。有时候它们是嵌套的，但是你并不关心这个。您需要一个数字数组，而不是数字数组的数组，或者您需要来自服务器的 JSON 结果，而不是 JSON 结果的承诺。

## 什么

承诺已经有了一个`then`方法，它展平了嵌套的承诺。

```
// loadDataA returns a promise
const dataAPromise = loadDataA();

// dataAPromise resolves to a result with an id that is used to load the next data
// loadDataB returns a promise too
const dataBPromise = dataAPromise.then(result => loadDataB(result.id))

// dataBPromise resolves to the resulting data
// and not the the promise returned from the callback above.
// It's flattened out right away so you only care about the result
const upperCasePromise = dataBPromise.then(result => result.someText.toUpperCase())

// upperCasePromise resolves to the upper case text
// returned by the previous callback.
upperCasePromise.then(upperCaseText => console.log(upperCaseText)); 
```

Enter fullscreen mode Exit fullscreen mode

所以这里没什么可做的。一些有前途的库，像[蓝鸟](http://bluebirdjs.com/docs/getting-started.html)有单独的`map`和`flatMap`方法，但是大多数情况下你会使用`then`并且不太关心这里的扁平化。

数组中这个问题的解决方案是给数组添加一个`flatten`和`flatMap`方法。`flatten`方法用嵌套元素的内容替换数组中的每个嵌套数组，它还删除了*空的*元素。

该功能可以借助`reduce`手动编写。

```
const flatten = a => a.reduce(
  (newArray, element) =>
    element instanceof Array
      ? [...newArray, ...element]
      : element !== undefined? [...newArray, element] : newArray,
  []
);

const a = [1, [2, 3, 4], , 5];
flatten(a); // -> [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

我们通过添加每一个`element`将`a`缩减为`newArray`，如果这个数组是一个`istanceof Array`，我们将那个`element`的每一个元素添加到`newArray`。(`...`-操作符将为这两种情况创建一个新的数组，而不是添加到现有的数组中，但是我想您已经明白了这一点)。

命令式版本可能是这样的:

```
function flatten(a) {
  let b = [];
  for (let element of a) {
    if (element instanceof Array) {
      for (let subElement of element) {
        b.push(subElement);
      }
    } else if (element !== undefined) {
      b.push(element);
    }
  }
  return b;
}

const a = [1, [2, 3, 4], , 5];
flatten(a); // -> [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

这个的`flatMap`版本只是在一个由`map`发出的新数组上调用`flatten`。

```
const flatMap = (f, a) => flatten(a.map(f));

const a = [1,0,7,-3];
flatMap(x => x != 0? 1/x : undefined, a);
// -> [1, 0.14285714285714285, -0.3333333333333333]

const c = [1,2,5];
flatMap(x => ''.padEnd(x, ".").split(""), c);
// -> [".", ".", ".", ".", ".", ".", ".", "."] 
```

Enter fullscreen mode Exit fullscreen mode

真正的实现可能像方法而不是函数一样工作:

```
// Instead of 
flatten(a.map(x => [1,x]));

// it would be
a.map(x => [1,x]).flatten();

// instead of
flatMap(x => [1,x], a);

// it would be
a.flatMap(x => [1,x]); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

展平是每个程序中多次执行的一个相当重要的动作，所以如果 JavaScript 自带一个内置版本就好了，独立于它的最终名称 lol。