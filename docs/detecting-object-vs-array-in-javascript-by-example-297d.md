# 通过示例检测 JavaScript 中的对象与数组

> 原文：<https://dev.to/hugo__df/detecting-object-vs-array-in-javascript-by-example-297d>

假设我们要测量具有混合嵌套数组/对象的对象的深度，如下所示:

```
const obj = {
  myKey: {
    nest: {
      doubleNested: 'value',
      nestedArray: [ { key: 'value' } ]
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

难点在于检测我们应该把值当作对象(字典)还是当作列表。

我们应该能够用下面的代码做到这一点:

```
function maxDepth(obj, depth = 0) {
  if (typeof obj !== 'object') {
    return depth;
  }
  const [values, depthIncrease] = Array.isArray(obj)
    ? [obj, 0]
    : [Object.values(obj), 1];
  return values.length > 0
    ? Math.max(...values.map(
      value => maxDepth(value, depth + depthIncrease))
    )
    : depth;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Some of these fail even although
// the assertions hold 🙄
console.assert(maxDepth({}), 0);
console.assert(maxDepth(''), 0);
console.assert(maxDepth([ { one: 'deep' } ]), 1);
console.assert(maxDepth({ one: 'deep' }), 1);
console.assert(maxDepth({ one: [ { two: 'deep' } ] }), 2)
console.assert(maxDepth({ one: { two: 'deep' } }), 2) 
```

Enter fullscreen mode Exit fullscreen mode

为了分解对象和基本类型检测，这是一个`typeof obj === 'object'`的例子，请看这个关于事物类型的快速提示:

```
console.assert(typeof '', 'string');
console.assert(typeof new String(), 'string');
console.assert(typeof 1, 'number');
console.assert(typeof Infinity, 'number');
console.assert(typeof NaN, 'number');
console.assert(typeof undefined, 'undefined');

console.assert(typeof [], 'object');
console.assert(typeof null, 'object');
console.assert(typeof {}, 'object');
console.assert(typeof new Map(), 'object');
console.assert(typeof new Set(), 'object'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了区分对象和数组，每天都是`Array.isArray`，虽然我们可以使用对`.length`的检查，但是还有一点需要注意的是，`Set`或`Map`在函数
中传递

```
// Console.assert flips out again
// even though the assertions hold
console.assert(Array.isArray({}), false);
console.assert(Array.isArray(new Map()), false);
console.assert(Array.isArray(new Set()), false);

console.assert(Array.isArray([]), true);
console.assert(Array.isArray(new Array()), true); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用`.length > 0`，尽管它将检查一个**非空的**数组，或`.length != null`，这是`!=` / `==`的一个很好的用例，但是我们还是远离它，以免有人把它改成`!==` / `===`。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。