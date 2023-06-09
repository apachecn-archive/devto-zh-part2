# Es6 牛逼(上)

> 原文：<https://dev.to/ogwurujohnson/es6-awesomeness-part-i-7d2>

ECMAScript 标准的第六版，即 Es6，为编写复杂的应用程序(包括类和模块)增加了重要的新语法。一些新的语法和概念我们将在我学习的 3 部分文档中讨论，尽可能使它们简单。

对于这一部分，我们将了解以下概念和语法:

1.  模板字符串
2.  &常量
3.  为..关于
4.  箭头功能
5.  传播算子

**1。模板字符串:**
Es6 有两种新的文字:*模板文字*和*标记模板文字*。*模板文字*允许使用多行字符串和表达式。
`
***模板文字***
用反勾代替双引号或单引号括起来，表达式可以用美元符号和花括号${..}.下面是工作中模板文字的例子；

```
const firstname = 'johnson';
console.log(`Hello ${firstname},
How are you?`); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码相当于下面的 Es5 标准代码；

```
var firstname = "Johnson";
console.log('Hello '+firstname+', \n How are you?'); 
```

Enter fullscreen mode Exit fullscreen mode

比较这两段代码，我们不得不承认 Es6 为我们简化了很多。下面是另一个展示 Es6 模板文字的例子。

```
const  currentYear = 2018;  
let DOB = 1980;

console.log(`Subtracting your DOB ${DOB} from the current year, 
your are ${currentYear-DOB}years old`); 
```

Enter fullscreen mode Exit fullscreen mode

***标记的模板文字；*T3】**

```
let a = 1;
let b = 2;

let yourString = function(strArray, ...val){
    console.log(strArray);
    console.log(val);
}

yourString `Hello ${a},${b} world man ${a+b}`; 
```

Enter fullscreen mode Exit fullscreen mode

*标记模板文字*，是模板文字的更高级形式。通过它们，您可以使用函数修改模板文字的输出。第一个参数包含一个字符串数组(在上面的示例中为“Hello”、“world”、“man”和“)。第二个参数和第一个参数之后的每个参数都是经过处理的(有时称为熟的)替换表达式的值(“1”、“2”、“3”)，最后函数会返回您操作过的字符串。

Es6 中的其他一些字符串函数有，`include`，`startsWith`，`endsWith`，我会用它们的用法来解释它们，所以你可以自己复制并在你的控制台上运行它们。

```
var stringWord = "Hello World";
stringWord.startsWith("Hell");

var stringWord = "Hello world";
stringWord.endsWith("rld"); 
```

Enter fullscreen mode Exit fullscreen mode

运行以上不同的代码返回布尔值`true`。

```
var anyArray = [1,2,4];
anyArray.includes(1); 
```

Enter fullscreen mode Exit fullscreen mode

**2。让&常数:**

***Let:***
`let`语句声明了一个块作用域局部变量，而不是像`var`那样的函数级作用域。

```
let year = 2018;
let dob = 1970;

function calcAge(){
    let age = year-dob;
    if (age > 19){
        let age = "You re young";
        console.log(age);
    }
    console.log(age);
}

calcAge(); 
```

Enter fullscreen mode Exit fullscreen mode

在同一个函数或块范围内重新声明同一个变量会引发一个`SyntaxError`，而且你不能在它的范围之外使用一个变量；比方说，试图在`calcAge`函数之外访问`age`将会返回一个`undeclared variable Error`。

***常量:***

`const`的工作原理和`let`一样，但是你声明的变量必须立即初始化，其值不能在事后更改。`const`声明创建了一个值的只读引用。记住，常量的值不能通过重新赋值来改变，也不能重新声明。

假设`const PI = 3.14`试图重新分配 PI，那么`PI = 3.12`将返回一个`uncaught type error`。就像`let`，`const`不能在它的作用域之外被访问，让我们举一个例子来说明这一点，并且请尝试自己运行这个代码来更好地理解。

```
 const PI = 3.14;
   function fun(){
        const PI = 3.121;
        if(true){
            const PI = 3.416;
            console.log(PI)
        }
        console.log(PI);
    }
    console.log(PI);
    fun(); 
```

Enter fullscreen mode Exit fullscreen mode

**3。为..of:**
`for-of`是 Es6 中的一个新循环，它取代了`for-in`和`forEach()`，并支持新的迭代协议。下面突出显示了语法；

```
for(variable of iterable){
    statement
} 
```

Enter fullscreen mode Exit fullscreen mode

迭代数组；

```
let values = [1,34,20];
for (let value of values){
    console.log(value);
} 
```

Enter fullscreen mode Exit fullscreen mode

迭代一个字符串；

```
let color = "red";
for(let item of color){
    console.log(item);
} 
```

Enter fullscreen mode Exit fullscreen mode

**4。箭头功能:**

箭头函数总是匿名的。示例如下:

*不带任何参数的函数:*

```
var intro = () => "Welcome"; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码等效于下面的代码:

```
var intro = function(){
    return "welcome";
} 
```

Enter fullscreen mode Exit fullscreen mode

*单参数函数:*

```
var multiplyBy2 = value1 => value1 * 2;
console.log(multiplyBy2(4)); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码等效于下面的代码:

```
var multiplyBy2 = function(value1){
    return value1 * 2;
}
console.log(multiplyBy2(4)); 
```

Enter fullscreen mode Exit fullscreen mode

*多参数函数:*

```
var addBy = (value1,value2) => value1 + value2;
console.log(addBy(10,30)); 
```

Enter fullscreen mode Exit fullscreen mode

**5。扩展运算符:**
扩展语法允许表达式在需要多个参数(用于函数调用)或多个变量(用于析构赋值)的地方进行扩展。

*举例:*
数组作为函数参数的常见用法如下:

```
function myFunctions(x,y,z){
    console.log(x);
    console.log(y);
    console.log(z);
}
var args = [0,1,2];
myFunctions(args); 
```

Enter fullscreen mode Exit fullscreen mode

使用`Es6 spread`,您现在可以将上面的内容写成:

```
function myFunctions(x,y,z){
    console.log(x);
    console.log(y);
    console.log(z);
}

myFunctions(...args); 
```

Enter fullscreen mode Exit fullscreen mode

今天到此为止，伙计们。