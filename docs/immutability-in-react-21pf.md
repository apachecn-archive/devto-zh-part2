# 反应的不变性

> 原文：<https://dev.to/bnevilleoneill/immutability-in-react-21pf>

[![](img/6ceb73dbd1dd8e2747ed2d128826fc78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xtdzgi-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2ATPF5q9zVHp944Ub-xtU7MQ.jpeg) 

<figcaption>[形象功劳](https://www.geeknative.com/39314/mutate-the-t-shirt/)</figcaption>

当您开始使用 React 时，您首先要学习的一件事情是，您不应该改变(修改)列表:

```
// This is bad, push modifies the original array
items.push(newItem);
// This is good, concat doesn’t modify the original array
const newItems = items.concat([newItem]); 
```

但是…

你知道为什么吗？

你知道变异物体有什么问题吗？

[![](img/77b56ea6ec1434afced50127d99232c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QlaId3Pf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/422/0%2A88XOllaZvI-HBP8o.)

好吧，互联网是错误的。变异物体没有错。

是的，在类似并发的情况下，这可能会成为一个问题。但这是最简单的开发方法。正如编程中的许多事情一样，这是一种权衡。

函数式编程和像不变性这样的概念很流行，几乎是“酷”的话题。但是在 React 的例子中，不变性给了你一些真正的好处。不仅仅是时尚。这里有实际的效用。

### 什么是不变性？

不变性意味着某物不能改变其价值或状态。

这是一个简单的概念，但和往常一样，细节决定成败。

您可以在 JavaScript 本身中找到不可变类型。字符串**值类型**就是一个很好的例子。

如果你这样定义一个字符串:

```
var str = 'abc'; 
```

您不能直接更改字符串中的字符。

在 JavaScript 中，字符串不是数组，所以你可以这样做:

```
str[2] = 'd'; 
```

做类似于:
的事情

```
str = 'abd'; 
```

将不同的字符串赋给 str。

你甚至可以将 str 引用定义为一个常量:

```
const str = 'abc' 
```

因此，给一个新字符串赋值会产生一个错误(尽管这与不变性无关)。

如果要修改字符串值，必须使用[替换](https://www.w3schools.com/jsref/jsref_replace.asp)、[翻转](https://www.w3schools.com/jsref/jsref_touppercase.asp)或[修剪](https://www.w3schools.com/jsref/jsref_trim_string.asp)等操纵方法。

所有这些方法都返回新的字符串，它们不会修改原来的字符串。

### 值类型

现在，也许你没有注意到，但是之前我强调了*值类型*这几个字。

字符串值是不可变的。字符串**对象**不是。

如果一个对象是不可变的，你不能改变它的状态(它的属性值)。但这也意味着您不能向对象添加新属性。

试试这个拨弄:
[https://jsfiddle.net/eh3rrera/a6uh7tsv/embedded//dark](https://jsfiddle.net/eh3rrera/a6uh7tsv/embedded//dark)
如果你运行它，你会看到一个警告窗口，消息未定义。

新属性未添加。

但是现在试试这个:
[https://jsfiddle.net/eh3rrera/e46Lsrp7/embedded//dark](https://jsfiddle.net/eh3rrera/e46Lsrp7/embedded//dark)
[![](img/352d8513ce69b93be8f367ec6253de0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SxglwhcT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/302/0%2Af3DODCqLTseJ5h3L.)

字符串**是**不可变的。

最后一个示例使用 String()构造函数创建一个对象，该构造函数包装(不可变的)字符串值。但是您可以向这个包装器添加新的属性，因为它是一个对象，并且没有被冻结。

这让我们明白了一个非常重要的概念。引用和值相等的区别。

### 引用相等 vs 值相等

使用引用相等，您可以用操作符`===`和`!==`(或者`==`和`!=`)来比较对象引用。如果引用指向同一个对象，它们被认为是相等的:

```
var str1 = ‘abc’;
var str2 = str1;
str1 === str2 // true 
```

在上面的例子中，两个引用(str1 和 str2)是相等的，因为它们指向同一个对象(“abc”)。

[![](img/60c9be7d00ab3c95a9a803a401732715.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pouYYZbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/377/0%2AipAtUvsW9QPr3EHO.)

两个引用在引用同一个值时也是相等的，如果这个值是不可变的:

```
var str1 = ‘abc’;
var str2 = ‘abc’;
str1 === str2 // true
var n1 = 1;
var n2 = 1;
n1 === n2 // also true 
```

[![](img/7ea395d9abc2da656c28760268f728aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZdRUUYXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/419/0%2AjE_ls1ixbCHkVH5J.)

但是当谈到对象时，这不再成立:

```
var str1 =  new String(‘abc’);
var str2 = new String(‘abc’);
str1 === str2 // false
var arr1 = [];
var arr2 = [];
arr1 === arr2 // false 
```

在每种情况下，都会创建两个不同的对象，因此它们的引用不相等:

[![](img/d68a12a60daf97f7a4ee9ec16aa7a5ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MP0t5IaG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/387/0%2AQI4r9ERIF1OPVADk.)

如果要检查两个对象是否包含相同的值，必须使用值相等，比较对象的属性值。

在 JavaScript 中，没有直接的方法来实现对象和数组的值相等。

如果使用字符串对象，可以使用 valueOf 或 trim 方法返回字符串值:

```
var str1 =  new String(‘abc’);
var str2 = new String(‘abc’);
str1.valueOf() === str2.valueOf() // true
str1.trim() === str2.trim() // true 
```

但是对于另一种类型的对象，你要么必须[实现你自己的 equals 方法，要么使用第三方库](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html)。

这与不变性和反应有什么关系？

如果两个对象是不可变的，那么测试它们是否相等就更容易了，React 利用这个概念进行了一些性能优化。

我们来谈谈这个。

### React 中的性能优化

React 维护 UI 的内部表示，即所谓的[虚拟 DOM](http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/) 。

当组件的属性或状态发生变化时，这个虚拟 DOM 会更新以反映这些变化。操纵虚拟 DOM 更加容易和快速，因为 UI 中没有任何改变。

然后，React 将虚拟 DOM 与更新前的版本进行比较，以了解发生了什么变化。这就是[对账](https://reactjs.org/docs/reconciliation.html)流程。

这样，在真正的 DOM 中只有改变的元素被更新。

但是有时，DOM 的某些部分会被重新渲染，即使它们没有发生变化，这是其他部分发生变化的副作用。

在这种情况下，您可以实现函数[shouldcomponentdupdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)来检查属性和/或状态是否真的发生了变化，并返回 true 来让 React 执行更新:

```
class MyComponent extends Component {
// ...
shouldComponentUpdate(nextProps, nextState) {
    if (this.props.myProp !== nextProps.color) {
      return true;
    }
    return false;
  }
// ...
} 
```

如果组件的属性和状态是不可变的对象或值，您可以用一个简单的等式操作符检查它们是否改变了。

从这个角度来看，不变性消除了复杂性。

因为有时候，知道什么会改变是非常困难的。

想想深领域:

```
myPackage.sender.address.country.id = 1; 
```

如何有效地跟踪哪个嵌套对象发生了变化？

想想数组。

对于两个大小相同的数组，知道它们是否相等的唯一方法是比较每个元素。大型阵列的高成本操作。

最简单的解决方案是使用不可变对象。

如果对象需要更新，必须创建一个具有新值的新对象，因为原来的对象是不可变的，不能更改。

你可以用引用相等来知道它改变了。

但对于一些人来说，这个概念可能看起来有点不一致，或者与性能和简单性的想法相反。

因此，让我们回顾一下创建新对象和实现不变性的选项。

### 实现不变性

在大多数实际应用程序中，您的状态和属性将是对象和数组。

JavaScript 提供了一些方法来创建它们的新版本。

对于对象，不使用新属性手工创建对象:

```
const modifyShirt = (shirt, newColor, newSize) => {
  return {
    id: shirt.id,
    desc: shirt.desc,
    color: newColor,
    size: newSize
  };
} 
```

您可以使用 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 来避免定义未修改的属性:

```
const modifyShirt = (shirt, newColor, newSize) => {
  return Object.assign( {}, shirt, {
    color: newColor,
    size: newSize
  });
} 
```

Object.assign 会将作为参数传递的对象的所有属性(从第二个参数开始)复制到第一个参数中指定的对象。

或者你可以使用 [spread 操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)达到同样的效果(T2 的区别是 Object.assign()使用 setter 方法来分配新值，而这个操作符没有):

```
const modifyShirt = (shirt, newColor, newSize) => {
  return {
    ...shirt,
    color: newColor,
    size: newSize
  };
} 
```

对于数组，您也可以使用 spread 操作符创建带有新值的数组:

```
const addValue = (arr) => {
  return [...arr, 1];
}; 
```

或者您可以使用 concat 或 slice 之类的方法返回一个新数组，而不修改原来的数组:

```
const addValue = (arr) => {
  return arr.concat([1]);
};
const removeValue = (arr, index) => {
  return arr.slice(0, index)
    .concat(
        arr.slice(index+1)
    );
}; 
```

在这个[要点](https://gist.github.com/JoeNoPhoto/329f002ef4f92f1fcc21280dc2f4aa71)中，您可以看到如何将 spread 操作符与这些方法结合起来，以避免在执行一些常见操作时改变数组。

然而，使用这些本地方法有两个主要缺点:

*   它们通过将属性/元素从一个对象/数组复制到另一个对象/数组来工作。对于大型对象/阵列，这可能是一个缓慢的操作。
*   默认情况下，对象和数组是可变的，没有任何东西强制不变性。你必须记住使用这些方法中的一种。

由于这些原因，最好使用一个处理不变性的外部库。

React 团队推荐 [Immutable.js](https://facebook.github.io/immutable-js/) 和[immutable-helper](https://github.com/kolodny/immutability-helper)，但是[这里](https://github.com/markerikson/redux-ecosystem-links/blob/master/immutable-data.md)你可以找到很多具有类似功能的库。有三种主要类型:

*   使用特殊数据结构的库。
*   通过冻结对象来工作的库。
*   带有助手函数的库来执行不可变的操作。

这些库中的大多数都使用持久数据结构。

### 持久数据结构

持久数据结构在提供对所有版本的访问的同时，每当有东西被修改(这使得数据不可变)时就创建一个新版本。

如果数据结构是部分持久的，则可以访问所有版本，但只能修改最新的版本。如果数据结构是完全持久的，那么每个版本都可以被访问和修改。

基于两个概念，树和共享，新版本的创建以有效的方式实现。

数据结构充当列表或映射，但在幕后，它被实现为一种称为 [trie](https://en.wikipedia.org/wiki/Trie) (特别是一种[位图向量 trie](https://stackoverflow.com/a/29121204/3593852) )的树，其中只有树叶保存值，关键字的二进制表示是树的内部节点。

例如，对于数组:

```
[1, 2, 3, 4, 5] 
```

你可以把索引转换成 4 位二进制数:

```
0: 0000
1: 0001
2: 0010
3: 0011
4: 0100 
```

并将数组表示为一棵树，如下所示:

[![](img/c39ddb78861b97853625194596a09f00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Z8f-p2m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/502/0%2A3hlxKXFBvhgY-Pzk.)

其中每一级都有两个字节来构成到达一个值的路径。

现在，假设您想要将值 1 更新为 6:

[![](img/2cddfd06b966d921e61cb6da9d396f02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UC8LoTn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/501/0%2AYq2TMZjNipslzaQe.)

不是直接更新树中的值，而是复制从根到您正在更改的值之间的节点:

[![](img/54ba99023ee57b78c36063dc0b2320d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YVG0AcC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/740/0%2AL2vypVatx0VywZZS.)

该值在新节点上更新:

[![](img/9394fe49819ac99f7b7a322e5200f66b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HB6ZIxJj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/741/0%2A4TVKbnY7a3av-4Fq.)

其余的节点被重用:

[![](img/34f679d06d4429b3c440847d2aa7ad96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RtsCaY3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/740/0%2AaAJm2raVQKpBjzqM.)

换句话说，未修改的节点由两个版本共享。

当然，这种 4 位分支通常不用于这些数据结构。但是，这是**结构共享**的基本概念。

我不会讲更多的细节，但是如果你想了解更多关于持久数据结构和结构共享的知识，[阅读这篇文章](https://medium.com/@dtinth/immutable-js-persistent-data-structures-and-structural-sharing-6d163fbd73d2)或者[观看这篇演讲](https://www.youtube.com/watch?v=Wo0qiGPSV-s)。

### 缺点

不变性并不是没有自身的问题。

正如我前面提到的，在处理对象和数组时，您要么必须记住使用方法而不是强制不变性，要么使用第三方库。

但是许多这些库使用它们自己的数据类型。

尽管它们提供了兼容的 API 和将这些类型转换为本地 JavaScript 类型的方法，但是在设计应用程序时，您必须小心:

*   避免高度耦合，或者
*   用 [toJs()](https://twitter.com/leeb/status/746733697093668864) 这样的方法伤害性能

如果库不实现新的数据结构(例如，通过冻结对象工作的库)，就不会有任何结构共享的好处。最有可能的是，对象在更新时会被复制，在某些情况下性能会受到影响。

此外，您必须考虑与这些库相关的学习曲线。

因此，在选择用于实施不变性的方法时，您必须小心谨慎。

此外，看看这篇文章中关于不变性的相反观点。

### 结论

不变性是 React 程序员需要理解的一个概念。

不可变的值或对象不能被改变，所以每次更新都会创建新的值，旧的值保持不变。

例如，如果您的应用程序状态是不可变的，您可以将所有状态对象保存在一个存储中，以便轻松实现撤销/重做功能。

听起来熟悉吗？应该的。

像 [Git](https://git-scm.com/) 这样的版本控制系统以类似的方式工作。

[还原](https://redux.js.org/)也是基于那个[原理](https://redux.js.org/introduction/three-principles)。

然而，对 Redux 的关注更多的是在应用程序状态的[纯函数](https://medium.com/@jamesjefferyuk/javascript-what-are-pure-functions-4d4d5392d49c)和*快照*方面。这个 [StackOverflow 答案](https://stackoverflow.com/a/34962065/3593852)很好的解释了 Redux 和不变性的关系。

不变性还有其他优点，比如避免意外的副作用或者减少耦合，但是它也有缺点。

请记住，与编程中的许多事情一样，这是一种权衡。

* * *

### Plug: LogRocket，一款用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

React 中的帖子[不变性:变异对象没有错](https://blog.logrocket.com/immutability-in-react-ebe55253a1cc/)最早出现在[日志博客](https://blog.logrocket.com)上。