# 为了乐趣和利润而扩展 JSON

> 原文：<https://dev.to/krofdrakula/extending-json-for-fun-and-profit-3cg3>

*标题图片由[钢铁公司捏造](http://www.steelsfudge.com/)T3】*

在 JavaScript 的早期，当异步请求首次使 web 作者能够向 HTTP 服务器发出请求并接收可读的响应时，每个人都使用 XML 作为数据交换的标准。这方面的问题通常是解析；您必须有一个强大的解析器和序列化器来安全地与服务器通信。

随着道格拉斯·克洛克福特引入 JSON 作为 JavaScript 语言的静态子集，只允许字符串、数字和数组作为值，对象被简化为键和值集合，这种情况发生了变化。这使得这种格式在提供安全性的同时也很健壮，因为不像 [JSONP](https://en.wikipedia.org/wiki/JSONP) ，它不允许你定义任何可执行代码。

Web 作者喜欢它[ *引文需要* ]，API 开发者接受它，很快，标准化将`JSON` API 纳入 web 标准。

## 解析`JSON`

`parse`方法只有两个参数:代表一个`JSON`值的字符串和一个可选的`reviver`函数。

对于解析，您可能只使用了第一个参数来解析函数，这就很好了:

`const json = '{"hello": "world"}'; const value = JSON.parse(json);`

但是这个论点到底有什么作用呢？

按照 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#Using_the_reviver_parameter) 的说法，`reviver`是一个函数，在解析过程中，每个键和值都会被传递给它，并且它会返回那个键的替换值。这使您有机会将任何值替换为任何其他值，如对象的实例。

让我们创建一个例子来说明这一点。假设您有一队想要连接的无人机，API 会为每架无人机响应一组配置对象。让我们先来看看`Drone`类:

`const _name = Symbol('name'); const _config = Symbol('config'); class Drone { constructor(name, config) { Object.defineProperties( this, { [_name]: { value: name, configurable: false, enumerable: false }, [_config]: { value: config, configurable: false, enumerable: false } } ); } get name() { return this[_name]; } } const d = new Drone('George Droney', { id: 1 });`

为了简单起见，这个类所做的就是提供`name`属性。定义的符号是为了对公共消费者隐藏私有成员。让我们看看是否可以创建一个工厂函数，将配置转换为实际的对象。

我们假想的 API 服务器用下面的 JSON 对象响应:

```
[  {  "$type":  "Drone",  "args":  ["George Droney",  {  "id":  "1"  }  ]  },  {  "$type":  "Drone",  "args":  ["Kleintank",  {  "id":  "2"  }  ]  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们希望通过将参数传递给适当对象类型的构造函数，将每个具有`$type`属性的条目转化为一个实例。我们希望结果等于:

```
const drones = [
  new Drone('George Droney', { id: '1' }),
  new Drone('Kleintank', { id: '2' })
] 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们编写一个`reviver`，它将寻找包含等于`"Drone"`的`$type`属性的值，并返回对象实例。

`const _name = Symbol('name'); const _config = Symbol('config'); class Drone { constructor(name, config) { Object.defineProperties( this, { [_name]: { value: name, configurable: false, enumerable: false }, [_config]: { value: config, configurable: false, enumerable: false } } ); } get name() { return this[_name]; } } const jsonData = [ '[', ' { "$type": "Drone", "args": ["George Droney", { "id": "1" } ] },', ' { "$type": "Drone", "args": ["Kleintank", { "id": "2" } ] }', ']' ].join('\n');` `const reviver = (key, value) => { switch(value.$type) { case 'Drone': { return new Drone(...value.args); } default: { return value; } } }; const drones = JSON.parse(jsonData, reviver);`

`reviver`函数的好处在于，解析 JSON 对象中的每个键时都会调用它，不管值有多深。这允许相同的`reviver`在不同形状的传入 JSON 数据上运行，而不必为特定的对象形状编码。

## 序列化成`JSON`

有时，您可能有不能在`JSON`中直接表示的值，但是您需要将它们转换成与之兼容的值。

假设我们有一个想要在我们的`JSON`数据中使用的`Set`。默认情况下，`Set`不能序列化为 JSON，因为它存储对象引用，而不仅仅是字符串和数字。但是如果我们有一个`Set`可序列化的值(比如字符串 id)，那么我们可以写一些在`JSON`中可编码的东西。

对于这个例子，让我们假设我们有一个包含属性`memberOfAccounts`的`User`对象，属性`memberOfAccounts`是它可以访问的帐户的字符串 id 的`Set`。我们可以在`JSON`中对此进行编码的一种方法是使用一个数组。

```
const user = {
  id: '1',
  memberOfAccounts: new Set(['a', 'b', 'c'])
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将通过使用名为 [`stringify`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 的`JSON` API 中的第二个参数来做到这一点。我们通过 [`replacer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#The_replacer_parameter) 函数

`const user = { id: '1', memberOfAccounts: new Set(['a', 'b', 'c']) };` `const replacer = (key, value) => { if (value instanceof Set) { return { $type: 'Set', args: [Array.from(value)] }; } else { return value; } }; const jsonData = JSON.stringify(user, replacer, 2);`

这样，如果我们想把它解析回原始状态，我们也可以反过来。

## 完成循环

但是在我们验证反向映射有效之前，让我们扩展我们的方法，使`$type`可以是动态的，我们的 reviver 将检查全局名称空间以查看该名称是否存在。

我们需要编写一个函数，它能够获取一个类的名称并返回该类的构造函数，这样我们就可以执行它。因为没有办法检查当前的作用域和枚举值，所以这个函数需要将它的类传递给它:

```
const createClassLookup = (scope = new Map()) => (name) =>
  scope.get(name) || (global || window)[name]; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数在给定的范围内查找名称，然后返回到全局名称空间，尝试解析内置的类，如`Set`、`Map`等。

让我们通过将`Drone`定义在解析范围内来创建类查找:

```
const classes = new Map([
  ['Drone', Drone]
]);

const getClass = createClassLookup(classes);

// we can call getClass() to resolve to a constructor now
getClass('Drone'); 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们把这些放在一起，看看它是如何工作的:

`const _name = Symbol('name'); const _config = Symbol('config'); class Drone { constructor(name, config) { Object.defineProperties( this, { [_name]: { value: name, configurable: false, enumerable: false }, [_config]: { value: config, configurable: false, enumerable: false } } ); } get name() { return this[_name]; } } const user = { id: '1', memberOfAccounts: new Set(['a', 'b', 'c']) }; const replacer = (key, value) => { if (value instanceof Set) { return { $type: 'Set', args: [Array.from(value)] }; } else { return value; } }; const jsonData = JSON.stringify(user, replacer, 2);` `const createClassLookup = (scope = new Map()) => (name) => scope.get(name) || (global || window)[name]; const classes = new Map([ ['Drone', Drone] ]); const getClass = createClassLookup(classes); const reviver = (key, value) => { const Type = getClass(value.$type); if (Type && typeof Type == 'function') { return new Type(...value.args); } else { return value; } } const parsedUser = JSON.parse(jsonData, reviver);`

瞧吧！我们已经成功地将对象解析并恢复到正确的实例中！让我们看看是否可以用一个更复杂的例子让动态类解析器工作:

```
const jsonData = `[
  {
    "id": "1",
    "memberOf": { "$type": "Set", "args": [["a"]] },
    "drone": { "$type": "Drone", "args": ["George Droney", { "id": "1" }] }
  }
]`; 
```

Enter fullscreen mode Exit fullscreen mode

准备，设置，解析！

`const _name = Symbol('name'); const _config = Symbol('config'); class Drone { constructor(name, config) { Object.defineProperties( this, { [_name]: { value: name, configurable: false, enumerable: false }, [_config]: { value: config, configurable: false, enumerable: false } } ); } get name() { return this[_name]; } } const jsonData = [ '[', ' {', ' "id": "1",', ' "memberOf": { "$type": "Set", "args": [["a"]] },', ' "drone": { "$type": "Drone", "args": ["George Droney", { "id": "1" }] }', ' }', ']' ].join('\n'); const createClassLookup = (scope = new Map()) => (name) => scope.get(name) || (global || window)[name];` `const classes = new Map([ ['Drone', Drone] ]); const getClass = createClassLookup(classes); const reviver = (key, value) => { const Type = getClass(value.$type); if (Type && typeof Type == 'function') { return new Type(...value.args); } else { return value; } } const data = JSON.parse(jsonData, reviver, 2);`

如果你深入到对象结构，你会注意到对象上的`memberOf`和`drone`属性是`Set`和`Drone`的实际实例！

## 包装完毕

我希望上面的例子能让您更好地理解内置于`JSON` API 中的解析和序列化管道。每当您处理需要合并到类实例(或反过来)的传入数据对象的数据结构时，这提供了一种方法来将它们双向映射，而不必编写自己的递归或定制函数来处理转换。

编码快乐！

[![](../Images/905d0687446a2374a9dcb6e8e4a7ac5f.png)T2】](https://i.giphy.com/media/3orieStB8OH7lanfGg/giphy.gif)