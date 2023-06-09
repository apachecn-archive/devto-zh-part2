# JavaScript 中的生活是什么？

> 原文：<https://dev.to/gyi2521/what-is-iife-in-javascript-o81>

我第一次在我的编码训练营课上听到“IIFE ”,它立刻让我想起了我姐姐的狗“Yeffi ”,在某些人类语言中它的意思是“漂亮”。

那么 JavaScript 语言中的生命是什么？

IIFE 代表立即调用的函数表达式。它是一个 JavaScript 函数，一定义就运行。

通常，当我们使用“函数声明”或“函数表达式”创建函数时，我们需要调用函数才能使用它。

```
Function Declaration:

function myFunction(p1, p2) {
    return p1 * p2;
}
alert(myFunction(4, 3));
//12

Function Expression:

let myFunction = function(p1, p2){
    return p1 * p2;
}
alert(myFunction(4,3));
//12

```

然而，在现实生活中，该函数被包装在一个括号中，这使得它成为一个后跟()的函数表达式，它告诉 JavasScript 编译器立即调用或调用。

```
(function() {
   let dName = "Yeffi";
   alert(dName);
}
)();
//Yeffi

```

那么我们为什么要利用生命呢？

主要是因为隐私。任何在生命内部声明的变量都不能从外部世界访问。

```
(function() {
   let dName = "Yeffi";
}
)();

console.log(dName);
//  Uncaught ReferenceError: dName is not defined

```

如果您试图在 IIFE 之外访问 dName 变量，您会得到如上所示的错误消息。生命中的所有变量都在函数的范围内。另外，它通过不创建命名函数来保护全局命名空间。当一个命名函数停留在全局命名空间中时，它可能会被意外地再次调用。然而，由于 IIFE 不是一个已命名的函数，它不可能被意外调用。这将避免任何潜在的安全隐患。因此，现在很多 JavaScript 库都在使用这种技术。

感谢阅读！