# JavaScript forEach 你应该知道的 10 个 JavaScript 数组方法

> 原文：<https://dev.to/frugencefidel/10-javascript-array-methods-you-should-know-4lk3>

这篇文章最初发表在我的博客上。

在这篇文章中，我将分享你应该知道的 10 个 JavaScript 数组方法。

如果你对数组一无所知，可以点击这里查看[数组介绍](http://frugencefidel.com/blog/understanding-basics-of-array-in-javascript)。

这里有 10 个你至少应该知道的 javascript 数组方法

*1。forEach()*

这个方法可以帮助你循环数组的元素。

```
 const arr = [1, 2, 3, 4, 5, 6];

  arr.forEach(item => {
    console.log(item); // output: 1 2 3 4 5 6
  }); 
```

Enter fullscreen mode Exit fullscreen mode

*2。包含()*

此方法检查数组是否包含方法中传递的项。

```
 const arr = [1, 2, 3, 4, 5, 6];

  arr.includes(2); // output: true
  arr.includes(7); // output: false 
```

Enter fullscreen mode Exit fullscreen mode

*3。过滤器()*

这个方法创建一个新的数组，在提供的函数中只包含传递条件的元素。

```
 const arr = [1, 2, 3, 4, 5, 6];

  // item(s) greater than 3
  const filtered = arr.filter(num => num > 3);
  console.log(filtered); // output: [4, 5, 6]

  console.log(arr); // output: [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

*4。map()*

该方法通过在每个元素中调用提供的函数来创建新的数组。

```
 const arr = [1, 2, 3, 4, 5, 6];

  // add one to every element
  const oneAdded = arr.map(num => num + 1);
  console.log(oneAdded); // output [2, 3, 4, 5, 6, 7]

  console.log(arr); // output: [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

*5。reduce()*

> reduce()方法对一个累加器和数组中的每个元素(从左到右)应用一个函数，将它简化为一个值——MDN

```
 const arr = [1, 2, 3, 4, 5, 6];

  const sum = arr.reduce((total, value) => total + value, 0);
  console.log(sum); // 21 
```

Enter fullscreen mode Exit fullscreen mode

*6。一些()*

此方法检查是否至少有一个数组项通过了条件。如果通过，则返回“真”，否则返回“假”。

```
 const arr = [1, 2, 3, 4, 5, 6];

  // at least one element is greater than 4?
  const largeNum = arr.some(num => num > 4);
  console.log(largeNum); // output: true

  // at least one element is less than or equal to 0?
  const smallNum = arr.some(num => num <= 0);
  console.log(smallNum); // output: false 
```

Enter fullscreen mode Exit fullscreen mode

*7。每()*

这个方法检查是否所有的数组元素都通过了条件。如果通过，则返回“真”，否则返回“假”。

```
 const arr = [1, 2, 3, 4, 5, 6];

  // all elements are greater than 4
  const greaterFour = arr.every(num => num > 4);
  console.log(greaterFour); // output: false

  // all elements are less than 10
  const lessTen = arr.every(num => num < 10);
  console.log(lessTen); // output: true 
```

Enter fullscreen mode Exit fullscreen mode

*8。sort()*

这种方法用于排列/排序数组的项目或升序或降序。

```
 const arr = [1, 2, 3, 4, 5, 6];
  const alpha = ['e', 'a', 'c', 'u', 'y'];

  // sort in descending order
  descOrder = arr.sort((a, b) => a > b ? -1 : 1);
  console.log(descOrder); // output: [6, 5, 4, 3, 2, 1]

  // sort in ascending order
  ascOrder = alpha.sort((a, b) => a > b ? 1 : -1);
  console.log(ascOrder); // output: ['a', 'c', 'e', 'u', 'y'] 
```

Enter fullscreen mode Exit fullscreen mode

*9。Array.from()*

这将所有类似数组或可迭代的东西变成真正的数组，尤其是在使用 DOM 时，这样就可以使用其他数组方法，如 reduce、map、filter 等等。

```
 const name = 'frugence';
  const nameArray = Array.from(name);

  console.log(name); // output: frugence
  console.log(nameArray); // output: ['f', 'r', 'u', 'g', 'e', 'n', 'c', 'e'] 
```

Enter fullscreen mode Exit fullscreen mode

使用 DOM

```
 // I assume that you have created unorder list of items in our html file.

  const lis = document.querySelectorAll('li');
  const lisArray = Array.from(document.querySelectorAll('li'));

  // is true array?
  console.log(Array.isArray(lis)); // output: false
  console.log(Array.isArray(lisArray));  // output: true 
```

Enter fullscreen mode Exit fullscreen mode

10。Array.of()

这将从传递给它的每个参数中创建一个数组。

```
 const nums = Array.of(1, 2, 3, 4, 5, 6);
  console.log(nums); // output: [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode