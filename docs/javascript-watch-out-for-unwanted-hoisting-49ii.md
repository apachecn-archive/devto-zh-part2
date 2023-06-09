# JavaScript:小心不必要的提升！

> 原文：<https://dev.to/antogarand/javascript-watch-out-for-unwanted-hoisting-49ii>

# 挑战

让我以一个小挑战开始这篇文章。

用实际代码替换`// Your code here`，打印`Flag`！

```
function generateSecret() {
  return Date.now() + Math.random() * 10000;
}

const mySecretKey = generateSecret();

// Your code here

if (mySecretKey === 42) {
    console.log('Flag!');
} else {
    console.log('Bad secret!');
} 
```

Enter fullscreen mode Exit fullscreen mode

# 写完

为了打印旗帜，我们需要了解函数提升是如何工作的。

```
myFunction();

function myFunction() {
    console.log('My function was called!');
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段是有效的，并且将正确地打印`My function was called!`，即使这个函数是在被调用之后声明的。

这个工程多亏了[吊装](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)。

下面是 MDN 的一个快速定义:

> 例如，从概念上讲，提升的严格定义表明变量和函数声明被物理地移到了代码的顶部，但事实上并不是这样。相反，变量和函数声明在编译阶段被放入内存中，但是保持在您在代码中键入它们的位置。

这意味着前面的代码可以理解为:

```
function myFunction() {
    console.log('My function was called!');
}

myFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

函数声明和定义在实际代码执行之前被移动，这让我们可以在声明函数之前使用它们。但是如果我们声明同一个函数两次会发生什么呢？

```
function myFunction() {
    console.log('My function was called!');
}

myFunction();

function myFunction() {
    console.log('My *evil* function was called!');
} 
```

Enter fullscreen mode Exit fullscreen mode

剧透预警:邪恶功能被调用！

一旦吊起，前面的代码可以理解为:

```
function myFunction() {
    console.log('My function was called!');
}
function myFunction() {
    console.log('My *evil* function was called!');
}

myFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

因为`myFunction`的最后一个声明是邪恶的，所以所有对`myFunction`的调用都将指向邪恶的函数！

# 解

为了解决这个问题，我们只需要重新声明`generateSecret`函数。

```
function generateSecret() {
  return Date.now() + Math.random() * 10000;
}

const mySecretKey = generateSecret();

// Your code here
function generateSecret() {
  return 42;
}

if (mySecretKey === 42) {
    console.log('Flag!');
} else {
    console.log('Bad secret!');
} 
```

Enter fullscreen mode Exit fullscreen mode

# 参考文献

MDN: [提升](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)

MDN: [功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)

媒介:[提升你的 JavaScript 提升知识](https://medium.com/byteconf/hoist-your-knowledge-of-javascript-hoisting-a3db96c029d5)

<small>[原创于 GitLab](https://gitlab.com/AntonyGarand/javascript-quirks/blob/master/writeups/1-hoisting.md)</small>