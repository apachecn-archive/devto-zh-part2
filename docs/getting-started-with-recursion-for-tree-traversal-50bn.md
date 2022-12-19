# 树遍历递归入门

> 原文：<https://dev.to/bnevilleoneill/getting-started-with-recursion-for-tree-traversal-50bn>

**作者:[阿尔伯塔·威廉姆斯](https://blog.logrocket.com/author/albertawilliams/)** ✏️

你有没有遇到过你觉得可以用递归解决的问题，只是你不知道从哪里开始？还是看起来你不得不自己想办法解决问题？

处理递归的第一部分是理解问题何时需要递归。当问题可以建模为递归关系时，可以使用递归。递归关系是从以前的值中寻找未来值的规则。斐波那契数列是递归关系的一个例子。当数据被递归定义时，也可以使用递归。文件系统可以递归定义，因为每个目录都由其他目录组成。

第二部分是理解如何实现递归函数。在这篇文章中，我将向您展示使用递归来遍历递归数据结构的技术。

### 在树中查找项目

递归数据结构类似于树。在代码中，这转化为数组的数组或其键是其他对象的对象。我们的案例研究将是一棵树，它模拟了纽约市的社区。树根是纽约。它有两个孩子，曼哈顿和布鲁克林。而曼哈顿有两个孩子，哈莱姆区和上东区。

这是我们的树的列表表示:

```
const locations = [
  'New York', 
  [
    'Manhattan',
    [
      'Harlem', 'Upper East Side'
    ]
  ],
  [
    'Brooklyn'
  ]
]; 
```

Enter fullscreen mode Exit fullscreen mode

我们将实现一个函数`includes`，来测试我们的列表是否包含指定的条目。如果找到匹配，该函数将返回 true，否则返回 false。

这个功能有三个部分。首先，基本情况。我们的函数将在每一步减少列表，直到我们得到一个没有元素的列表。接下来，我们来看单个节点的情况。节点应该是字符串“Manhattan”。最后，当元素是另一个列表或子树时。列表`[‘Harlem’, ‘Upper East Side’]`是一个子树。

这是这三种情况的框架:

```
function includes(item, list) {
  if (isEmpty(list)) {
    ...
  } else if(isNode(first(list))) {
    ...
  } else {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果列表中没有元素，`isEmpty`函数将返回`true`。如果遍历了列表中的所有元素，但没有找到匹配，那么函数返回`false`。`first`函数返回列表中的第一个元素。如果元素是一个列表，`isNode`函数返回`false`。

在`else if`中，您想要测试当前元素是否匹配您正在搜索的项目。如果是，可以返回 true。如果不是，你需要在列表的其余部分重现。

[![](img/184dfcc3649f17c50808cfdfb9409f6a.png)T2】](https://logrocket.com/?cid=banner_b)

这是更新后的代码:

```
function includes(item, list) {
  if (isEmpty(list)) {
    return false;
  } else if(isNode(first(list))) {
    if(first(list) == item) {
      return true;
    } else {
      return includes(item, rest(list));
    }
  } else {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`rest`函数返回没有第一个元素的列表。这就是我们如何减少问题，以达到基本情况，一个空列表。条件语句的`else if`块也可以写成:

```
return first(list) == item || includes(item, rest(list)); 
```

Enter fullscreen mode Exit fullscreen mode

它做同样的工作，但更简洁。比起嵌套的`if`语句，我更喜欢这行代码。

最后，在`else`块中，我们需要在第一个元素上递归，因为它是一个列表，并在列表的其余部分上递归。这是`else`区块的代码:

```
return includes(item, first(list)) || includes(item, rest(list)); 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，您现在拥有:

```
function includes(item, list) {
  if (isEmpty(list)) {
    return false;
  } else if(isNode(first(list))) {
    return first(list) == item || includes(item, rest(list));
  } else {
    return includes(item, first(list)) || includes(item, rest(list));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 从树中删除项目

接下来，我们将实现一个函数`remove`,该函数将一个字符串和一个列表作为输入，并返回删除了该字符串的所有匹配项的列表。在真实的树中，您可能会对删除一个节点及其所有子节点感兴趣。为简单起见，我们将只查看删除单个项目的情况。

从列表中删除一个条目类似于查找它的成员，除了我们需要确保当我们重复它的子条目时，我们保留了对列表的引用。

三种情况将是相同的:

```
function remove(item, list) {
  if (isEmpty(list)) {
    ...
  } else if (isNode(first(list))) {
    ...
  } else {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这个函数返回一个列表，我们的基本情况将返回一个空数组。将通过复制列表中除了要移除的项目之外的所有项目来构建新列表。

如果我们使用 for 循环从一维列表中删除一个条目，函数可能是这样的:

```
function remove(item, list) {
  let result = [];
  for (let i = 0; i < list.length; i++) {
    if (list[i] != item){
      result.push(list[i]);
    }
  }
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于递归实现，测试在`else if`块中进行。如果当前元素等于该项，我们在列表的其余部分循环。这具有移除项目的效果。但是，如果当前元素不是条目，那么我们必须保存这一部分，以便连接到列表的其余部分。当函数到达基本情况时，所有被推迟的连接都将被添加到这个列表中。

```
function remove(item, list) {
  if (isEmpty(list)) {
    return [];
  } else if (isNode(first(list))) {
    if (first(list) == item) {
      return remove(item, rest(list));
    } else {
      return concat(first(list), remove(item, rest(list)));
    }
  } else {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`concat`函数将两个输入合并成一个列表。

在`else`块中，我们定义了当前元素是一个列表的情况。我们需要在那部分和列表的其余部分重复出现。此外，这两个部分需要连接成一个列表。这是我们最后得到的:

```
function remove(item, list) {
  if (isEmpty(list)) {
    return [];
    } else if (isNode(first(list))) {
    if (first(list) == item) {
      return remove(item, rest(list));
    } else {
      return concat(first(list), remove(item, rest(list)));
    }
  } else {
    return concat(remove(item, first(list)), remove(item, rest(list)));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 运动

实现一个函数`occur`，它接受一个字符串和一个列表作为输入，并返回该字符串在列表中出现的次数。首先，建立你的三个案例。在你的基本情况下，你应该返回什么？当你有一个节点时，你应该做什么？当你有一个清单时，你应该怎么做？使用前面的两个例子作为指导。

### 结论

用于查找和删除项目的技术可以扩展到解决许多其他需要遍历树的问题。树可以用来模拟游戏中的移动或者表演二分搜索法。实现递归函数时，请记住以下几点:

*   定义基本情况
*   定义元素是节点的情况
*   定义元素是列表的情况
*   在递归调用中，更改参数以使函数达到基本情况

另一个需要考虑的问题是，递归并不总是解决问题的最有效的方法。这就是为什么你应该记住任何可以用递归解决的问题也可以用`for`和`while`循环来解决。当拥有一个更简单的解决方案的好处超过效率的代价时，你会选择递归而不是循环。

最后，这里展示的例子只是解决这类问题的一种方法。使用它们作为起点，并阅读下面列出的资源以获得更深入的理解。

#### 进一步阅读

*   [用 JavaScript 理解递归](https://code.tutsplus.com/tutorials/understanding-recursion-with-javascript--cms-30346)
*   [小阴谋家](https://www.amazon.com/Little-Schemer-Daniel-P-Friedman/dp/0262560992)
*   [离散数学及其应用:第五章归纳与递归](https://www.amazon.com/Discrete-Mathematics-Its-Applications-Seventh/dp/0073383090)
*   [计算机程序的结构和解释:第 1.2 章程序及其生成的过程](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book.html)
*   [哥德尔、埃舍尔、巴赫:一条永恒的金色辫子:第五章递归结构与过程](https://www.amazon.com/G%C3%B6del-Escher-Bach-Eternal-Golden/dp/0465026567) * * * **编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[开始使用递归遍历树](https://blog.logrocket.com/getting-started-with-recursion-for-tree-traversal-9a89fe6e772/)首先出现在[博客](https://blog.logrocket.com)上。