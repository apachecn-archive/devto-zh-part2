# Javascript 变量- var，let 和 const。

> 原文：<https://dev.to/frugencefidel/javascript-variables---var-let-and-const-32c>

*这篇文章最初发布在[我的博客](http://frugencefidel.com/blog/javascript-variables---var-let-and-const)和我的 youtube [频道](https://youtu.be/dKZ8Z834dKU)。*

Javascript 变量是保存数据的容器。

在 Javascript 中声明变量时会用到三个关键字，分别是`var`、`let`和`const`。他们遵循这种模式或语法`var variableName = variableValue;`。

Javascript 变量是动态类型的，这意味着它们可以从一种数据类型转换为另一种数据类型。下面，变量 fullName 从字符串变为数字，然后变为布尔值。

```
 var fullName = 'Frugence Fidel'; // Frugence Fidel
  fullName = 100; // 100
  fullName = false; // false 
```

Enter fullscreen mode Exit fullscreen mode

**时间死区**

在定义变量之前，不能访问它。

```
 console.log(fullName); // Uncaught ReferenceError: fullName is not defined
  const fullName = 'Frugence Fidel'; 
```

Enter fullscreen mode Exit fullscreen mode

**命名变量的方式或风格**

1.  蛇 _ 案例

    `var full_name = 'Frugence Fidel';`

2.  茶包

    `var fullName = 'Frugence Fidel';`

建议使用 camelCase。

**声明变量的三种方式**

**= >在**处

这是在 ES6 之前声明变量的唯一方法。在这里，您可以多次声明相同的变量，并且可以更新。

```
 var myFriend = 'Baraka';
  var myFriend = 'Peter';
  console.log(myFriend); // 'Peter' 
```

Enter fullscreen mode Exit fullscreen mode

如果在 block 语句内部声明变量，该变量将会泄漏到外部。

```
 var bodyWeight = 50;
  if (bodyWeight > 49) {
    var water = 1.4;
    console.log(`For body weight of ${bodyWeight}kg, you should drink water atleast ${water}litre`);
  }
  console.log(water); // 1.4 
```

Enter fullscreen mode Exit fullscreen mode

**= >设常数**

`let`和`const`是 ES6 中引入的声明变量的新方法。在`let`和`const`中，你不能声明变量两次。

```
 let myFriend = 'Baraka';
  let myFriend = 'Peter'; // Uncaught SyntaxError: Identifier 'myFriend' has already been declared 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，`let`和`const`几乎是一样的，唯一的区别我知道，`const`不能更新，但`let`可以。

```
 // let can be updated
  let myFriend = 'Baraka';
  myFriend = 'Peter';
  console.log(myFriend); // Peter

  // const cannot be updated
  const otherFriend = 'Patrick';
  otherFriend = 'Raphael'; // Uncaught TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

如果使用`let`或`const`，变量不会泄漏到 block 语句之外。

```
 const bodyWeight = 50;
  if (bodyWeight > 49) {
    const water = 1.4;
    console.log(`For body weight of ${bodyWeight}kg, you should drink water atleast ${water}litre`);
  }
  console.log(water); // Uncaught ReferenceError: water is not defined 
```

Enter fullscreen mode Exit fullscreen mode

**何时使用 var，let 和 const**

声明变量时总是使用`const`,当你想更新变量时只使用`let`。`var`不应该用在 ES6 及以上。

这是关于这个主题的视频

[https://www.youtube.com/embed/dKZ8Z834dKU](https://www.youtube.com/embed/dKZ8Z834dKU)