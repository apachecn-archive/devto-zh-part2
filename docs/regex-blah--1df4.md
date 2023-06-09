# 正则表达式废话

> 原文：<https://dev.to/kauresss/regex-blah--1df4>

[![Regex](img/faac90db2d296fa29a7d50609351a88f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n3FYKDxz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gs8hzn4i9q8g483ki20i.png) 
只是我对正则表达式的注释。没什么刺激的，只是痛苦。

### 简介

正则表达式通过使用符号、字母和标志来匹配模式(日期、生日、字母等)。Regex 用例中最普遍的是表单验证。

### 基础知识

要构造正则表达式:

```
1\. Literal form:  var pattern = /hello/; 
2\. Constructor form: var pattern = new RegExp('hello'); 
```

Enter fullscreen mode Exit fullscreen mode

### 图案

##### 1。精确匹配

var pattern =/Doing regex/；
//匹配单词“Doing”和“regex ”,两者之间有一个空格

某些用户提交的输入中的精确匹配？

```
var userInput = "Doing regex";
var pattern = new RegExp(userInput); 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。寻找匹配

1.  使用 test 方法查找用户输入/测试字符串和模式之间的匹配
2.  测试方法根据是否找到匹配返回真/假
3.  示例:

```
 var input= "I like dogs";
    var pattern  = /dogs/;  
    console.log(pattern.test(input));
    // returns true 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。正则表达式属性

1.  正则表达式对象有许多属性，这些属性提供了关于正则表达式范围的信息
2.  这些属性包括:

    *   全球的
    *   ignorecase
    *   采用双字节对字符进行编码
3.  它们由以下标志描述:
    -g
    -i
    -u

4.  示例:

```
 var input = "I like dogs";
    var pattern = /dogs/g;
    console.log(pattern.global);
    // returns true 
```

Enter fullscreen mode Exit fullscreen mode

1.  根据是否使用标志返回 True/False。

#### 4。句法

1.  使用标志将改变正则表达式工作的上下文
2.  默认情况下，正则表达式区分大小写
3.  使用“I”标志将确保忽略匹配的大小写。所以 F 和 F 是一样的
4.  默认情况下，只匹配模式的第一个实例。为了找到所有实例，使用了“g”(全局)标志

5.  示例:

```
 var input = "I like dogs";
      var pattern = /I LIKE DOGS/i; 
      console.log(pattern.test(input));
     //returns true 
```

Enter fullscreen mode Exit fullscreen mode

1.  ES2015 允许测试 unicode 字符。
2.  示例:

```
 var character = '😂';
     var pattern = /\u{1F602}/u;
     console.log(pattern.test(character); 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。特殊字符

1.  字符集:允许在特定的字符集内进行字符的 or 匹配
2.  字符集可以是:
    *   某些字符示例:“abc”
    *   或一系列字符例如:A-Z

```
3\. var input  = "I am a dog"; 
   var pattern = /[jkl]/; 
   console.log(pattern.test(input)); 
   //returns false 
```

Enter fullscreen mode Exit fullscreen mode

##### 范围

这些描述了与
匹配的任意范围的字符

```
 var input = "I like dogs"; 
 var pattern = /[a-zA-Z]/;
 console.log(pattern.test(input));
 // returns true 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试连字符

```
 var numbers = "100-200"; 
 var pattern = /[10-20-]/;
 console.log(pattern.test(numbers));
 // returns true 
```

Enter fullscreen mode Exit fullscreen mode

#### 否定字符集

要测试不在集合中的字符，使用'^'(帽子)符号

```
var noLetters = "abcdefghijklmnopqrstuvwxzy"
var pattern = /[^a-z]/
console.log(pattern.test(noLetters));
//returns false

var noNumbers = "abcdefghijklmnopqrstuvwxzy"
var pattern = /[^1-100]/
console.log(pattern.test(noNumbers));
//returns true 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试为“或”

要测试“或”,请使用“|”字符

```
var dogOrCat = "dog";
var pattern = /[dog|unicat]/
console.log(pattern.test(dogOrCat));
//returns true 
```

Enter fullscreen mode Exit fullscreen mode

#### 6。量词

量词允许你寻找一个模式“x”次。例如:

a.*:匹配一个字符或集合 0 次或更多次
b. +:匹配一个字符或集合 1 次或更多次
c. {n}:匹配一个字符或集合“n”次
d. {n，m}:匹配该字符或集合最少“n”次，最多“m”次。

#### 7。边界

a.^:从输入的开头匹配
b. $:匹配到输入的结尾
c. \b:匹配字母和非单词字符之间的边界，例如空格
d: \B:匹配非单词边界，即相同类型的两个字符之间的位置(例如字母、空格)。

#### 8。锚

^和$被称为锚，当我们想要确保整个测试字符串只包含模式时，我们使用它们。

#### 9。断言

让我想起了我在法学院学到的东西。断言允许你有条件地寻找模式。例如，只有当标记 B 跟在后面时，我们才能匹配标记 A。内括号()'？= '位于条件标记之前。要反转断言，请使用'？!'

```
var input = 'I love dogs',
pattern = /love(?= dogs)/;

console.log(pattern.test(input)); 
// returns true 
```

Enter fullscreen mode Exit fullscreen mode

```
var input = 'I love dogs',
pattern = /love(?! dogs)/;

console.log(pattern.test(input)); 
// returns false 
```

Enter fullscreen mode Exit fullscreen mode

#### 10 个 Exec 方法

此方法在字符串中搜索模式。如果找到匹配，则返回一个数组；否则，如果没有找到匹配，则返回“null”。

该数组包含匹配的文本作为第一项，任何匹配的组(用括号括起来)作为数组中的项。

```
var sentence = 'I love dogs',
var pattern = /(love)\s(dogs)/;
console.log(pattern.exec(sentence));
//returns ["love dogs", "love", "dogs"] 
```

Enter fullscreen mode Exit fullscreen mode

```
var sentence = 'I love dogs',
var pattern = /(love)\s(cats)/;
console.log(pattern.exec(sentence));
//returns null 
```

Enter fullscreen mode Exit fullscreen mode

#### 11。匹配方法

match 方法的作用与 exec 方法相同，只是它用于字符串而不是 regex 表达式。

```
var sentence= 'I love dogs';
var pattern = /(love) (dogs)/;
console.log(sentence.match(pattern));
// returns ["love dogs", "love", "dogs"] 
```

Enter fullscreen mode Exit fullscreen mode

#### 12。搜索方法

search 方法用于正则表达式，在字符串中搜索子字符串。如果找到，它将返回子字符串的索引，否则返回-1。

```
var sentence = 'I love dogs';
var pattern = /I/;
console.log(sentence.search(pattern));
//returns 0 
```

Enter fullscreen mode Exit fullscreen mode

#### 13。拆分方法

split 方法将把一个字符串拆分成一个数组，并以分隔符作为参数。

```
var sentence = 'I love dogs';
var pattern = /\s/;
 console.log(sentence.split(pattern));
// returns ["I", "love", "dogs"] 
```

Enter fullscreen mode Exit fullscreen mode

#### 14。资源:

[Envato Tuts+](https://code.tutsplus.com/tutorials/8-regular-expressions-you-should-know--net-6149)