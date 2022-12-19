# “箭头函数不会重新绑定“这个”是什么意思？”

> 原文：<https://dev.to/moz5691/what-does-it-mean-arrow-function-will-not-rebind-this-146d>

“箭头函数不会重新绑定“这个”是什么意思？

如何从回调函数中引用内部的 person 对象？在下面的示例中，引用的回调函数是“setTimeout()”。

```
[Code #1 ]
const person = {
  walk() {
    setTimeout(function() {
      console.log('this', this);
    }, 1000);
  }
};
person.walk(); 
```

以下是代码#1 的结果。

```
[Code #1 Result]
index.js:4 this 
    1\. Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …} 
```

在运行上面的例子时，“this”返回 Window 对象。肯定不是我们想看到的。在这种情况下,“this”内部回调函数引用了 person 对象的外部。即使启用了“严格模式”,我们仍然会看到相同的问题。这种意外行为的原因是 Javascript 中的“this”与其他语言的运行方式不同。Javascript 中的“this”在回调函数中重新绑定“this ”,这意味着“this”在传递给回调函数时指向“Window”对象。

有两种不同的方法来解决这个问题。[代码#2]和[代码#3]分别示出了旧的方式和新的方式。

如果不想用“ES6 中的箭头功能”，还是可以用老办法修复这个问题。我们可以将“this”传递给另一个常量值，比如“self ”,以便在调用回调函数之前将“this”的状态存储到“self”中。回调函数中的“self”存储“this”的先前状态，使得当“self”包含“this”的先前状态时，“this”仍然可以在回调函数内部被更新。显然，“self”在其中存储了带有 walk()方法的“person”对象。

```
[Code #2]
const person = {
  const self = this;
  walk() {
    setTimeout(function() {
      console.log('self', self);
    }, 1000);
  }
};
person.walk(); 
```

```
[Code #2 Result ]
this 
    1\. {walk: ƒ}
        1\. walk: ƒ walk()
            1\. arguments: null
            2\. caller: null
            3\. length: 0
            4\. name: "walk"
            5\. prototype: {constructor: ƒ}
            6\. __proto__: ƒ ()
            7\. [[FunctionLocation]]: index.js:2
            8\. [[Scopes]]: Scopes[1]
        2\. __proto__: Object 
```

箭头函数(= >)有助于解决“这种”重新绑定。ES6 箭头函数不会在回调函数中重新绑定“this”。如果我们把回调函数改成箭头函数，“这个”就继承到回调函数里了。只需将回调函数更改为箭头函数...这就是你需要做的。

```
[Code #3 ]
const person = {
walk() {
    setTimeout(() => {
        console.log('this', this);
    }, 1000);
    }
};
person.walk(); 
```

```
[Code #3 Result]
    1\. {walk: ƒ}
        1\. walk: ƒ walk()
            1\. arguments: null
            2\. caller: null
            3\. length: 0
            4\. name: "walk"
            5\. prototype: {constructor: ƒ}
            6\. __proto__: ƒ ()
            7\. [[FunctionLocation]]: index.js:2
            8\. [[Scopes]]: Scopes[1]
        2\. __proto__: Object 
```

希望这篇帖子可以帮助理解 ES6 arrow 函数，以及它是如何在回调函数中解决“this”重绑定的。