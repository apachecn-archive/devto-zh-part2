# 让我给你解释一下什么是“这”。(Javascript)

> 原文：<https://dev.to/ycmjason/let-me-explain-to-you-what-is-this-javascript-44ja>

> 原帖:[https://www.ycmjason.com/blog/2018/06/15.html](https://www.ycmjason.com/blog/2018/06/15.html)
> 
> *   本文假设在所有上下文中使用“严格”
> *   本文还假设了一些关于函数的知识，但是仍然有点困惑

Javascript 中的`this`可能是编程世界中最神奇的关键词。它的不可预测性已经达到了前所未有的程度。

[![it's over 9000!!!](img/4e42324f364145b5429a0cdcde52a8a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HYHviAUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/MvedbKot538WY/giphy.gif)

然而，如果你想成为一名 Javascript 高手，完全理解它是很重要的。那么让我试着给你解释一下**什么是`this`** 。(如果不行，好吧，至少我试过了。)

## 功能

从函数开始。在这篇文章中，我想把函数分成三个不同的类别。

1.  正常功能
2.  箭头功能
3.  有界函数

### 正常功能

我把普通函数定义为用...

```
// function declaration
function magic() {
    ...
}

// function expression
const magic = function() {
    ...
};

// (or if you hate your life)
// function constructor
const magic = new Function('...'); 
```

Enter fullscreen mode Exit fullscreen mode

### 箭头功能

箭头功能基本上是 ES6 箭头功能:

```
const magic = () => {
    ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 绑定函数

可以通过调用普通函数上的`Function.prototype.bind`来创建绑定函数。

```
// magic is created with function declaration/expression/constructor
const bound = magic.bind(...); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/fdf90d223d8106e0a4f64d60522bd05c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h4UCcbB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/zG6MKhlBxIloc/giphy.gif)

## 调用函数的方式

现在假设我们有一个函数`f`(任何类别)。有两种方法可以调用它。

1.  隐式(直接)调用
2.  显式调用

### 隐式(直接)调用

隐式(直接)调用很无聊:

```
/* f is defined */

// direct call
f();

// or attach it to an object and call it
const obj = {};
obj.fn = f;
obj.fn(); 
```

Enter fullscreen mode Exit fullscreen mode

### 显式调用

显式调用更有趣。你可以用`Function.prototype.call`或者`Function.prototype.apply`调用你的函数。

```
/* f is defined */

// Function.prototype.call
f.call(...);

// Function.prototype.apply
f.apply(...); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/0cc17edb811e21fbe0bac796422617a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GimMpfxA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/3oEjI5VtIhHvK37WYo/giphy.gif)

## 快速回顾

让我们快速回顾一下，我们有 3 类功能:

1.  普通函数-使用函数声明/表达式/构造函数创建
2.  箭头功能- `() => {...}`
3.  绑定函数-用`f.bind(...)`创建

调用函数有两种方法:

1.  隐式(直接)调用- `f()`或`obj.f()`
2.  显式调用- `f.call(...)`或`f.apply(...)`

这意味着我们有 6 个不同的场景。

1.  普通函数+隐式(直接)调用
2.  普通函数+显式调用
3.  箭头函数+隐式(直接)调用
4.  箭头函数+显式调用
5.  绑定函数+隐式(直接)调用
6.  绑定函数+显式调用

[![](img/0c6963d98719f22f728373554f0376d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rxUSTYMN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/lKZEeXJGhU1d6/giphy.gif)

别慌，没那么可怕。

事实上，箭头函数和绑定函数并不关心隐式/显式调用。所以这就简化为 4 种情况:

1.  普通函数+隐式(直接)调用
2.  普通函数+显式调用
3.  箭头功能
4.  有界函数

## 程序查找`this`

[![](img/9b278399a1217ebdd3c8128776402e7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sui3WVSF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/O7pL1zfZKI836/giphy.gif) 
下面是在`f` :
[![](img/bd0e87250741af4aa2bfa19002974e00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UEIp_Vxd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jbon851k9easj5ot8tte.png) 函数中查找`this`绑定的过程

## 演习！

给定`magic`定义如下:

```
'use strict';

const magic = function() {
    // a. what is `this`?
    console.log(this);

    const cool = () => {
        // b. what is `this`?
        console.log(this);
    };
    cool();
};

// QUESTION 1
magic();

// QUESTION 2
const apple = { name: 'apple' };
apple.magic = magic;
apple.magic();

// QUESTION 3
const orange = { name: 'orange' };
magic.call(orange); 
```

Enter fullscreen mode Exit fullscreen mode

### 问题 1.a

按照流程图，我们想在`magic`中找到`this`。

1.  `magic`的类别是普通功能
2.  `magic`被隐式(直接)调用
3.  `magic`用`magic()`调用
4.  所以`this` = `undefined`！！！

### 问题 1.b

按照流程图，我们想在`cool`中找到`this`。

1.  `cool`的类别是箭头功能
2.  从问题 1.b 中，我们知道`magic`的`this`是`undefined`
3.  `cool`的定义者是`magic`
4.  所以`this` = `magic`的`this` = `undefined`！

## 懒惰的讲师

剩下的问题，问题 2.a，2.b，3.a 和 3.b，用我的流程图来说都是微不足道的。所以我会把它们留给你们作为练习。

## 答案

[https://repl.it/@ycmjason/What-is-this](https://repl.it/@ycmjason/What-is-this)

点击运行，你会依次看到答案(1.a，1.b，2.a，2.b，3.a，3.b)。

## 注

*   没有“绑定箭头功能”。`(() => {...}).bind(...)`还是原来的箭头功能。
*   对于隐式调用，只有形状(`f()`或`obj.f()`)有关系。`f`来自哪里并不重要。考虑以下代码:

```
const f = obj.f; // obj.f is a normal function
f(); // `this` in the body of `f` is `undefined`!!! not `obj`!!! 
```

Enter fullscreen mode Exit fullscreen mode

> 更新:
> 
> *   2018 . 7 . 16:感谢 [@joshcheek](https://dev.to/joshcheek) 提醒我箭头函数`this`的正确绑定！
> *   2018 . 6 . 18:感谢勇指出错别字！