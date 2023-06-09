# 大 O 符号备忘单

> 原文：<https://dev.to/kbariotis/big-o-notation-cheatsheet--717>

*这篇文章是我从这篇[Dionysis " dionyziz " Zindros](https://twitter.com/dionyziz)关于复杂性的优秀文章中摘录的笔记。我强烈推荐给任何对学习算法复杂性感兴趣的人，即使是第一次。如果你已经知道什么是时间复杂度，我希望这篇文章是一个新的提醒。*

可伸缩性可能是当今使用最多的术语之一，每个人都在努力为更多的用户服务。但是，虽然我们可以建立一个基础设施来处理这样的负载，我们也需要编写代码来完成这项工作。为了分析我们的代码并计算出它的效率，我们使用了 Big-O 符号。它还可以帮助我们弄清楚随着负载的增加，我们的代码将会如何表现。最后但同样重要的是，我们使用 Big-O 符号来比较算法。

## 计数指令

一个函数需要许多指令才能运行，并且可能会根据编写语言或运行架构的不同而有所延迟。

```
let element = list[0];

for (let i = 0; i < n; ++i) {
  if (list[i] >= element) {
    element = list[i];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数需要`4 + 2n`指令才能运行，我们可以把它分配给`f(n)`。(你能想出我们是怎么想出这个的吗？)

### 最坏情况分析

对于最坏情况分析术语，我们指的是函数将运行它能运行的所有指令的情况，因此是最坏情况。

### 渐近行为

由于我们需要从总体上检查一个功能，而不是它在特定架构或语言上的性能，我们通过丢弃增长缓慢的术语来简化指令的数量。于是，`f(n) = 4 + 2n`变成了`f(n) = n`。该函数仍以与之前相同的速率增长，并且看起来简单得多。

## 复杂程度

我们将算法最坏情况下的渐近行为称为复杂性，并将其写成`Θ`。所以，函数`f(n) = 4 + 2n`的复杂度是θ(n)，读作 n 的θ。

### 大 O 符号

当我们开始分析复杂函数时，我们会发现有时很难找出最坏情况下的确切指令。我们可以改变算法，让它变得稍微差一点(当然是在我们的脑海中)，这样我们就可以找到一个比实际情况更差的场景。然后，如果我们确定我们得出的复杂性实际上更差，我们可以称之为`O(Θ(n))`读作 n 的θ的大 oh。例如，`O(n^2)`意味着我们的函数可以渐近地表现得不比 n^2.差

### 上限

`O(n^2)`是实际时间复杂度的上界。虽然这是真的，但也可能是实际的时间复杂度。因此，这给了我们一个严格的界限。如果我们确定θ(n)肯定不是`O(n)`而是`O(n^2)`，那么我们可以把它写成`o(n^2)`。所以，`Θ(n)`就是`O(n)`(紧束缚)`o(n^2)`(非紧束缚)。

### 下界

虽然我们已经找到了上限或最坏的情况，但找到最好的情况也是有用的。下界给了我们函数不会比的复杂度。我们使用符号ω，读作 big omega，表示紧的下界，ω表示不紧的下界。

## 对数

对数取一个数，使它变得小得多。同样，平方根是平方一个数的逆运算，对数是指数一个数的逆运算。

某些在每次迭代中分割输入的算法往往具有`O(log(n))`复杂性，而这些算法通常是最有效的。例如快速排序和合并排序算法。

## 例子

### O①

```
function getLast (items) {
  return items[items.length-1]; 
}; 
```

Enter fullscreen mode Exit fullscreen mode

### (n)O

```
function findIndex (items, match) {
  for (let i = 0, total = items.length; i < total; i++) {
     if (items[i] == match) {
        return i;
     }
  }

  return -1;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 【o(n^2)

```
function buildSquareMatrix (items) {
  let matrix= [];

  for (let i = 0, total = items.length; i < total; i++) { 
    matrix[i] = [];

    for (let j = 0, total = items.length; j < total; j++) {
      matrix[i].push(items[j]);
    }
  }
  return matrix;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### O(log(n))

```
 function quickSort (list) {
  if ( list.length < 2) {
    return list;
  }

  let pivot = list[0];
  let left = []; 
  let right = [];

  for (let i = 1, total = list.length; i < total; i++ ){
    switch (true){
      case (list[i] < pivot):
        left.push( list[i] );
        break;
      case (list[i] >= pivot):
        if( list[i] ) {
          right.push( list[i] );
        }
        break;
    }
  }

  return [].concat(quickSort(left), pivot, quickSort(right));
}; 
```

Enter fullscreen mode Exit fullscreen mode