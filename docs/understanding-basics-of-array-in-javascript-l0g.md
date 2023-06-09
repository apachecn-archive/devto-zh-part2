# 理解 javascript 中数组的基础知识

> 原文：<https://dev.to/frugencefidel/understanding-basics-of-array-in-javascript-l0g>

*本帖最初发布于[我的博客](http://frugencefidel.com/blog/understanding-basics-of-array-in-javascript)。*

在这篇文章中，我将分享一些关于数组的基础知识。

**数组**

数组是存储数据组或列表的方式。

比方说，我们想在不使用数组的情况下存储颜色组。

```
 const color1 = 'purple';
  const color2 = 'black';
  const color3 = 'yellow';
  const color4 = 'aqua'; 
```

Enter fullscreen mode Exit fullscreen mode

如我们所见，我们的代码一点也不枯燥(不要重复自己)。他们是湿的(每件事都写两遍)，这不是写好代码的好习惯。我们可以用数组来解决这个问题。

```
 const colors = ['purple', 'black', 'yellow', 'aqua']; 
```

Enter fullscreen mode Exit fullscreen mode

**如何创建数组**

您可以从空数组开始，然后在以后添加数据，或者您可以从它的数据开始；

```
 // empty array
  const colors = [];

  // with data
  const colors = ['purple', 'black', 'yellow', 'aqua']; 
```

Enter fullscreen mode Exit fullscreen mode

**将数据加入数组**

我知道有两种向数组中添加数据的方法，“括号符号”和数组方法。

*一.用括号标注*

数组从 0 开始索引。

```
 const colors = [];

  // Add first data
  colors[0] = 'purple';

  // So the second item
  colors[1] = 'black';

  console.log(colors); // ['purple', 'black']; 
```

Enter fullscreen mode Exit fullscreen mode

*二。通过数组方法*

如果你想在数组的第一个位置添加项，使用 unshift 方法，在最后使用 push 方法。

```
 const colors = ['purple', 'black'];

  // use unshift method to add to front
  colors.unshift('aqua');
  console.log(colors); // ['aqua', 'purple', 'black'];

  // use push method to add to end
  colors.push('yellow');
  console.log(colors); // ['aqua', 'purple', 'black', 'yellow']; 
```

Enter fullscreen mode Exit fullscreen mode

**从数组**中访问数据

您可以使用括号符号从数组中访问数据。

```
 const colors = ['purple', 'black', 'yellow', 'aqua'];

  // black and aqua
  colors[1]; // 'black'
  colors[3]; // 'aqua' 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过循环访问数组的条目。

```
 const colors = ['purple', 'black', 'yellow', 'aqua'];

  for(const color of colors) {
    console.log(color);  // purple black yellow aqua
  } 
```

Enter fullscreen mode Exit fullscreen mode

**更新数组**的数据

您也可以使用括号符号来更新数组的数据。

```
 const colors = ['purple', 'black'];

  // update black to yellow
  colors[1] = 'yellow';
  console.log(colors); // ['purple', 'yellow']; 
```

Enter fullscreen mode Exit fullscreen mode

数组可以保存任何数据类型，并且可以嵌套。

```
 const data = [24, true, ['orange', null], undefined]; 
```

Enter fullscreen mode Exit fullscreen mode