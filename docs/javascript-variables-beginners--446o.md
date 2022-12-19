# JavaScript:变量(初学者)

> 原文：<https://dev.to/banesag/javascript-variables-beginners--446o>

在数学中，程序设计语言中的变量是用来引用存储起来以备后用的值，在这种情况下，变量存储在程序的内存中。

**声明和分配变量**

变量必须在使用前声明。在 JavaScript 中，关键字“const”和“let”用于声明变量。给我们的关键字赋值的方法是在关键字后面加上' = '(等号)运算符。

**关键字 const and let**

重要的是要注意，当变量不会被重新赋值给另一个值时，使用关键字“const ”,关键字“let”可以在程序内部重新赋值。

**例如:**

这个例子展示了我们如何声明变量名并给它分配一个字符串‘banesa’。

```
const name = 'banesa'; // In this case the value ('banesa') will not be assigned to another string 
```

**另一个使用关键字‘let’的例子:**

这个例子展示了我们如何声明变量 score 并赋值‘0’(数字)。

```
let score = 0; // The assigned value can change during the program 
```

这是通过使用原始数据实现的，但是如果变量引用非原始数据类型，比如数组、函数或对象，那么关键字 const 将是可变的。也就是说，对象内部的数据是可以改变的。

**举例:**

```
const name = { value: ‘Banesa’}; // the object is indicated by using curly brackets
name.value = 'Barbara'; //change the value through the use of object notation (.)
<< 'Barbara' // the output will be 'Barbara' since we allowed to change the variable by been inside an object 
```

因为变量使用了花括号，它允许将变量值转换为对象，这将需要一个额外的步骤，即调用新变量，该变量将在程序中使用对象符号(name.value)重新分配，这两个规则/方法的组合将允许您重新分配仍然使用 const 的非原始数据。

即使 const 的使用似乎是一种限制，它也可以帮助你避免程序中的错误，因为它不会给同一个变量赋值两次。