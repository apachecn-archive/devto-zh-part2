# JS 中的名称空间

> 原文：<https://dev.to/muhammadridwan/namespace-in-js-5dbj>

不幸的是，JavaScript 默认不提供名称空间。因此，我们在 JavaScript 中创建的任何东西
(函数、方法、对象、变量)都是全局的，而
我们继续通过添加更多的东西来污染全局名称空间。

JavaScript 缺少名称空间。然而，我们可以使用对象，生命来创建名称空间。

命名空间的优点是它们组织 JavaScript 代码，使 JavaScript 代码可维护，不创建不必要的全局变量和函数。

## 没有命名空间的问题

在这个例子中，我们将定义两个同名的函数。看看下面的例子，我们已经定义了 fun1()两次，然后我们调用 fun1()，我们看到最新的函数被执行。

JavaScript-演示

```
 function fun1() {
        console.log("I am first fun1");

    }
    function fun1() {
        console.log("I am second fun1");
    }
    fun1(); 
```

Enter fullscreen mode Exit fullscreen mode

输出:
我是第二 func1

## 利用命名空间解决问题

正如我们前面解释的，名称空间解决了名称冲突问题。在这个例子中，我们将在多个函数中共享同一个函数名，但是它们将属于不同的名称空间。下面来看看以下两种方法:

## 1。使用对象文字符号

这里，我们将变量和函数包装在充当名称空间对象文字中。我们通过符号访问包装的变量和函数:

对象名称.变量名称；
object _ name . function _ name()；

JavaScript-演示

```
 var myfunctionCollection1 = {
        fun1: function () {
              console.log("I am first fun1");             
        }        
   }
    var myfunctionCollection2 = {
        fun1: function () {
              console.log("I am second fun1");

        }
   }
    myfunctionCollection1.fun1();
    myfunctionCollection2.fun1(); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

我是第一个有趣的人

## 2。使用 IIFE(立即调用的函数表达式)

生命是一个包含在一对括号中的匿名函数，可以立即调用。这对括号为其中的所有代码创建了一个局部范围，并使匿名函数成为一个函数表达式。这证明了“立即调用函数表达式”这个名称的合理性。

最外面的一对括号把里面的所有东西都变成了表达式，因为括号不能包含 JavaScript 语句。函数定义后的另一对括号会立即调用函数。让我们看一个例子。

JavaScript-演示

```
 (function() {
   function fun1(){
   console.log("I am first fun1");
   } fun1();
   }());

   (function() {
   function fun1(){
   console.log("I am second fun1");
   } fun1();
   }()); 
```

Enter fullscreen mode Exit fullscreen mode

输出:
我是第一 fun1
我是第二 fun1

## 3。使用 block 和 let 声明(或 const 声明):

在 ES5 中，如果您想将变量的范围限制在一个块内，您必须使用一种称为 IIFE(立即调用的函数表达式)的模式。在 ECMAScript 6 中，您可以简单地使用一个块和一个字母声明(或一个常量声明):

JavaScript-演示

```
 {
  let temp= function fun1(){
  console.log("I am first fun1");
  } 
  temp();
 }
  //temp(): ReferenceError: temp is not defined.

 {
  let temp= function fun1(){
  console.log("I am second fun1");
  } 
  temp();
 }
  //temp(): ReferenceError: temp is not defined. 
```

Enter fullscreen mode Exit fullscreen mode

输出:
我是第一 fun1
我是第二 fun1

我希望你喜欢这篇文章。
问候。