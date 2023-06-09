# Javascript 中的比较和逻辑运算符

> 原文：<https://dev.to/christiangraves/comparison-and-logical-operators-in-javascript-lda>

比较和逻辑操作符是大多数编程语言的两个主要特性。它们与条件语句一起构成了手机应用程序、程序或网站功能交互的基本构件之一。它们基于数学中相同的逻辑和比较运算符，只有一点不同。我在下面列出了逻辑和比较操作符，并将通过一些例子来说明如何在 JavaScript 编程中使用它们。

```
 //Comparison Operators
//Equal to and strict equal to
==
===
//Not equal to and strict not equal to
!=
!==
//Greater than and greater than or equal to
>
>=
//Less than and less than or equal to
<
<=

//Logical Operators
//And
&&
//Or
||
//Not
! 
```

正如您在上面看到的，有两种不同的运算符用于等于和不等于。“等于”和“不等于”运算符允许您比较两个变量的值，而不考虑变量的类型，并根据匹配值返回 true 或 false。严格的等于运算符要求变量和变量值匹配才能返回 true。如果值或类型不匹配，则严格不等于返回 true。您可以在下面的示例中看到这两种情况，以及大于号和小于号运算符的示例和一个示例条件语句。

```
let x = 3
let y = 4

x == 3 //true
x === “3” //false

x!=5 //true
x!== “5” //true
X!== 3 //false 

y > 5 //false
y >= 3 //true

y < 5 //true
y <= 2 //false

if (x === 3){
    /**comparison in if conditional
    is true, so perform some code here**/
} 
```

基本形式的逻辑运算符用于计算复合条件语句。它们将根据计算结果返回布尔值 true 或 false。JavaScript 允许不同类型和短路评估的返回，但那是另一篇文章。下面是两个逻辑运算符的示例。

```
 let x = 15
let y = 23

x > 12 && y < 25 // True
x < 2 && y >12 // False

x > 12 || y > 25 // True
x < 2 || y < 12 // False 

if (x < 45 && 32 > y){
    /**compound comparison in if conditional
    is true, so perform some code here**/ 
} 
```