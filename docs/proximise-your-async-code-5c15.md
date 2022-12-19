# 代理您的异步代码

> 原文：<https://dev.to/kayis/proximise-your-async-code-5c15>

如果你最近已经写了相当多的 JavaScript，那么你可能会遇到[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)和它们的语言集成[异步函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)。有了 [Proxymise](https://github.com/kozhevnikov/proxymise) ，现在有一种方法可以让你的代码更加一致！

## 什么

Proxymise 是一个 JavaScript 库，它允许您包装返回承诺的函数，这样您就可以对它们要解析的对象调用方法。

## 为什么

使用同步函数时，可以直接使用返回值。这让你可以为你的库编写流畅的接口。

```
select("*").from("myTable").where("x > 10"); 
```

Enter fullscreen mode Exit fullscreen mode

虽然*异步函数*对某些类型的调用有所帮助，但它仍然会使您的代码混乱。

Proxymise 想解决这个问题。

## 如何

而不是像这样写一个`then`链:

```
fetch("example.com")
.then(r => r.json())
.then(r => r.record.id)
.then(id => ...); 
```

Enter fullscreen mode Exit fullscreen mode

或者像这样的`await`列表:

```
let r = await fetch("example.com");
r = await r.json();
const {id} = r.record;
... 
```

Enter fullscreen mode Exit fullscreen mode

它允许你在上面包装你的承诺和调用方法，使你的界面更加流畅。

```
const newFetch = proxymise(fetch);

const {id} = await newFetch("example.com").json().record; 
```

Enter fullscreen mode Exit fullscreen mode

它通过在承诺还未兑现时立即返回[代理对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)来做到这一点。然后，它们将等待解析并在以后应用函数调用。

## 结论

Proxymise 是一种快速方法，通过消除样板代码，使异步库的使用更加合理。