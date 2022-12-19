# JavaScript:字符串(初学者)

> 原文：<https://dev.to/banesag/javascript-strings-beginners-1bpj>

[![alt text](../Images/27b439fa82cbc0f2aa1c1e8bf91b7a3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qoj3ixF2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6r3moa4dncwis50bzx3h.jpg)

字符串是一种可以赋给变量的值。字符串本身是包含字母和符号的字符的集合。字符串是表示文本的值。单引号或双引号用于创建字符串文字；引号表示包含的文本不是代码，而是一个值。

**举例:**

```
‘hello’ 
```

在命名变量时应该特别注意，避免以下划线开始命名变量，因为有一些私有属性和方法是以这种方式命名的，这样会导致代码出错。还要避免以$字符开始变量，因为在 jQuery 库中经常可以看到它，而且它还会导致代码出现问题。

对于初学者来说，记住并防止常见错误的简单方法是使用引号；例如，可以用单引号创建字符串文字。

**但如果你想用双引号，也是可以接受的。**

```
“hello” 
```

**现在，如果你的字符串包含一个撇号，**

```
‘It’s me’ // It will cause an error 
```

**那么，你需要这样写:**

```
“it’s me” // this will work because the double quotes are enclosing the string 
```

**另一个选项，是在字符串里面的省略号前使用 backlash:**

```
‘It\’s me’ // the backlash avoids to cause an error in the use of apostrophes 
```

## 字符串属性和方法

属性是指关于对象或其值的信息，方法是对对象或值(字符串)执行的操作。访问字符串属性的方法是使用点符号(.)其次是我们感兴趣的物业。

查找 length 属性以了解字符串中有多少个字符的示例。

**举例:**

```
Const name = ‘banesa’; // Declared and assigned variable
name.length; // this will return the variable’s length
<< 6 
```

**替代符号法:**

```
Name[‘length’]; // Using square brackets is an alternative notation method
<< 6 
```

使用点标记法调用方法。);这是通过编写属性，然后是点符号，后面是您想要应用的方法，如下:

```
name.toUpperCase();
<< ‘BANESA’

name.toLowerCase();
<< ‘banesa’

name.includes(‘a’);
<< true

name.startsWith(‘b’);
<< true 
```

## 模板文字

模板文字的主要特征是它们使用反斜杠字符，并且允许使用双引号。

```
`I said, “It’s late!” ` ; 
```

模板文字还允许字符串插值，这很有帮助，因为您可以使用${}语法将变量直接添加到字符串中。

**举例:**

```
Const name = ‘Banesa’;
`Hello ${ name }!`;
<< ‘Hello Banesa!’ 
```

### 好的做法:

使用 const 来确保在代码的不同部分没有分配相同的变量，特别是当代码变得很大，可能会忘记已经分配的变量时。

在每行的末尾使用分号。

如果你是一个初学者或有抱负的程序员，或者如果你觉得你需要回到基础并巩固它们，我希望这篇文章能帮助你。