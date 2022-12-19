# JavaScript 函数概述

> 原文：<https://dev.to/howtocodejs/an-overview-of-javascript-functions-47id>

[![image](img/1e618c6e33281fcb119e743433d058fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X_rTgn_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fs6fwvrjs1mqqujy8a9.png) 
*访 howtocodejs.com 以典随例*

我们可以整天沉浸在关于函数及其用途的诗意中。相反，让我们来探索有功能和没有功能的生活。

### 没有功能的生活

```
let pets = 35;
let owners = 15;
let petsPerOwner = pets / owners;
//======Pet Info Form
let answer = prompt("how many pets do you have?");
//============
 // update based on answer, add new owner
pets += answer / 1; //  coerce string into number
owners += 1; // register new owner
petsPerOwner = pets / owners;

//test
`There are now ${petsPerOwner} pets per owner at Pet Nirvana `; 
```

Enter fullscreen mode Exit fullscreen mode

那个比这个更容易读吗？

### 有功能的生活

```
let pets = 35;
let owners = 15;
let petsPerOwner = average(pets, owners);
let answer = prompt("how many pets do you have?");

registerPets(answer);
registerOwner();
updateAvg(); // update based on answer, add new owner
console.log(`There are now ${petsPerOwner} pets per owner at Pet Nirvana `);

function average(total, number){
  return total / number;
}
function registerPets(newNum){
  pets += Number(newNum); // register new pet(s)
}
function registerOwner(){
  ++owners;
}
function updateAvg(){
  petsPerOwner = Math.ceil(average(pets, owners)); // find new average, round up
} 
```

Enter fullscreen mode Exit fullscreen mode

除了易读性，您还可以看到，当我们拥有所有这些内置功能时，我们的工作会变得容易得多。`Math.ceil`取整，`log()`帮助我们调试代码。此外，请注意第一个示例仍然使用函数来满足纯粹的需求。

没有函数就没有 JavaScript，至少没有我们所了解和喜爱的 JavaScript 的所有好的部分。

## 解剖功能

```
function multiply(x, y){
  return x * y;
}

function // keyword for decleration
multiply // function name
(x,y)   // parameters
return x * y; // a return statement allows
              //the function to produce value 
```

Enter fullscreen mode Exit fullscreen mode

函数有一个或多个参数。我们可以给它们起任何我们喜欢的名字，就像变量一样。不过，我们应该把参数看得更像引用，而不是存储。我们告诉函数，我们希望用户将一些变量或数据类型插入到这个空间中。然后，我们对函数体内的参数名进行操作。

多数情况下，您会希望确保返回预期的结果。不这样做将在调用函数时产生`undefined`。如果您打算使用函数来设置值，请包含 return 关键字。

## 返回

`return`语句可以*返回*任何数据类型。

数字:

```
return 2; 
```

Enter fullscreen mode Exit fullscreen mode

字符串:

```
return "hello"; 
```

Enter fullscreen mode Exit fullscreen mode

Null:

```
return null; 
```

Enter fullscreen mode Exit fullscreen mode

未定义:

```
return undefined; 
```

Enter fullscreen mode Exit fullscreen mode

数组:

```
return [1,2,3]; 
```

Enter fullscreen mode Exit fullscreen mode

对象:

```
return {one: 1, two: 2, three: 3}; 
```

Enter fullscreen mode Exit fullscreen mode

功能:

```
return function(){
  return "I'm in a function";
} 
```

Enter fullscreen mode Exit fullscreen mode

## 调用函数

您可以通过在函数名中添加`()`来调用函数。如果函数需要参数，您必须输入它们，否则会出现错误。

```
function multiply(x, y){
  return x * y;
}
multiply(2,2); // 4 
```

Enter fullscreen mode Exit fullscreen mode

你可以在声明之前调用一个函数，它仍然可以工作。这叫吊装。

```
multiply(2,2); // 4

function multiply(x, y){
  return x * y;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 功能符号

当一个地标或一件事物在任何人类语言中都很重要时，通常会有不止一种方式来命名。

> 有趣的事实:在经典的阿拉伯语中，给骆驼命名有上百种方式。

类似地，函数对 JavaScript 如此重要，以至于根据使用它们的上下文，有许多名称。

### 函数声明

你有可靠的**函数声明** :

```
function greet(){
  return 'hello';
}

// we can the call or invoke this functions

greet(); // 'hello' 
```

Enter fullscreen mode Exit fullscreen mode

### 函数表达式

你还有一个**函数表达式**。它被称为函数表达式，因为你在给一个变量赋值函数:

```
let greet = function(){
  return 'hello';
}

// we can still call or invoke this functions

greet(); // 'hello' 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的一件重要事情是提升不支持函数表达式。

```
greet(); // undefined

let greet = function(){
  return 'hello';
} 
```

Enter fullscreen mode Exit fullscreen mode

### 匿名函数

被称为**匿名函数**后没有名字的函数关键字(`function()`)。Es6 引入了一种编写匿名函数的新方法。除了使用 function 关键字，您还可以删除它，并将箭头操作符`=>`添加到括号中。

```
let greet = ()=>{
  return 'hello';
} 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，引入语法上的差异是为了满足喜欢编写最少代码的纯粹主义者。不过，arrow 函数确实引入了自动绑定。我们将在后面向您展示什么是自动绑定，而不是变得过于专业。

**匿名函数**用途广泛。您可以将它们设置为对象文字中的一个键的值:

```
let person = {
  name: "Mark",
  greet: function(){
    return 'hello' + '  ' +  this.name;   
  }
}; // end of object literal

person.greet(); 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:`this`是指我们匿名函数内的`person`。我们可以很容易地写出`person.name`。

### 回调函数

匿名函数也可以放在参数中。这样做将匿名函数变成了所谓的**回调**。

```
//here's a function expression
let greet = (callback, times)=>{
  for(let cnt=0; cnt < times; cnt ++){
      console.log(callback()); //it doesn't return.
                              //This will cause a side effect
  }
}

//here's our anonymous func AKA callback
greet(()=>{return 'hello'}, 3);
//we could have written it like this:
greet(function(){return 'hello'}, 3); 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:还记得我们讨论过函数的解剖吗？当你定义一个函数时，你创建了一个模型。回调正好利用了这一点，因为我们可以等待函数的到来。我们通过将`()`附加到我们的参数名来告诉解释器我们希望在函数到达时调用它。

### 关闭

函数中的函数被称为**闭包** :

```
// We have two functions. One is named outie and the other is named closure *wink* *wink*
function outie(){
  // this is closure's first and only outer scope
  function closure(){
   // this is closure's local scope
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你一直在玩回调，你可能已经正确地猜到回调也是一个结束。在其存在期间的某个时刻，它会在另一个函数中被调用。

**上下文:**’
既然我们已经开始嵌套函数，我们应该处理上下文。函数创建它们自己的上下文，这会影响关键字`this`，但是如果我们在匿名函数中编写闭包，`this`将引用我们的函数。因此，我们会变得不明确。

这里有一个例子:

```
 let person = {
  name: "Mark",
  greet: function(){    
    return function(){
          return 'hello' + '  ' +  this.name;  
    }      
  }
}
// double invoke ()() can invoke a returned closure
person.greet()();// >'hello undefined' 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，开发人员只需将`this`设置为一个变量来保存上下文。换句话说，我们正在*绑定*这个。开始了解自动绑定可能会带来什么？:

```
//code excerpt
greet: function(){
  let self = this;   
  return function(){
        return 'hello' + '  ' +  self.name;  
  }      
}
//end of excerpt 
```

Enter fullscreen mode Exit fullscreen mode

另一种解决方案是在函数的右括号上显式调用`bind(this)`。

```
//code excerpt
greet: function(){
  return function(){
        return 'hello' + '  ' +  this.name;  
  }.bind(this)      
}
//end of excerpt 
```

Enter fullscreen mode Exit fullscreen mode

看起来很丑，但是很管用。

> 专业提示:还记得新的`()=>`语法吗？上面的例子很好地说明了为什么我们需要自动绑定。之前，你必须记得像我们之前做的那样将`this`绑定在一个变量中。现在，你只需使用新的语法和，哇！，你就有了一个正常运转的`this`关键词。通过重写闭包来尝试一下。

最终的解决方案是使用 Es6 箭头功能。

```
//code excerpt
greet: function(){
  let self = this;   
  return ()=>{
        return 'hello' + '  ' +  this.name;  
  }      
}
//end of excerpt 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:在外部匿名函数上使用 arrow 函数会破坏上下文。因为 arrow 函数是自动绑定的，所以您将把`this`绑定到`person`对象之外的上下文。于是，`this.person`就不再起作用了。

### 生活

调用自身的函数称为**立即调用的函数表达式(life)**。

```
(function(){
  return 'hello'; //'hello'
}()); 
```

Enter fullscreen mode Exit fullscreen mode

你仍然可以做任何你可以用其他函数做的事情。您可以设置参数并使用“invoker”`()`输入数据。

```
(function(name){
  return name;   // 'hi'
}("hi")); 
```

Enter fullscreen mode Exit fullscreen mode

你可以给一个变量设置一个生命，但是你必须声明它的名字。不过，您不必调用它。

```
var greet =
(function(name){
  return name;   
}("hi"));

greet // 'hi' 
```

Enter fullscreen mode Exit fullscreen mode

## 功能躁作

我们可以使用 IFFE 和闭包，结合匿名函数，来创建一个 android。

```
//function expression
let android = (function(){
    //==private
    this.name = "Mark VI";
    //declaration
    function addStrings(){
       return "hello" + "  " + this.name;
    }
    function setName(name){
      this.name = name;
    }
    //==public: we're just returning an object.
    return {  //anonymous functions
       setName:(name)=>{
          return setName(name);
        },    
        greet: ()=>{
            return addStrings();
        }
    }
}());//IIFE

android.setName("Raj");
android.greet(); //'Hello, I'm Raj' 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码利用了函数提供给我们的所有功能来生成一个可运行的对象。它管理自己的状态，这意味着我们所做的任何更改都将被保存。所以，如果我们设置了一个新名字，并告诉机器人来问候我们，它会用这个新名字来问候我们。这是一些强大的东西！我们将在另一章学习更多关于面向对象编程的知识。

> 注意:如果开发人员想让他们的代码在不被事件触发的情况下运行，他们通常会用 IFFE 来包装 JavaScript 代码。

## 总结

可能很难跟踪所有这些不同类型的函数，所以让我们列出不同的函数类型。

*   声明的函数
*   匿名函数
*   回收
*   关闭
*   立即调用的函数表达式

> 挑战:写一个利用所有这些不同功能的程序