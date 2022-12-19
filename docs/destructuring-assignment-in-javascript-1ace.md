# JavaScript 中的析构赋值

> 原文：<https://dev.to/gksander/destructuring-assignment-in-javascript-1ace>

JavaScript 是一种...有趣的...语言。我个人很喜欢它，但可以理解为什么其他人不会这么喜欢它。ECMAScript 6，也就是 ES6，引入了一些非常好的特性，使得 JavaScript 开发更加有趣。在这篇短文中，我想谈一点关于*析构赋值*的内容，并提供一些有用的实例。

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 以如下方式描述析构赋值:

> **析构赋值**语法是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。

这意味着你可以获取一个数组或对象，并轻松地从中提取值，并将它们赋给变量。

## 用对象解构赋值

假设你有一个物体代表三维空间中的一个点:

```
let myPointInSpace = {
  x: 3,
  y: -2,
  z: 2.7
} 
```

假设你想用这个点的坐标做一些计算[比如计算它离原点的距离，(0，0，0)]，所以你想把这个点的`x`、`y`和`z`坐标放在一些变量中以便于引用。人们可以做以下事情:

```
let x = myPointInSpace.x;
let y = myPointInSpace.y;
let z = myPointInSpace.z;

let distance = Math.sqrt(x*x + y*y + z*z); 
```

这当然管用！但是如果你这样做的次数足够多，你可能会厌倦复制/粘贴。有了析构赋值，你可以用更简洁的方式提取坐标！下面是方法:

```
let {x, y, z} = myPointInSpace;

let distance = Math.sqrt(x*x + y*y + z*z); 
```

在这种情况下，花括号表示析构赋值。上面的第一行着眼于`myPointInSpace`变量，并寻找花括号中指示的任何属性，并将这些属性返回到单独的变量赋值中。

方便的是，您只能选取对象属性值的子集。例如，如果你只需要`x`和`y`坐标，你也可以做:

```
let {x, y} = myPointInSpace; 
```

## 用数组析构赋值

析构赋值在用于对象时非常有用，但它也可以以类似的方式用于数组。让我们假设我们在空间中的点被表示为一个三元组(或 3 个值的数组)。

```
let myPointInSpace = [3, -2, 2.7]; 
```

获取该点坐标的传统方法是:

```
let x = myPointInSpace[0];
let y = myPointInSpace[1];
let z = myPointInSpace[2]; 
```

对于析构赋值，我们可以将其简化为:

```
let [x, y, z] = myPointInSpace; 
```

相当整洁！

## 一些真实的例子

在我真正使用赋值之前，我读过几次关于析构赋值的文章，并体会到了它的效用。我在上面展示了一个简单的例子，它只是简单地提取值，但是我还想展示几个更有用的例子。

### 设置默认函数值

在编写函数时，我通常喜欢使用单个对象作为输入，然后从该对象中提取值——这样我就不必担心输入的顺序。析构赋值有助于这一点，并允许我们利用 ES6 中引入的“默认参数”。

假设您想要编写一个函数，它需要几个小时、几分钟和几秒钟，并将这些时间转换成几毫秒。我们可以通过下面的方法做到这一点:

```
let toMilliseconds = ({
  hours = 0,
  minutes = 0,
  seconds = 0
}) => {
  // Compute the # of ms
  let ms = (hours * 60 * 60 * 1000) + (minutes * 60 * 1000) + (seconds * 1000);
  // Return the # of ms
  return ms;
} 
```

这可能看起来像一个奇怪的函数声明，但是它要求我们可以将一个对象作为输入传递给`toMilliseconds()`，函数将寻找要使用的键`hours`、`minutes`和`seconds`。如果它在传入的对象中没有找到任何一个键，它将默认为 0。将它投入使用可能如下所示:

```
let ms1 = toMilliseconds({hours: 3, minutes: 42, seconds: 33});
let ms2 = toMilliseconds({minutes: 7}); 
```

在上面的第二行中，`hours`和`seconds`被默认为 0，我们不必显式地传递小时数或秒数。

我越来越喜欢这种编写函数的方式，因为有些函数有很多很多参数都需要缺省值——这种形式的函数声明对我来说很容易理解。

### 交换数值

交换变量的值是一个有点常见的过程，通常涉及到创建一个临时变量。这是一个经典的例子。

```
// Initial values
let x = 5;
let y = 3;

// Now swap, creating tmp variable
let tmp = y;
y = x;
x = tmp;
delete tmp; 
```

然而，析构赋值使它更简洁，并且在我看来，可读性更好一些:

```
// Initial values
let x = 5;
let y = 3;

// Now swap
[x, y] = [y, x]; 
```

如果你发现自己经常交换变量，析构可能是一个非常好的工具。

### 拉取数值，赋予新名称

使用对象析构，除了你要析构的对象的键之外，你实际上可以给你的变量命名。假设您正在使用一个 API，API 发送回一个响应，其中对象具有您不喜欢使用的奇怪名称。大概如下:

```
let apiResponse = {
  someWeirdKeyForX: 3,
  someWeirdKeyForY: -7
} 
```

我们可以从响应中提取出`x`和`y`值，并随意命名它们——比如说`x`和`y`。为此，我们使用下面的语法:

```
let {someWeirdKeyForX: x, someWeirdKeyForY: y} = apiResponse; 
```

析构的`someWeirdKeyForX: x`部分声明您想要从`apiResponse`中取出键`someWeirdKeyForX`，并且您想要将它赋给一个名为`x`的变量。这非常有用。实际上，我喜欢在简单的场景中使用它，比如将`apiResponse.latitude`分配给`lat`和`apiResponse.longitude`分配给`lng`。

## 小心！

我偶然发现的一个小问题是，有时你需要将你的析构语句用括号括起来。如果你的析构语句不是以变量声明关键字开始的(比如`var`、`let`或`const`)，你需要用圆括号将你的语句括起来。我假设编译器知道如何区分析构语句中的`{ ... }`和表示代码块的`{ ... }`。

这就是我的意思。考虑下面的代码:

```
// Declare x and y ahead of time
let x, y;
// Object that we will destructure
let o = {x: 3, y: -7};

// Try to destructure
{x, y} = o; // -> No good! 
```

编译器不知道如何解释最后一行代码。你需要把它改成:

```
// Declare x and y ahead of time
let x, y;
// Object that we will destructure
let o = {x: 3, y: -7};

// Successfully destructure
({x, y} = o); // -> Good! 
```

如果你发现自己在使用析构，一定要注意这个小小的警告！

### 还有更！

MDN 有更多析构赋值的例子。如果你想知道更多，请查看该页面。

在我的下一篇文章中，我们将深入探讨 Javascript 中的 *rest* 和 *spread* 操作符，看看它们是如何在析构赋值中发挥作用的。