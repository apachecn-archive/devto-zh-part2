# JavaScript: var，let，const

> 原文：<https://dev.to/mingt/javascript-var-let-const-41he>

在 ES6 中，引入了一种新的声明变量的方式——**const**和 **let** 。在 ES6 之前，开发者使用 **var** 来声明不同的变量。但是，它可能会导致潜在的问题或代码中隐含的错误。在这篇文章中，我将介绍 const，let，var 并给出一个清晰的比较。

## 对照表

|  | 定义变量 | 让 | 常数 |
| --- | --- | --- | --- |
| 重新分配 | O | O | X |
| 范围 | 功能范围 | 块范围 | 块范围 |
| 申报前参考 | O | X | X |

### 常数

**const** 在你不想重新分配变量的时候使用。一旦声明，它就保持**常数**。因此，如果我不需要重新分配变量，const 总是我的默认选项。这样，我们也可以避免偶尔在其他文件中声明相同变量名的情况。

### 让

当遇到我需要重新分配一个变量的情况时， **let** 比 ES6 之后的 var 更受欢迎。原因是 let 在块范围内，这意味着它只存在于自己的范围内。例如，

```
let foo='outside';
if(true){
    let foo = 'inside'
    console.log(foo);            //print inside

}
console.log(foo);                //print outside 
```

Enter fullscreen mode Exit fullscreen mode

在 if 条件之后，foo 等于‘outside’而不是 if 语句中的值(‘inside’)。

### 有一个“t0”

var 是 javaScript 中声明变量的老方法，现在它是定义变量最弱的关键字。由 var 声明的变量可能被重新分配，也可能不被重新分配，因为它在函数范围内，所以该变量可能用于也可能不用于整个函数。如果变量在 for 循环(块范围)内被意外地重新分配，那么事情就很容易出错。在这种情况下，人们可以覆盖父分配。例如，

```
var foo='outside';
if(true){
    var foo = 'inside'
    console.log(foo);            //print inside

}
console.log(foo);                //print inside 
```

Enter fullscreen mode Exit fullscreen mode

澄清一下，var 在函数范围内。所以如果人们在一个函数中重新赋值，函数外的变量不会改变。

```
var foo='outside';
var fooFunction = function(){var foo='inside'}
fooFunction();
console.log(foo);    //print outside 
```

Enter fullscreen mode Exit fullscreen mode

var 的另一个潜在问题是，它在赋值之前可以被引用。例如，

```
var x = 'foo';
console.log(x);         //foo
console.log(y);         //undefined, no errors!!
var y = 'late foo';     //late foo 
console.log(y); 
```

Enter fullscreen mode Exit fullscreen mode

在声明前使用变量没有错误。Javascript 引擎将上述脚本读取为

```
var x;             //variable declaration
var y;             //variable declaration
x = 'foo';         //variable initialization
console.log(x);    //foo
console.log(y);    //undefined, no errors!!
y ='late foo';     //variable initialization
console.log(y);    //late foo 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 Javascript 引擎只对**进行声明**而不是初始化。虽然人们可以通过使用“使用严格”来避免这个问题，但**常量**和 **let** 仍然更强大，可以减少潜在的错误，使代码更加清晰。