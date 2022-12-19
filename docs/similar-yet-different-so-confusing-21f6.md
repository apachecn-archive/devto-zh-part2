# 相似却又不同。如此令人困惑

> 原文：<https://dev.to/stereobooster/similar-yet-different-so-confusing-21f6>

有些概念非常相似，但又不同，让许多人感到困惑。

## 参照透明性与不变性

引用透明性是编程语言中的一个概念，它保证一旦值被赋给变量~~就不会改变~~，没有办法给它赋不同的值。例如，在 ES6 中，您可以使用`const`来表示它:

```
const a = { x: 1 };
a = { z: 1 }; // TypeError: Assignment to constant variable
a.x = 2; // This is ok 
```

(不确定为什么这是一个**类型的**错误，但是你明白了)

不变性是编程语言(以及不同领域)中的一个概念，它保证值一旦被创建就保持不变。例如，在 ES6 中，您可以使用`Object.freeze`来表示它:

```
let b = Object.freeze({ x: 1 });
b.x = 2; // Silently ignores error e.g. does nothing
b = { z: 1 }; // This is ok 
```

**困惑**:我猜没有多少人知道或者每天都在使用“参照透明”这个术语。编程语言称之为常量(`const`)。常数是保持不变的东西，你甚至可以说是不可变的...

## 身份验证与授权

在用户访问应用程序的上下文中，身份验证告诉应用程序当前用户是谁以及他们是否存在( [source](https://oauth.net/articles/authentication/) )。

认证回答了你是谁的问题。

在用户访问应用程序的上下文中，授权告诉应用程序是否允许当前用户(包括未经身份验证的用户)执行给定的操作。

授权回答了这个问题——你被允许做什么。

**困惑**:首先，名字非常相似，这在一开始就不是个好主意(命名很难)。困惑的第二部分是您通常需要身份验证来进行授权。

你最喜欢的困惑来源是什么？

> 照片由 Lisa Algra 在 Unsplash 上拍摄