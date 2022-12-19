# JavaScript 中的三元运算符

> 原文：<https://dev.to/iandavid/ternary-operators-in-javascript-4inl>

你厌倦了在编码时重复自己吗？你还在使用传统的‘if…else’模块吗？如果我告诉你，你可以用 4 行代码写 15 行代码呢？现在就打电话给 1-800 三元运算符。

在计算机编程中，**？:**是一个三元运算符，是几种编程语言中基本条件表达式语法的一部分。

三元运算符是 if…else 语句的简写版本。它是 JavaScript 中唯一接受三个操作数的条件运算符。

三元运算符的基本语法是**条件？表达式 1:表达式 2**

*   如果条件是要测试/评估的值，

*   **表达式 1** 可以是要执行的任何类型的值**如果条件为真**

*   **表达式 2** 可以是要执行的任何类型的值**如果表达式 1 为假**即通常称为‘else’的回退值

*   **“？”**表示**“如果”**，**::**表示**“否则”**

让我们看一个例子

如果我们使用 if…else 语句来决定一个人的年龄是否允许开车，它看起来像这样

```
var age = 18;

if (age >= 16) {
   alert("You're allowed to drive!");
}

else {
   alert("You should be 16 to drive!");
}

// "You're allowed to drive!" 
```

Enter fullscreen mode Exit fullscreen mode

使用三元运算符，相同的代码将如下所示

```
var age = 18;

alert (age >= 16) ? "You're allowed to drive!" : "You should be 16 to drive!"

// You're allowed to drive! 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。这段代码将翻译成**如果变量“年龄”大于或等于 16，浏览器应该警告“你可以开车了！”，否则“你应该满 16 岁才能开车！”应该警惕！**

采用三进制的另一个原因是它的灵活性和小型化，可以放在你代码的任何地方。例如，如果您想将字符串的结果直接附加到一个字符串，您可以很容易地做到这一点，而不必单独声明您的条件。

```
var isMember = true;
'The fee is ' + (isMember ? '$2.00' : '$10.00'); // The fee is $2.00 
```

Enter fullscreen mode Exit fullscreen mode

如果 isMember 为真，“费用为$2.00”，否则“费用为$10.00”。

你猜怎么着？！还可以将三元运算的结果赋给变量。让我们使用前面使用的相同的驾驶示例。

```
var age = 18;
var canDrive = (age >= 16) ? "You're allowed to drive!" : "You should be 16 to drive!";
console.log(canDrive); // "You're allowed to drive!" 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将结果保存在一个变量中，稍后在控制台中显示它。

三元运算符和传统的 if…else 块中的一样多。多个三元运算符可以链接在一起，形成我们所说的“IF…ELSE IF…ELSE”块。

```
var year = prompt('Which year was the 2018 World Cup?', '');
alert(year < 2018) ? 'Too early' : (year > 2018) ? 'Too late' : 'Exactly!'; 

// Exactly! 
```

Enter fullscreen mode Exit fullscreen mode

如果年份小于 2018 年，上面的代码块将进行翻译，浏览器应该警告“太早”，否则，如果年份大于 2018 年，将显示“太晚”，否则，如果年份不大于或小于，即等于，则将显示“正好”。

也可以嵌套 if 语句:

```
var statement1 = true;
var statement2 = true;

var check = statement1 ? (statement2 ? "True, Yes!" : "True, False!") : 'False';

console.log(check); // True, Yes! 
```

Enter fullscreen mode Exit fullscreen mode

我们的眼睛垂直扫描代码，缩进和空格在帮助我们轻松阅读代码方面发挥了很大的作用。三进制中不排除它们，自由空格不影响你的代码。

```
var stop = false, age = 23;

age > 18 ? (
    alert('OK, you can go.')
) : (
    alert('Sorry, you are much too young!')
); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将和任何三元求值或任何 if…else 操作一样有效。

每种情况下也可以有多个操作，并用逗号分隔。

```
var age = 16;

var auth = age > 18 ? (
    alert('OK, you can go.'), 
    // alert returns "undefined", but it will be ignored because
    // isn't the last comma-separated value of the parenthesis
    'APPROVED' // the value to be assigned if age > 18
) : (
    alert('You are much too young!'),
    alert('Sorry :-('),
    // etc. etc.
    'DISAPPROVE' // the value to be assigned if !(age > 18)
);

location.assign(auth); // "DISAPPROVE" 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，三元运算可用于函数返回值。例如，如果我们要编写一个函数来返回值，以确定克里斯·帕拉特是否是漫威宇宙的成员；

```
var marvel = true;

function chrisPratt(marvel) {
  if (marvel === true){
return "I am Star-Lord!";
}
else {
return "Have you watched Guardians of the Galaxy?";
}

}
var output = chrisPratt(marvel);
console.log(output); // "I am Star-Lord!" 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们多次使用 return，并且不得不编写 if…else 语句并将它们的值嵌入到花括号中，这很好。但是可以使用三元运算符来缩短它。上述相同的代码将在三元运算中进行同样的计算；

```
var marvel = true;

function chrisPratt(marvel) {
  return (marvel === true) ? "I am Star-Lord!" : "Have you watched Guardians of the Galaxy?";
}
var output = chrisPratt(marvel);
console.log(output); // "I am Star-Lord!" 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:三元运算中使用的所有变量都应该在运算创建之前定义。

**还有一点，建议不要嵌套三元运算符，因为可能很难理解。**

> 优秀的程序员编写人类能够理解的代码。所以最好不要过度使用这些术语，而是在特定的情况下使用它们。

对于大多数开发人员和不了解的人来说，三元运算符可能看起来晦涩难懂，但是它是重构代码的一个很好的方法。经过足够的练习，无论何时你在你的代码或其他开发者的代码中看到它，你都能够理解它。