# javascript 中的可选(空安全)

> 原文：<https://dev.to/pichardoj/optional-null-safe-in-javascript-1b7k>

昨天我碰到了[这个](https://stackoverflow.com/questions/50452219/how-to-return-blank-string-if-object-is-undefined-in-angularjs-forms) StackOverflow 问题，它让我想到了 javascript 中的`null` / `undefined`处理。

## 短暂的背景

**什么是`undefined`？** `undefined`是一个原始值，赋予只被声明的变量，不存在的属性或函数参数

**什么是`null`？** `null`是另一个代表价值缺失的本原值。

那么当我们做下面的
时会发生什么

```
 let obj;

console.log(obj.someProp); 
```

Enter fullscreen mode Exit fullscreen mode

我们得到以下错误

> TypeError:无法读取未定义的属性“someProp”

同样的情况也会发生在`null`

## 零检测

那么，我们如何避免这种情况呢？好吧，幸运的是在 javascript 中我们有`short-circuit`求值，这意味着为了避免前面的`TypeError`我们可以写下面的代码。

```
 let obj;

console.log(obj && obj.someProp); // Prints undefined 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们想更深入，比如说`obj.prop1.prop2.prop3`呢？我们将结束大量的检查，比如:

```
 console.log( obj && obj.prop1 && obj.prop1.prop2 && obj.prop1.prop2.prop3 ); 
```

Enter fullscreen mode Exit fullscreen mode

似乎很讨厌，不是吗？

如果在那个链中有一个`undefined`或`null`，我们想要打印一个缺省值呢？那么这将是一个更大的表达:

```
 const evaluation = obj && obj.prop1 && obj.prop1.prop2 && obj.prop1.prop2.prop3;

console.log( evaluation != null ? evaluation : "SomeDefaultValue" ); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他语言是怎么做到的？

这个问题不是 javascript 独有的，它存在于大多数编程语言中，所以让我们看看如何在其中一些语言中进行空检查。

#### Java

在 java 中，我们有`Optional` API:

```
 SomeClass object;

Optional.ofNullable(object)
    .map(obj -> obj.prop1)
    .map(obj -> obj.prop2)
    .map(obj -> obj.prop3)
    .orElse("SomeDefaultValue"); 
```

Enter fullscreen mode Exit fullscreen mode

#### [釜底抽薪](#kotlin)

在 kotlin(另一种 JVM 语言)中，有*猫王* ( `?:`)和*安全调用* ( `?.`)操作符。

```
 val object: SomeClass?

object?.prop1?.prop2?.prop3 ?: "SomeDefaultValue"; 
```

Enter fullscreen mode Exit fullscreen mode

#### C#

最后，在 c#中我们还有*空条件* ( `?.`)和*空合并* ( `??`)操作符。

```
 SomeClass object;

object?.prop1?.prop2?.prop3 ?? "SomeDefaultValue"; 
```

Enter fullscreen mode Exit fullscreen mode

## JS 选项

所以在看到这一切之后，我在想，*有没有一种方法可以避免用 javascript 写那么多东西？所以我开始尝试用 regex 编写一个函数，让我能够安全地访问对象属性。* 

```
 function optionalAccess(obj, path, def) {
  const propNames = path.replace(/\]|\)/, "").split(/\.|\[|\(/);

  return propNames.reduce((acc, prop) => acc[prop] || def, obj);
}

const obj = {
  items: [{ hello: "Hello" }]
};

console.log(optionalAccess(obj, "items[0].hello", "def")); // Prints Hello
console.log(optionalAccess(obj, "items[0].he", "def")); // Prints def 
```

Enter fullscreen mode Exit fullscreen mode

而在那之后，我又找到了关于`lodash._get`，它有着相同的签名:

`_.get(object, path, [defaultValue])`

但是说实话，我不太喜欢字符串路径，所以我开始寻找避免它们的方法，然后我想到了一个使用代理的解决方案:

```
 // Here is where the magic happens
function optional(obj, evalFunc, def) {

  // Our proxy handler
  const handler = {
    // Intercept all property access
    get: function(target, prop, receiver) {
      const res = Reflect.get(...arguments);

      // If our response is an object then wrap it in a proxy else just return
      return typeof res === "object" ? proxify(res) : res != null ? res : def;
    }
  };

  const proxify = target => {
    return new Proxy(target, handler);
  };

  // Call function with our proxified object
  return evalFunc(proxify(obj, handler));
}

const obj = {
  items: [{ hello: "Hello" }]
};

console.log(optional(obj, target => target.items[0].hello, "def")); // => Hello
console.log(optional(obj, target => target.items[0].hell, { a: 1 })); // => { a: 1 } 
```

Enter fullscreen mode Exit fullscreen mode

## 未来

目前，有一个 [TC39 提案](https://github.com/tc39/proposal-optional-chaining)将允许我们做以下事情:

```
 obj?.arrayProp?[0]?.someProp?.someFunc?.(); 
```

Enter fullscreen mode Exit fullscreen mode

看起来很整洁，对吗？然而，这个提议仍然处于第一阶段，这意味着我们可能需要一段时间才能看到这是 js。尽管如此，有一个 babel [插件](https://github.com/babel/babel/tree/master/packages/babel-plugin-proposal-optional-chaining)允许我们使用该语法。

## 大结局

`null`已经存在并将会存在一段时间，我敢打赌这是编程中最讨厌的概念之一，然而，有办法获得*零安全*。在这里，我已经张贴了我的两美分，让我知道你的想法，或者如果你有任何其他选择。

附言:这里有一个小小的[要点](https://gist.github.com/pichardoJ/76e3bb814b86d5a8860f9a2956e827ad)