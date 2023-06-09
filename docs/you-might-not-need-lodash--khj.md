# 你可能不需要洛达什

> 原文：<https://dev.to/antjanus/you-might-not-need-lodash--khj>

你可以在我的主博客上查看更多我的教程和文章。享受这篇文章吧！

虽然 Lodash 可能已经成为任何 JavaScript 开发人员工具包中的一个主要部分，但是其中的许多方法已经慢慢成为 JavaScript 本身的一部分，或者说是 EcmaScript 规范的一部分。

Lodash 并不大，事实上，它很轻，适当进口和树摇，它的大小可以忽略不计，但如果你可能不需要它，为什么要这么麻烦呢？

这里收集了我最喜欢的 Lodash 方法，以及如何用 ES2015+原生方法替换它们。有时候替代是 1 比 1，有时候不是。我一定会记下来的

**注:** Lodash 方法倾向于超级短甜。如果你从未浏览过开源代码库，我强烈推荐 [Lodash 的 github repo](https://github.com/lodash/lodash)

### _。to array:object . values+array . from

简单地说，你正在把某个东西转换成一个数组。最常见的是，我用这种方法来转换一个查找对象，比如:

```
const postAuthors = {
  'Antonin Januska': { id: 1, name: 'Antonin Januska', role: 'author' },
  'JK Rowling': { id: 2, name: 'JK Rowling', role: 'real author' },
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了显示的目的。现在，我可以使用这个方法:

```
const postAuthorsArray = Object.values(postAuthors);

/** result:
[
  { id: 1, name: 'Antonin Januska', role: 'author' },
  { id: 2, name: 'JK Rowling', role: 'real author' }
]
**/ 
```

Enter fullscreen mode Exit fullscreen mode

查找对象对于创建唯一列表、聚合数据以及查找东西来说非常方便。通常情况下，该对象必须被转换成数组以用于其他用途。

那`Array.from`呢？嗯，`_.toArray`支持将其他变量类型转换成数组，不仅仅是对象。对那些人来说，`Array.from`更有意义。下面是一些例子:

```
const dnaStrand = 'gattaca';
const dnaArray = Array.from(dnaStrand); // results in ['g', 'a', 't', 't', 'a', 'c', 'a'];

const someNumber = 3;
const result = Array.from(someNumber); // results in []. Not sure what this is used for but lodash supports this 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，一对一的平价到此结束。Array.from 和 Object.values 都不支持将 nulls 转换为空数组。

### _。克隆:对象/阵列分布

克隆一个对象或数组非常有用。在这两种情况下，操作结果意味着不会影响源数据。它还可以用来基于某个模板创建新的对象/数组。

JavaScript 没有 deepClone 的快捷方式，所以要小心，嵌套的对象不会被克隆，引用会被保留。此外，克隆对象数组使得数组可以安全地被操作，而不是对象本身。

有几种方法可以达到同样的效果，我将坚持使用对象/数组展开:

```
const clonedObject = { ...sourceObject };
const clonedArray = [ ...sourceArray ]; 
```

Enter fullscreen mode Exit fullscreen mode

与 lodash 不同，利用 JavaScript 的内置方法需要您知道它们的类型。你不能将一个对象分散到一个数组中，反之亦然来实现克隆。

### *。赋值/* 。扩展:Object.assign

赋值/扩展允许你将一个对象“合并”到另一个对象中，覆盖其原始属性(注意:这与`_.merge`不同，它有一些注意事项)。我其实一直都在用这个！

要在没有 lodash 的情况下实现这一点，您可以使用 lodash 文档甚至引用的`Object.assign`。

```
const sourceObject = { id: 1, author: 'Antonin Januska' };

Object.assign(sourceObject, {
  posts: [],
  topComments: [],
  bio: 'A very cool person',
});

/** result:
{
  id: 1,
  author: 'Antonin Januska',
  posts: [],
  topComments: [],
  bio: 'A very cool person',
}

note: this is still sourceObject
**/ 
```

Enter fullscreen mode Exit fullscreen mode

Object.assign 将使用第二个(第三个，第四个，等等)填充。)参数填入`sourceObject`。

如果希望结果是一个新对象，并且保持不变性呢？很简单，只需指定一个空对象作为第一个参数！

```
const sourceObject = { id: 1, author: 'Antonin Januska' };

const finalObject = Object.assign({}, sourceObject, {
  posts: [],
  topComments: [],
  bio: 'A very cool person',
});

// note: sourceObject is a separate object from finalObject in this scenario 
```

Enter fullscreen mode Exit fullscreen mode

事实上，在对象传播之前，您只需使用`Object.assign({}, whateverObject)`做一个浅层克隆。

### 加成:_。展平:Array.smoosh

Flatten 被认为是 EcmaScript 的一部分，但是由于各种各样的问题，出现了一个(笑话？)提名将其改名`smoosh`。对此我有自己的想法，但是希望不久之后，你能够在你喜欢的深度嵌套数组上使用`Array.smoosh`。

那么 flatten/smoosh 是做什么的呢？它接受一个数组的数组，并把它变成一个单独的数组。假设某个 API 查看了你的 Twitter 列表，并从每个列表中挑选了最好的 tweets，你想把它们组合成一个自己的 feed，你可以使用 flatten 来完成这个任务:

```
const sourceArray = [
  [ 'tweet 1', 'tweet 2', 'tweet 3'],
  [ 'tweet 4', 'tweet 5'],
  [ 'tweet 6', 'tweet 7', 'tweet 8', 'tweet 9']
];

const feed = Array.smoosh(sourceArray);

/** result:
[ 'tweet 1', 'tweet 2', 'tweet 3', 'tweet 4', 'tweet 5', 'tweet 6', 'tweet 7', 'tweet 8 ', 'tweet 9' ];
**/ 
```

Enter fullscreen mode Exit fullscreen mode