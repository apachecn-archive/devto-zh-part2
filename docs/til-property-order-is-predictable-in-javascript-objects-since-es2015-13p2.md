# TIL:自 ES2015 以来，JavaScript 对象中的属性顺序是可预测的

> 原文：<https://dev.to/stefanjudis/til-property-order-is-predictable-in-javascript-objects-since-es2015-13p2>

今天的学习让我大吃一惊！

我在看推特，看到了一个由塞巴斯蒂安·麦肯齐发起的帖子。在这个帖子中，他分享了一个 React 片段，该片段依赖于对象中特定属性的顺序。

```
styles({
  backgroundBlue: true,
  backgroundRed: true
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，背景是红色的，因为“它最后出现”。如果你做过一段时间的 JavaScript，你可能会立刻想到——**等等，什么**！？！

在本帖中，我主要分享片段和事实，因为我不想重复最后列出的资源内容。

## 常见的误解——“无法保证 JavaScript 属性的顺序”

几年前开始编写 JavaScript 时，您可能听说过 JS 对象中属性的顺序是不可预测的。我从未遇到过奇怪和不寻常的属性顺序，但我总是遵循“从不依赖属性顺序”的规则。

### 内功`ownPropertyKeys`心法

事实证明，自 ES2015 以来，有一些方法基于特定的规则来定义属性的顺序，除了一个特定的情况，顺序是按时间顺序排列的。对象中属性的顺序取决于所包含属性的类型及其值。

查看规范，规则是在[内部“ownPropertyKeys”方法](http://www.ecma-international.org/ecma-262/6.0/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys)中定义的。例如由相当新的方法`Object.getOwnPropertyNames`和`Reflect.ownKeys`使用。

有趣的是，有一个规格变化，例如从 [ES5](https://www.ecma-international.org/ecma-262/5.1/#sec-15.2.3.14) 到 [ES6](http://www.ecma-international.org/ecma-262/6.0/#sec-object.keys) 的`Object.keys`。ES6 规范定义了`Object.keys`也依赖于`ownPropertyKeys`，这使得它在今天的浏览器中也是可预测的。

这也意味着你必须小心使用这种方法，不应该依赖使用`Object.keys`的可预测顺序，因为结果[可能根据浏览器实现](https://www.reddit.com/r/javascript/comments/9ozyn3/property_order_is_predictable_in_javascript/e7yhm3g/)而变化。

理论已经讲得够多了:让我们看看为实现`ownPropertyKeys`的方法定义的属性顺序。

#### 1。整数索引

所有为整数索引的属性在整个对象属性顺序中首先出现，并按数字排序。

```
const objWithIndices = {
  23: 23,
  '1': 1,
  1000: 1000
};

console.log(Reflect.ownKeys(objWithIndices));
// [1, 23, 1000]
// ☝️ following numeric order 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。字符串(非整数)

不计入整数索引且不属于类型
`Symbol`的属性排在后面，并遵循时间顺序。

```
const objWithStrings = {
  'bar': 'bar',
  '01': '01'
};

objWithStrings.last = 'last';
objWithStrings['veryLast'] = 'veryLast';

console.log(Reflect.ownKeys(objWithStrings));
// ['bar', '01', 'last', 'veryLast']
// ☝️ following chronological order 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。标志

最后，符号也遵循时间顺序。

```
const objWithSymbols = {
  [Symbol('first')]: 'first',
  [Symbol('second')]: 'second'
};

objWithSymbols[Symbol('last')] = 'last';

console.log(Reflect.ownKeys(objWithSymbols));
// [Symbol(first), Symbol(second), Symbol(last)]
// ☝️ following chronological order 
```

Enter fullscreen mode Exit fullscreen mode

## 都在一起

当您组合这些规则时，您会看到整数总是在对象属性的“前排”，后面是字符串和符号。此外，我们可以控制字符串和符号属性的顺序，因为它们是按时间顺序排列的！

```
const obj = {
  '2': 'integer: 2',
  'foo': 'string: foo',
  '01': 'string: 01',
  1: 'integer: 1',
  [Symbol('first')]: 'symbol: first'
};

obj['0'] = '0';
obj[Symbol('last')] = 'symbol: last';
obj['veryLast'] = 'string: very last';

console.log(Reflect.ownKeys(obj));
// [ "0", "1", "2", "foo", "01", "veryLast", Symbol(first), Symbol(last) ]
// -> 1\. integers in numeric order
// -> 2\. strings in chronological order
// -> 3\. Symbols in chronological order 
```

Enter fullscreen mode Exit fullscreen mode

*编辑:正如 Malgosia Stepniak 指出的,[只有现代浏览器才完全支持“自有财产秩序”,例如 IE](https://twitter.com/malgosiastp/status/1052539495453773824) 就不支持。*

## 附加资源

感谢阿克塞尔，他已经在三年前写了这篇文章。:)

*   [由](http://2ality.com/2015/10/property-traversal-order-es6.html#traversing-the-own-keys-of-an-object)[阿克塞尔·劳施迈尔](https://twitter.com/rauschma)在 ES6 中遍历对象属性的顺序
*   [ECMAScript 2019 语言规范- OrdinaryOwnPropertyKeys](https://tc39.github.io/ecma262/#sec-ordinaryownpropertykeys)
*   [“ES6 是否为对象属性引入了明确定义的枚举顺序？”](https://stackoverflow.com/questions/30076219/does-es6-introduce-a-well-defined-order-of-enumeration-for-object-properties)堆栈溢出时