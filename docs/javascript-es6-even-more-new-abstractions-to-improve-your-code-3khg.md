# JavaScript ES6:更多新的抽象来改进您的代码

> 原文：<https://dev.to/bnevilleoneill/javascript-es6-even-more-new-abstractions-to-improve-your-code-3khg>

#### 利用强大的 ES6 特性编写更好、更优雅、更可预测的 JavaScript。

[![](img/cd9503272d57134053d9be85a68ee93b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RrShs-i---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJioH6ER4FMK_jUbZpIwf7w.jpeg)

JavaScript ES6，也称为 ECMAScript 2015，有许多非常酷的新功能，可以提高代码的质量。昨天， [Glad 介绍了 ES6 中的五个新特性](https://dev.to/bnevilleoneill/javascript-es6-5-new-abstractions-to-improve-your-code-492i-temp-slug-1721386),这些特性可能会改进你编写的代码。

所以我想，为什么不多拍五部呢？

对于那些略读的人来说，这里是它们的列表:

1.  Let + Const
2.  For … of 循环
3.  传播
4.  地图
5.  承诺

#### 1。Let + Const

Let 只是声明变量的另一种方式，唯一的区别是 Let 是块范围的，也就是说，当它被用来在块内声明变量时，该变量的值在块外是不可访问的。

```
let foo = 10;
let bar = 5;

if (true) {
    let bar = foo * 2;
    console.log( bar ); // 20
}

console.log( bar ); // 5 
```

Const 允许你为一个变量设置一个值，这个值在应用程序的整个生命周期中保持不变。

```
const foo = 20; 
```

#### 2。对于…的循环

在 ES6 中，引入了一种迭代数组中每个值的新方法，称为 for...循环的。

它还使得遍历集合的元素变得更加容易。

```
let colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

for(var color of colors){
  console.log(color);
} 
```

这将记录数组的直接值，而不必像通常那样通过 key colors[i]获取它。

#### 3。传播

扩展运算符，也称为...运算符是在 ES6 中引入的。它使处理对象变得更加容易，因为它有助于两件事:

1.  将一个数组或对象展开到另一个数组或对象中，
2.  将多个参数加入一个数组。

将一个数组或对象扩展到另一个:

```
let arr1 = [1, 2 , 3];
let arr2 = [0, ...arr1, 4, 5, 6];
console.log(arr2); // 0,1,2,3,4,5,6 
```

如上所述，spread 操作符在使用数组向函数传递参数时也很方便:

```
function user(name, age)
{
  console.log(`My name is ${name}, I am ${age} years old.`);
}

let person = ['Brian Willer', 38];

user(...person); //My name is Brian Willer, I am 38 years old. 
```

#### 4。地图

地图是相似的数组。它们拥有一个键值对，但是允许你指定你自己的索引，并且这个索引是唯一的。

```
var greetings = new Map();

greetings.set("hello", "Bless");
greetings.set(name, "panda");

greetings.get(name); // panda
greetings.get("hello"); // Bless

greetings.size; // 2 
```

*行动中的地图*

注意:要在许多浏览器上使用地图，你需要一个 polyfill，因为没有浏览器实现它。

#### 5。承诺

承诺为我们提供了一种以同步方式处理异步操作和流程的方法。有了它，我们可以轻松地编写非依赖代码。

有人认为不需要承诺，可以使用异步、回调等。然而，Javascript ES6 现在有了一个易于使用的标准承诺实现。

```
var welcomeMessage =  new Promise(function(resolve, reject) {
  setTimeout(resolve, 1000)
}).then(function() {
  console.log('Welcome User!')
}) 
```

**注意:**并不是所有的浏览器都支持开箱即用，所以你需要一个 polyfill 来跨浏览器使用。

#### 结论

ES6 有这么多很酷很神奇的功能，有些你可能会用到，有些你可能不会马上用到，但它们仍然值得一试。

如果你想了解更多的功能，卢克·霍班创建了一个 ES6 功能回购，列出了 ES6 提供的所有酷功能。

如果你觉得这篇文章很有帮助，别忘了分享一下然后点击**按钮**上的**狂怒**。

干杯！！！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子 [JavaScript ES6:更多改进你的代码的新抽象](https://blog.logrocket.com/javascript-es6-even-more-new-abstractions-to-improve-your-code-f046869e4a92/)首先出现在 [LogRocket 博客](https://blog.logrocket.com)上。