# Javascript 中对象的功能

> 原文：<https://dev.to/rmadisetti3/the-function-of-objects-in-javascript-23c4>

[![](../Images/1d987a9d4e29f2285baf938c55745633.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ko_n2xqv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zsw1a582x1gz55hcix3h.png)

各位 Javascript 编码员好，

本文将清晰简洁地解释 Javascript 中对象的特性，以及每种类型的实现在创建最高效、占用内存最少的函数时的各种用途。

就其定义而言，Javascript 是一种典型的面向对象语言，这意味着 JS 中的所有东西都是某种类型的对象，除了原始数据类型(数字、字符串和布尔)。一个对象可以有一些属性，这些属性可以用点符号来调用。例如，我们可以看看这个物体:

`var car1 = {
company: 'Ford',
model: 'Mustang',
color: 'red',
isLocked: false
};`

对象`car1`有三个属性`company, model, and color`，而行`car1.model`将依次返回值`'Mustang'`。对象也可以有方法，这些方法是特定于对象的函数，并且不在其范围之上或之外定义。我们可以使用同一个`car1`对象，并添加两个方法`paintBlue()`和`lock()`:

`var car1 = {
company: 'Ford',
model: 'Mustang',
color: 'red',
isLocked: false,
paintBlue() {
this.color = 'blue';
},
lock() {
this.isLocked = true;
}
};`

现在这些方法被添加到了`car1`对象中，它们可以像任何其他属性一样使用点符号来调用。比如`car1.paintBlue()`会把`car1.color`从`'red'`变成`'blue'`。还要注意的是，`this`关键字必须被用来允许对象引用它自己的属性并且不改变函数的范围。

有了属性和方法的功能，对象在创建有用的 Javascript 函数，甚至全栈 web 应用程序中是不可或缺的。