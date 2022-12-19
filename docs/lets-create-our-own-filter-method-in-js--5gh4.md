# 让我们在 JS 中创建我们自己的过滤方法

> 原文：<https://dev.to/machy44/lets-create-our-own-filter-method-in-js--5gh4>

JS 是一种基于原型的语言，这意味着如果我们创建一个数组变量，它的原型是 Array.prototype。每个数组都继承了 Array.prototype 的方法。我们将在后续的文本中看到事情的进展。

在这篇文章中，我将尝试展示 JS 中的方法是如何工作的。我们将重点讨论过滤方法。在[开发者 mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 上，你可以看到 filter 方法是如何被调用的，以及它是如何过滤数组的。

## 试着理解事物

出于学习目的，我们将制作自己的过滤函数，看看 filter method 实际上是如何工作的，以及为什么我们可以像在 JS 中那样调用数组上的方法。

```
var returnedArr = [1,2,3,4,5,6].mfilter(function(element, index, arr) {
  return element > 5;
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们可以看到声明了 *returnedArr* 变量。在*int*的数组上，我们调用 *mfilter* 方法，并且我们正在传递带有*元素*、*索引*和*数组*参数的函数。在函数体中，我们希望返回大于 5 的元素。

为了定义 *mfilter* ，我们必须在*array . prototype*上声明该方法。(否则 js 解释器会打印出*类型错误*，告诉我们 *mfilter* 不是一个函数，因为在*数组中找不到它*

```
Array.prototype.mfilter =  function (fun) {
  var filtered = []; 
  console.log(this);//output: [1,2,3,4,5,6]
  console.log(fun);
    // output:
    //  function (element, index, arr) {
    //    return element > 5;
    //  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将函数发送到 *Array.prototype.mfilter* ，我们必须将该函数作为参数接收。如果我们在 *mfilter* 中控制台. log *这个*关键字，我们可以看到*这个*有一个数组值，我们在这个数组上调用了 *mfilter* 。

同样，如果我们 console.log *fun* 我们可以看到我们已经得到了作为参数发送给 *mfilter* 的函数。

下一步是循环通过*这个*并返回一个大于 5 的值。

```
Array.prototype.mfilter =  function (fun) {
  var filtered = []; 
  for(let i = 0; i < this.length; i++) {
    if (fun(this[i], i, this)) filtered.push(this[i]);
  }
  return filtered;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在 for 循环中，我们循环通过*这个*。如果 *fun* 返回 true(元素大于 6)，该元素将被推入*过滤后的*变量中。在 for 循环之后，我们返回过滤后的变量。

最后，如果我们 console.log *返回一个 Arr* ，它将输出一个值为 6 的数组。

```
console.log(returnedArr); // output: [6] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个地方的全部代码。

```
Array.prototype.mfilter =  function (fun) {
  var filtered = [];
  for(let i = 0; i < this.length; i++) {
    if (fun(this[i], i, this)) filtered.push(this[i]);
  }
  return filtered;
};

var returnedArr = [1,2,3,4,5,6].mfilter(function(element, index, arr) {
  return element > 5;
});

console.log(returnedArr); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

总是试图理解事物在表面下是如何运作的，这将是一件好事。我希望这篇文章能帮助人们更好地理解 JS filter 方法是如何工作的。如果有人有问题或者想讨论帖子里的内容，我很乐意回答。

附:这是我的第一篇文章，写些连贯的东西真的很难，乌夫·:D