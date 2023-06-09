# 范围关闭

> 原文：<https://dev.to/ajahso4/scoped-closures-dde>

JavaScript 是一种容易掌握的很酷的语言，它有许多奇怪的地方，这也解释了为什么一些作者会写关于这种语言的优秀部分的书。在所有这些方面，这种语言的酷之处在于，你可以在不理解这种语言的复杂性，甚至不在乎其中的古怪之处的情况下，很快变得富有成效(可能直到它咬你一口)。

然而，在本文中，我打算帮助 JavaScript 开发人员理解作用域和闭包的概念。就像我在“你不了解 JavaScript”系列中读到的那样，正确理解这两个概念将会提高你对这门语言的掌握，因此，我将尝试给出我的 2 美分，以展示我对这些概念的理解，最重要的是，帮助像我一样正在寻求真正理解这门语言的人。

首先，我们将从**范围**的概念开始。对我来说，Scope 可以被看作是一个具有不同层级的组织。想象一个拥有全组织文档访问系统的组织。在不同的级别，对文档的访问有一定的级别，经理可以查看由其下属创建或处理的文档，但下属不能查看由其经理创建的文档。回到 JavaScript，其他函数内部的函数可以访问它们的父函数在外部作用域中创建的变量，但是父函数不能访问在内部函数中创建的变量。我们来看一个例子:

```
 var outerFunction = function(x,y){
                            var x = x;
                            var y = y;
                            var adder = function (){
                                var z = x + y;
                                console.log(z);
                            }
                            adder();
                            return z;
                       }
   outerFunction(3,2) // we see 5 in the console and Uncaught ReferenceError: z is 
                     //  not defined with the stack trace 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，组织中的一线员工无权访问高层正在讨论的顶级信息，除非他们被明确告知，在我们的情况下，这将通过加法器函数的显式返回语句以及相应的变量 z 的赋值来实现。因此，如果我们试图复制一线员工可以看到顶级管理决策的情况，代码将如下所示:

```
 var outerFunction = function(x,y){
                            var x = x;
                            var y = y;
                            var adder = function (){
                                var z = x + y;
                                console.log(z);
                                return z;
                            }
                            var z = adder();
                            return z;
                       }
   outerFunction(3,2) // we see 5 twice in the console twice from the log and 
                      // return statements respectively. 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们来谈谈**闭包**的概念。闭包之所以酷，是因为它可能与这样一个事实有关:即使信息已经从公共空间移走或者原始信息生成者的工作已经完成，我们仍然可以访问信息。让我们回到我们的组织结构类比。当一线工人从现场生成数据，清理数据，并可能使数据看起来像样并将其传递给经理时，一线工人的工作可能已经完成，但经理仍然可以访问创建的文档，并可以按照他想要的方式解释它。回到 JavaScript，闭包为嵌套函数提供了一种途径，即使在父函数完成调用栈之后，它仍然可以访问父函数生成的数据。尽管这仅在返回嵌套函数时有效。让我们来看看一些代码:

```
 var lineFunction = function(x,y){
                            var x = x;
                            var y = y;
                            var adder = function (){
                                var z = x + y;
                                return z;
                            }
                            return adder;
                       }
   var dataHandover = lineFunction(3,2) // this instantiates the line function
   dataHandover() // this returns 5 on the console. Notice it still had access to 
                   // the x and y arguments even after the lineFunction is called 
```

Enter fullscreen mode Exit fullscreen mode

对于**作用域**和**闭包**来说，所有这些的美妙之处在于它们可以嵌套到我们认为合适的任何级别，尽管需要控制以保持理解。请记住，代码主要是被阅读而不是被编写。

然而，有些人会问:这些概念的应用是什么？没有他们我也能过。当我们想在执行一些琐碎任务的主函数中实现子例程时，作用域是有用的，而闭包则适合于为我们的代码实现模块模式。闭包有助于实现私有和公共函数的概念。

我希望从你们那里看到更多的作用域闭包。感谢阅读。