# 野兽就是 Array.prototype.reduce

> 原文：<https://dev.to/akshendra/the-beast-that-is-arrayprototypereduce-3a3i>

当谈到 JavaScript 中的函数式编程时,`reduce()`是一种绝对的方法。你用得越多，你就越会看到到处都是它的用例。

我最近意识到，它已经成为我的 goto 方法，每当我必须处理数组时。所以我浏览了我的代码，找到了很多例子，其中一些我会在这篇文章中列出。但在此之前，让我们先简短回顾一下这个方法本身。

### 签名

```
arr.reduce((acc, current, index, array) => {
  // work goes here
}, initial); 
```

Enter fullscreen mode Exit fullscreen mode

`reduce()`需要两个参数。

*   一个`callback`函数，会是第一个。`reduce()`将遍历数组的每个元素，并向`callback`传递以下值。
    *   或者累加器，这个值类似于状态，在每次调用时更新以跟踪结果
    *   对于第一次调用，它等于作为第二个参数提供的`initial`值。
    *   并且在后续的调用中，`acc`将是前一个`callback`调用返回的值。
    *   `current`，我们正在处理的数组的元素。
    *   `index`，数组的当前索引
    *   `array`，阵列本身
*   第二个参数是`initial`，`acc`的第一个值。这是可选的，如果没有提供，`acc`将是数组的第一个元素。

### 简单例子

`reduce()`一个很常见的例子是计算一个整数数组的和。

```
[1, 2, 3, 4, 5].reduce((sum, integer) => sum + integer, 0); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们不需要`index`和`array`，这是一个带有`reduce()`的一般情况。而`sum`、`integer`和`0`分别扮演`acc`、`current`和`initial`的角色。

### 现在一些实际例子

我在上面提到过，我检查了我的一些代码来寻找`reduce()`的例子。我在下面列出了其中的一些，它们与众不同，足以代表一个新的用例。

> 为了保持简短，我从这些例子中删减了项目特定的代码

#### 1。化简为布尔值

我有一个文件路径(`id`)，我想知道该路径是否属于`watching`数组中的任何目录或文件。

```
return watching.reduce((acc, curr) => {
  return acc || id.startsWith(path.join(__dirname, curr));
}, false); 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。使用对象的特定属性/键将对象数组转换为映射

我有一个从数据库接收的对象数组。但是我想把它们转换成一个简单的地图，以便以后处理。所有这些对象都有一个通用的结构和一个存储唯一标识符的键(主键)。

数据示例，

```
// docs array
const docs = [{
  id: 'id-1',
  name: 'K Dilkington',
  style: 'orange',
}, {
  id: 'id-2',
  name: 'Lanky Fellow',
  style: 'googly',
}];

// result
const result = {
  'id-1': {
    id: 'id-1',
    name: 'K Dilkington',
    style: 'orange',
  },
  'id-2': {
    id: 'id-2',
    name: 'Lanky Fellow',
    style: 'googly',
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
function makeMap(docs, key) {
  return docs.reduce((map, doc) => {
    map[doc[key]] = doc;
    return map;
  }, {});
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以使用`makeMap(docs, 'id')`调用这个函数，来构建我们想要的地图。

#### 3。展平数组的数组

一个很普通的案例。我有一个数组的数组，我想把它们组合成一个数组。

```
function flatten(arr) {
  return arr.reduce((acc, current) => {
    return acc.concat(current);
  }, []);
}

flatten([['1', '2'], ['3', 4], [{}, []]]) // => [ '1', '2', '3', 4, {}, [] ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。做`filter()`的工作——完全没有必要:)

从一组玩家中，筛选出 id 有效的玩家。

```
game.players.reduce((acc, val) => {
  if (is.existy(val.mongoId)) {
    acc.push(val.mongoId);
  }
  return acc;
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。低沉的`Object.assign`

`Object.assign`将值从源对象复制到给定对象，但它做了一个浅层复制，也改变了给定对象。

我想要一个函数(`deepAssign`)，它可以进行深度复制，并且不会改变给定的对象。

```
const source = {
  l1: {
    inside: true,
    prop: 'in',
  },
  prop: 'value',
};
const target = {
  prop: 'out',
  l1: {
    prop: 'inisde',
  },
}

const shallow = Object.assign(source, target);
/*
shallow = {
  "l1": {
    "prop": "inisde"
  },
  "prop": "out"
}
*/

const deep = deepAssign(source, target);
/*
deep = {
  "l1": {
    "inside":true,
    "prop": "inisde"
  },
  "prop": "out"
} 
```

Enter fullscreen mode Exit fullscreen mode

```
function deepAssign(object, update, level = 0) {
  if (level > 5) {
    throw new Error('Deep Assign going beyound five levels');
  }

  return Object.keys(update).reduce((acc, key) => {
    const updatewith = update[key];
    if (is.not.existy(updatewith)) {
      return acc;
    }

    // lets just suppose `is` exists
    if (is.object(updatewith) && is.not.array(updatewith)) {
      acc[key] = deepAssign(object[key], updatewith, level + 1);
      return acc;
    }

    acc[key] = updatewith;
    return acc;
  }, Object.assign({}, object));
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用递归，不想杀死`stack`，因此简单检查一下我们应该关心源对象内部有多少层。

#### 6。连锁承诺

我有四个必须串行执行的异步函数，将前一个函数的结果输入下一个函数。

```
const arr = [fetchData, updateData, postData, showData];
const response = arr.reduce((acc, current) => {
  // (cue alarm sirens) no error handling
  return acc.then(current));
}, Promise.resolve(userId));

response.then(data => {
  // data is final response
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 就是这样乡亲们。

我又找到了几个例子，不过它们或多或少都遵循着相同的故事情节，只是有一两处自己的变化。

最后，感谢您的阅读，如果您对`reduce()`有任何神奇的使用案例，或者如果我在这篇文章中犯了任何错误，我很想知道。