# JavaScript 中不同类型的函数

> 原文：<https://dev.to/jaamaalxyz/different-type-of-function-in-javascript-364l>

# JavaScript 中不同类型的函数

[![leaf](img/1f4d828f2cc30ae63d1b659dac4177b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lk3IcSk6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ttlwr60fom463mm0vzcm.png) 
照片由[马库斯·斯皮斯克](https://unsplash.com/photos/QVVXPNbjLrI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/)

JavaScript 函数是为执行特定任务而设计的代码块。

## MDN 表示:

函数是 JavaScript 的基本构件之一。函数是一个 JavaScript 过程，即执行任务或计算值的一组语句。要使用一个函数，你必须在你希望调用它的范围内的某个地方定义它。

一个 JavaScript 函数在“某物”调用它时被执行。

### 举例:

```
function square(x) {
  return x * x;
}
square(10); // 100 
```

Enter fullscreen mode Exit fullscreen mode

### 基本语法:

```
function validFunctionName(parameter) {
  return statement;
} 
```

Enter fullscreen mode Exit fullscreen mode

一个函数可以有多个参数，也可以没有参数。在下面的例子中，bark 没有列出任何参数名，而 power 列出了两个:

`bark( )`

```
function bark() {
  return "woof-woof";
}
bark(); //   woof-woof 
```

Enter fullscreen mode Exit fullscreen mode

`power( )`

```
function power(base, exponent) {
  let result = 1;
  for(let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
}
power(2, 10); // 1024 
```

Enter fullscreen mode Exit fullscreen mode

### 函数表达式:

函数表达式定义了一个命名或匿名函数。匿名函数是没有名字的函数。

```
var fullName = function(firstName, lastName) {
 return `${firstName}  ${lastName}`;
}
fullName("Jamal", "Uddin"); // Jamal Uddin 
```

Enter fullscreen mode Exit fullscreen mode

### 箭头功能:

箭头函数表达式是编写函数表达式的一种较短的语法。箭头函数不会创建自己的值。

我们可以用多种方式编写箭头函数:

#### 第一:

它只是看起来像一个常规的函数表达式，但有箭头`(=>)`键。

```
const double = (value) => {
  return value * 2
}
double(10); // 20 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二:

省略返回关键字

```
const double2 = value => value * 2;
double2(10); // 20 
```

Enter fullscreen mode Exit fullscreen mode

#### 第三:

如果我们函数没有参数

```
const noise = () => console.log("Pling");
noise(); // Pling 
```

Enter fullscreen mode Exit fullscreen mode

##### 或

```
const noise2 = _ => console.log("Pling");
noise2(); // Pling 
```

Enter fullscreen mode Exit fullscreen mode

#### 第四:

如果我们有两个或更多的参数，你可以必须使用括号

```
const addAll = (x, y, z) => x + y + z;

addAll(10, 20, 30); // 60 
```

Enter fullscreen mode Exit fullscreen mode

#### 第五:

我们可以在参数
中使用默认值

```
const multiply = (a = 2, b = 3, c = 1) => a * b * c;
multiply(2, 2, 2); // 8
multiply(2, 2);    // 4
multiply(3);       // 9
multiply();        // 6 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 对于传递给函数的参数数量非常宽容。如果传递太多，多余的会被忽略。如果传递的参数太少，缺少的参数就会被赋值为 undefined。

#### 返回:

记住，return 关键字只能在函数内部使用。让我们看另一个例子。

```
function returnOnlyOnce(){
  return "Hello";
  return "Goodbye";
}
returnOnlyOnce(); // "Hello" 
```

Enter fullscreen mode Exit fullscreen mode

从这个例子中我们看到，return 关键字在一个函数中只能执行一次。一旦它被执行，函数就完成了，不会执行其他代码行。

#### 函数速记方法:

速记方法定义可以用在对象文字和 ES6 类的方法声明中。我们可以使用一个函数名来定义它们，后面是一对括号中的参数列表(para1，...，paramN)和一对花括号{...}界定身体陈述。

以下示例在对象文字中使用速记方法定义:

```
const fruits = {  
  items: [],
  add(...items) {
    this.items.push(...items);
  },
  get(index) {
    return this.items[index];
  }
};
fruits.add('mango', 'banana', 'guava');  
fruits.get(1); // banana 
```

Enter fullscreen mode Exit fullscreen mode

水果对象中的`add()`和`get()`方法是使用短方法定义来定义的。这些方法通常被称为:`fruits.add(...)`和`fruits.get(...)`。

### 发生器功能:

ES6 以生成器(或生成器函数)的形式引入了一种处理函数和迭代器的新方法。生成器是一个函数，它可以中途停止，然后从停止的地方继续。简而言之，生成器看起来像一个函数，但它的行为像一个迭代器。

注意:async/await 基于生成器。点击阅读更多[。](https://tc39.github.io/ecmascript-asyncawait/)

#### 举例:

```
function * generatorFunction() { 
  yield 'Hello, ';
console.log('I will be printed after the pause');  
  yield 'World!';
}
const generatorObject = generatorFunction();
console.log(generatorObject.next().value);
console.log(generatorObject.next().value);
console.log(generatorObject.next().value);
// output should be following below.
// Hello, 
// I will be printed after the pause
// World!
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能同:新增功能

函数构造器创建一个新的函数对象。

```
var sum = new Function('a', 'b', 'return a + b');
console.log(sum(2, 6)); // 8 
```

Enter fullscreen mode Exit fullscreen mode

### 真相来源:

*   [MDN JavaScript 函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)
*   [雄辩的 JavaScript 函数章节](https://eloquentjavascript.net/03_functions.html)
*   [w3 学校](https://www.w3schools.com/js/js_function_definition.asp)

英语不是我的母语，这是我的第一篇英语文章，所以如果你发现任何错误，请原谅我，也请鼓励我写更多的文章。

编程快乐！:)

## 我是谁？

这是在孟加拉国达卡工作的软件开发人员贾马尔·乌丁博士。我喜欢学习新东西并与他人分享。玩切割技术是我的爱好，处理遗产是我的日常工作:)。在[推特](https://twitter.com/jamal_uddin95)和 [LinkedIn](https://www.linkedin.com/in/jamal-pb95/) 上联系我