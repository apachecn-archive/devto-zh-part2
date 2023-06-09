# JavaScript 变量:初学者指南

> 原文：<https://dev.to/howtocodejs/javascript-variables-the-beginners-guide-5eid>

[![image](img/bd61494f770e847b7d694249b39295e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x_r7QkT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hr6yrx7279yunlvkunk1.png)

你可以在 howtocodejs.com 上这节课

如果你曾经愉快地(或不愉快地)坐在代数 1 课上，你可能听说过变量。你知道，x + 4 = 6 和所有这些好东西。别担心。JavaScript 变量没那么可怕。你可以声明它们，这样你就知道它们到底是什么了。而且，看在上帝的份上，不要把你的变量命名为 x、y 或 z。给它们起一个易读的名字，这样当你完成编码的时候，你就不会盯着一个二次方程了。

让我们把数字、字符串和布尔值想象成短时间后会消失的快照聊天照片。他们不会停留足够长的时间来发挥作用。如果您想使用相同的数据，您必须在 JavaScript 文件中的其他地方重新输入。

想象一下不得不一遍又一遍地写这个公式:`1/2(60 * 120);`

还是这个真的很长的字符串:`"superkalafragilisticespialadocious";`。

变量允许我们保存数据，以便我们可以再次使用它。

让我们声明两个变量

```
var triangleArea, poppinsQoute; 
```

Enter fullscreen mode Exit fullscreen mode

现在来看一些要点:

关键字`var`创建了所谓的全局变量。就像在公共场合洗澡一样。每个人都能看到你。在 JavaScript 中，我们有块，比如邻域块。

它们通常是这样的:

```
// brackets enclose the block
{
  //...code goes here
}

if(true){
  // execute code in this block...
} 
```

Enter fullscreen mode Exit fullscreen mode

在 if 语句中，`var triangleArea;`和`var poppinsQoute;`可以在 if 的块中访问，因为我们在全局范围内声明了它们。

把范围想象成透视。从外面的角度来看，我们不能总是看到别人房子里面的东西。但是从里面，我们可以看到外面的一切。

我们不应该能够看到 if 语句内部的局部变量。局部变量是在块中声明的变量。

```
if(3==3){
 var number = 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:在这个例子中，我们同时声明并初始化了我们的变量。

还记得我们讨论过的全局变量和局部变量吗？您可能会猜测，如果我们在`if`块之外使用这个变量，我们应该会得到一个错误。

```
if(3===3){
 var number = 3;
}

console.log(number); // > 3 
```

Enter fullscreen mode Exit fullscreen mode

等等……我们仍然可以访问块外的变量。我们刚刚获得了 x 光视觉吗？所以所有关于局部变量和全局变量的讨论都是骗人的，对吗？

嗯，`var`关键字的问题是它喜欢将自己暴露在全局范围内。即使它被定义在一个块中，它仍然希望被每个人看到。唯一能驯服它的是一个函数。

```
function test(){
  var number = 3;
}
console.log(number); // number is not defined 
```

Enter fullscreen mode Exit fullscreen mode

> 专业提示:能够阅读和理解错误对于调试 JavaScript 应用程序至关重要。在我们的例子中，当我们试图使用一个没有声明的变量时，就会出现一个 **ReferenceError** 。如果我们试图使用一个超出范围的变量，我们也会得到一个 ReferenceError。

我们将在另一个时间讨论函数，但是现在你需要知道的是函数创建它们自己的作用域。它们就像高度安全的大厦。

函数如此安全，这很好，但我如何保证 if 语句的安全呢？

从 es6 开始，有了一种声明变量的新方法。Ecma 国际不时会为我们提供用 JavaScript 编码的新方法。他们想出的解决这个问题的方法是`let`关键字。

让我们使用它！

```
if(3===3){
 let number = 3;
}
console.log(number); // > number is not defined 
```

Enter fullscreen mode Exit fullscreen mode

太好了。我们出错了！现在我们真正有了全局和局部变量。

回到我们最初的变量声明，`var triangleArea, poppinsQoute;`，您可以看到我们能够通过用逗号分隔来同时声明多个变量。另外，看看变量名中的第二个单词是如何以大写字母开头的。这个惯例叫做**骆驼案**。坚持这种约定是有好处的，这样你的代码对你和其他可能有一天会看到你的代码的人来说都是易读的。

### 分配

你可以通过给变量赋值来初始化变量。

```
let poppinsQoute = "superkalafragilisticespialadocious";

console.log(poppinsQoute); // > "superkalafragilisticespialadocious" 
```

Enter fullscreen mode Exit fullscreen mode

您也可以将数据重新分配给同一个变量。这将覆盖以前的数据。

```
let poppinsQoute = "superkalafragilisticespialadocious";

poppinsQoute = "Mary Poppins, practically perfect in every way.";

console.log(poppinsQoute); // > "Mary Poppins, practically perfect in every way."; 
```

Enter fullscreen mode Exit fullscreen mode

### 康斯特:忠实的朋友

关键字`const`是声明变量的另一种方式。如果您想告诉自己和其他开发人员这个变量不应该改变，您可以使用这个关键字。也许我们应该重新声明我们的`triangleArea`变量。

```
const TRIANGLE_AREA = 1/2(60 * 120);
//If we try to re-assign it, we'll get an error
TRIANGLE_AREA = 1/2(60 * 180); // > Syntax Error
); 
```

Enter fullscreen mode Exit fullscreen mode

您不能将数据重新分配给常量。惯例是全部大写，这样 500 行之后，参与同一项目的其他人就会知道你的变量是一个常量。

## 可变仓库

我们对变量做了一些改动，但还没有把它们推到极限。让我们看看可以在变量中放些什么。

### 数字陈述

```
const TRIANGLE_AREA = 1/2(60 * 120); 
```

Enter fullscreen mode Exit fullscreen mode

### 用线串

```
let poppinsQoute = "superkalafragilisticespialadocious"; 
```

Enter fullscreen mode Exit fullscreen mode

### 布尔代数学体系的

```
let true_ = true; 
```

Enter fullscreen mode Exit fullscreen mode

等等。为什么下划线？你不能给一个变量命名任何已经被语言设计者命名的东西。这些不可触及的名称包括关键字和保留字。另外，确保你的变量名不要以数字开头:`123true`。

### 逻辑陈述

```
let check = (2==2&&3==3&&4==4); 
```

Enter fullscreen mode Exit fullscreen mode

括号是为了可读性。你可以把它直接插入到 if 语句中，它会很好地工作。

```
if(check){
 console.log("true");
} 
```

Enter fullscreen mode Exit fullscreen mode

> 专业提示:我不需要输入`check === true`,因为 if 语句会自动检查真值。

### 功能

```
var myFunction = function(){
  return 'hello';
} 
```

Enter fullscreen mode Exit fullscreen mode

函数也可以分配给变量。这些函数被称为匿名函数，因为它们没有命名。

### 数组

```
let myArray = [1,3,4,5]; 
```

Enter fullscreen mode Exit fullscreen mode

数组是数据的集合。

### 目标

```
let myObject = {me: "too", save: "me"}; 
```

Enter fullscreen mode Exit fullscreen mode

对象还保存数据集合。实际上，JavaScript 中的一切都是对象。是的，甚至是数据类型。

### 空

```
let empty = null; 
```

Enter fullscreen mode Exit fullscreen mode

Null 没有任何意义。如果不希望变量有类型或值，可以有意将变量设置为 null。

### 其他变量

```
let oldVariable = 25;
let newVariable = oldVariable * 2; 
```

Enter fullscreen mode Exit fullscreen mode

### 多个变量！

```
var varOne = varTwo = varThree = 1; 
```

Enter fullscreen mode Exit fullscreen mode

不要在家里尝试这个，因为它有不想要的副作用。变量赋值从右向左进行。所以为了让`varOne`最终获得值，其他两个变量被自动推送到全局范围。这意味着即使函数也不会将`varTwo`和`varThree`识别为局部变量。这就是所谓的泄漏，这是一个很大的禁忌

## 关闭注释

变量就在这里，所以确保你养成使用它们的习惯。每当你发现自己重复使用相同的数据类型时，试着把它放入一个变量中。你会很高兴你做到了。

> 挑战:写一个返回变量函数。不要害怕向谷歌寻求任何帮助。