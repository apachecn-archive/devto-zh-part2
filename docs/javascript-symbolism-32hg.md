# JavaScript 符号

> 原文：<https://dev.to/kayis/javascript-symbolism-32hg>

在 ES2015 中，JavaScript 得到了相当大的更新，带来了许多新的东西，将其提升到一个更现代的地方。其中一个附加物是符号 T1。

## 什么

`Symbol`是 JavaScript 中一个新的基本类型，它有一个主要目的:**是唯一的**

虽然`object`也是唯一的，但是`Symbol`具有可用作对象键的优势。

## 如何

有一个名为`Symbol()`的全局函数，它在每次被调用时都会返回一个新的唯一符号。

```
const mySymbol = Symbol();
const anotherSymbol = Symbol();

mySymbol === anotherSymbol // -> false 
```

Enter fullscreen mode Exit fullscreen mode

### 同描述

我们还可以添加一个描述，以便以后区分:

```
const mySymbol = Symbol("xyz");

console.log(mySymbol); // -> symbol(xyz) 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，这允许通过`Symbol.for("xyz")`获取一个符号。如果我们想使用一个没有人可以覆盖的唯一键，这可能是一个问题。

此外，即使我们使用相同的描述，每次调用`Symbol()`都会产生一个唯一的符号。

### 熟知的符号

还有预定义的符号，称为[众所周知的符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#Properties#Well-known_symbols)。

JavaScript 使用它们来确定对象行为，而不会干扰我们的字符串键。

例如，有一个`Symbol.iterator`，它被用来标记一个`Array`的迭代器方法，这样它就可以在一个`for`循环中使用。

```
const myObject = {
  [Symbol.iterator] = function*() {
    yield "first value";
    yield "second value";
  }
};

for(let value of myObject) console.log(value); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们添加了一个[生成器函数](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/function*)到我们的对象的`Symbol.iterator`键中，这允许`myObject`与一个`for`循环一起使用。

## 为什么

一个用例是向只有符号创建者知道的对象添加键。

例如， [React](https://reactjs.org/) 用自定义符号标记用`React.createElement()`函数创建的对象，这样它就知道它们是 React 元素。如果他们使用了一个字符串，它可能会被意外覆盖。

如上所述，另一个用例是使用*众所周知的符号*来使定制对象的行为更像*本地的*。