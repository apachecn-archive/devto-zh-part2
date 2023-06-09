# JavaScript 对象

> 原文：<https://dev.to/codetheweb/javascript-objects-2c18>

[这篇文章最初发表在我的博客 Code The Web 上](https://codetheweb.blog/2018/06/09/javascript-objects/)

[![JavaScript Objects](img/592c5a9a898737639f3d57f067f073b2.png)T2】](https://codetheweb.blog/2018/06/09/javascript-objects/)

对象是 JavaScript 中存储数据的方式，是面向对象编程的关键部分。在本文中学习语法和更多关于它们的内容...

> 虽然这不是完全必要的，但是对 JavaScript 数组的基本理解可以帮助你理解对象。

## 什么是物体？

JavaScript 中的对象与[数组](https://codetheweb.blog/2018/04/28/javascript-arrays/)非常相似——它是一种存储大量值的数据类型。这些值可以是任何数据类型——数字、字符串、布尔值、函数，甚至数组和对象！

数组和对象的区别在于引用值的方式。在数组中，引用一个带数字的值(它在数组中的位置)，例如:

```
myArray[3] 
```

Enter fullscreen mode Exit fullscreen mode

当引用一个对象中的值时，使用一个*名称*而不是一个数字。比如:

```
myObject['superFlashyName'] 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！*姓名而不是号码*。

## 如何声明一个对象

声明对象的语法不同于声明数组的语法。下面是它的样子:

```
var myFriend = {firstName: 'John', lastName: 'Smith', age: 27, nationality: 'British'}; 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它的*有点类似于*的数组语法，但是它有花括号而不是方括号。这些项目仍然用逗号分隔。但是，这些项目现在有了“名称”和一个值。比如:

```
age: 27 
```

Enter fullscreen mode Exit fullscreen mode

这里，`age`是项目的名称(也称为“键”)，而`27`是项目的值。它们由冒号分隔。

我们称之为*键值对*。它有一个键和值...(*真的吗？*😜)

```
key: value 
```

Enter fullscreen mode Exit fullscreen mode

所以一个对象是由逗号分隔的键值对组成的，整个对象被一组花括号包裹起来。

我们的对象又来了:

```
var myFriend = {firstName: 'John', lastName: 'Smith', age: 27, nationality: 'British'}; 
```

Enter fullscreen mode Exit fullscreen mode

这样写完全可以接受:

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British'
}; 
```

Enter fullscreen mode Exit fullscreen mode

有些人喜欢第一种选择，而有些人喜欢第二种。这取决于你——选择一个你认为最有意义的。

## 如何获取/设置对象中的值

正如我在开头提到的，这是你如何引用一个对象中的项目:

```
myObject['key'] 
```

Enter fullscreen mode Exit fullscreen mode

你也可以这样表达:

```
myObject.key 
```

Enter fullscreen mode Exit fullscreen mode

对于第二个选项，不能使用变量或特殊字符(包括空格)作为键。例如，这些不会工作:

```
myObject[someVarContainingTheKey]
myObject['I have spaces and special characters ;)'] 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它们的效果并不好！

```
myObject.someVarContainingTheKey // No longer a variable - equivalent to myObject['someVarContainingTheKey']
myObject.I have spaces and special characters ;) // \0_0/ The syntax errors are killing me 
```

Enter fullscreen mode Exit fullscreen mode

值得一提的是，在变量名或对象键中使用特殊字符是不好的编程习惯。如果您使用某种形式的用户输入(可能包括空格)作为您的键，则是一个例外。

除非您有特殊字符或变量作为您的键，否则最好使用第二个选项(`myObject.key`)。为什么？它看起来更好(它对函数也更有意义，您很快就会看到)。但这取决于你。

## 作用于物体

我一开始就告诉过你，对象内部可以有任何类型的数据。这包括函数！但是你怎么把它放进一个物体里呢？

让我们用我们之前的朋友的例子:

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British'
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在假设我们有这个函数:

```
function sayHi() {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想把它添加到我们的对象中。这意味着我们可以这样称呼它:

```
myFriend.sayHi() 
```

Enter fullscreen mode Exit fullscreen mode

眼熟吗？这就是为什么`console.log()`是`console.log()`！`console`是一个内部有`log`功能的对象！

无论如何，这是我们如何把`sayHi()`放入我们的函数:

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British',
    sayHi: function() {
        alert('Hi!');
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

什么？！让我们来看看这是怎么回事。当你说:

```
function sayHi() {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

，它实际上只是:
的简称

```
var sayHi = function() {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，就像`var age = 27`变成了`age: 27`，这个:

```
var sayHi = function() {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

变成了这个:

```
sayHi: function() {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，当涉及到函数时，使用`object.key`语法比使用`object['key']`语法更有意义。想象一下不得不写这个！

```
myFriend['sayHi']() 
```

Enter fullscreen mode Exit fullscreen mode

恶心，真丑🙈

## 中的`this`物体

假设我们想给我们的朋友另一个函数:一个`introduce()`函数。它会发出警告:

> 我叫约翰·史密斯。我是英国人，今年 27 岁。

但是，我们不能仅仅将此作为一个简单的字符串来警告！我们需要使用对象值。所以如果我们把`age`改成`30`，它会说。

说`myFriend.age`是不好的编程习惯。这是因为如果将该函数放入另一个对象(比如说`myEnemy`)，它仍然会提醒`myFriend`而不是`myEnemy`的年龄。

相反，我们使用`this.age`。`this`简单指函数所在的对象。看看你能否运行这个函数，并把它放入对象中。请记住，您可以使用以下语法将字符串和变量合并在一起:

```
'Here is a variable: ' + myVar + ' ...yay!' 
```

Enter fullscreen mode Exit fullscreen mode

祝你好运！

> 注意:使用双引号，这样就不会干扰单词 I'm
> 中的撇号

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British',
    sayHi: function() {
        alert('Hi!');
    },
    introduce: function() {
        alert('My name is ' + this.firstName + '  ' + this.lastName + ". I'm " + this.nationality + " and I'm " + this.age + ' years old.');
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

{:.揭示}

厉害！`this`就是这么干的！

## 在对象和字符串之间转换

> 在这一部分，您需要打开 JavaScript 控制台。右键单击“检查元素”，然后转到“控制台”选项卡。

要将数组转换成字符串，可以使用`.join()`或`.toString()` :

```
var myArray = [1,2,3,4];
console.log(myArray.join());
// logs '1,2,3,4'
console.log(myArray.toString());
// logs '1,2,3,4' 
```

Enter fullscreen mode Exit fullscreen mode

我们如何用一个物体做到这一点？让我们试试`.toString()` ( `.join()`仅用于数组):

```
var myObject = {favoriteFood: 'Pizza', hobby: 'Coding'};
console.log(myObject.toString());
// logs '[object Object]' 
```

Enter fullscreen mode Exit fullscreen mode

恶！这不是我们想要的！💩

相反，我们有一个专门用于对象的特殊函数:

```
var myObject = {favoriteFood: "Pizza", hobby: "Coding"};
console.log(JSON.stringify(myObject));
// logs '{"favoriteFood":"Pizza","hobby":"Coding"}' 
```

Enter fullscreen mode Exit fullscreen mode

那更好！注意是`JSON.stringify(myObject)`，而不是像`.toString()`和`.join()`那样的`myObject.stringify()`。

`JSON`代表 JavaScript 对象符号。我会深入探讨这一点。

在 JavaScript 中，您还可以使用`split`函数从字符串中创建一个数组，如下所示:

```
var myString = 'how,are,you';
console.log(myString.split(','));
// logs ["how", "are", "you"] 
```

Enter fullscreen mode Exit fullscreen mode

对于对象来说，这相当于`JSON.parse`:T1

```
var myString = '{"favoriteFood":"Pizza","hobby":"Coding"}';
console.log(JSON.parse(myString));
// logs {favoriteFood: "Pizza", hobby: "Coding"} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，它记录的数组没有单引号，这意味着它现在是一个对象，而不再是一个字符串。要亲自查看，请将上面的代码片段粘贴到您的控制台中。它会像显示对象一样显示它，而不是字符串！

进一步尝试这两个新功能！将一些对象和对象组成字符串，然后在它们之间进行转换。

## 那么，那个 JSON thingo 是什么？

JSON 简单地代表 **JavaScript 对象符号**。换句话说，就是您在本文中学到的对象语法！

人们非常喜欢这种语法，以至于他们认为用它来存储数据是个好主意，所以现在 JSON 在 JavaScript 之外的许多方面都有使用。这就是为什么我们有类似`JSON.parse`的东西:我们只是简单地解析(解释)一个 JavaScript 对象。

你可以在[json.org](https://json.org/)找到更多关于 JSON 的信息(但是不要让你的大脑超载，它与本教程没有太大关系)。

## Object.keys()

对于一个数组，如果你知道它的大小，你就知道如何找到所有的值。例如，在一个长度为`4`的数组中，你知道要找到所有的值，你可以引用`array[0]`、`array[1]`、`array[2]`和`array[3]`。然而，我们不能对一个对象这样做，因为我们不知道所有的键是什么——它们不遵循像 0，1，2，3 这样的模式，因为我们可以将它们设置为我们喜欢的任何值。

幸运的是，有一个函数可以用来获取所有的密钥:

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British',
    sayHi: function() {
        alert('Hi!');
    },
    introduce: function() {
        alert('My name is ' + this.firstName + '  ' + this.lastName + ". I'm " + this.nationality + " and I'm " + this.age + ' years old.');
    }
};

console.log( Object.keys(myFriend) );
// logs ["firstName", "lastName", "age", "nationality", "sayHi", "introduce"] 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它返回了一个包含所有键的数组！记住语法是这样的:

```
Object.keys(myFriend) // RIGHT :) 
```

Enter fullscreen mode Exit fullscreen mode

**不是**这个:

```
myFriend.keys() // WRONG!!! 
```

Enter fullscreen mode Exit fullscreen mode

简单地创建一个对象中所有键的数组。

## Object.values()

除了查找对象中的所有键，我们还可以查找所有的值！这个*类似于*把一个对象变成一个数组，除了所有的键都将丢失。下面是它的工作方式:

```
var myFriend = {
    firstName: 'John',
    lastName: 'Smith',
    age: 27,
    nationality: 'British',
    sayHi: function() {
        alert('Hi!');
    },
    introduce: function() {
        alert('My name is ' + this.firstName + '  ' + this.lastName + ". I'm " + this.nationality + " and I'm " + this.age + ' years old.');
    }
};

console.log( Object.values(myFriend) );
// logs ["John", "Smith", 27, "British", function() { alert('Hi!'); }, function() { alert('My name is ' + this.firstName + ' ' + this.lastName + ". I'm " + this.nationality + " and I'm " + this.age + ' years old.'); }] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，控制台可能会用一个`ƒ`符号来缩写这些功能，例如:

```
["John", "Smith", 27, "British", ƒ, ƒ] 
```

Enter fullscreen mode Exit fullscreen mode

这就是`Object.values()`的工作方式——它只是创建一个对象中所有值的数组。

## 结论

今天到此为止！希望这篇文章已经让您对对象有了很好的理解。如果是这样，我真的很感激你在社交媒体上分享这篇文章([点击这里这样做](https://codetheweb.blog/2018/06/09/javascript-objects/#share))来帮助传播这个词，帮助像你一样的人。

一旦你对对象有了更长的了解，并消除了最初的困惑，就有必要研究对象的一些奇怪行为(称为“可变性”)，以及为什么它会令人害怕——我强烈推荐阅读 Zell Liew 的这篇文章。它更高级，这就是为什么我建议先玩一会儿物体。

如果你需要帮助理解这些概念，或者想给出反馈，请点击下面的[评论...](https://codetheweb.blog/2018/06/09/javascript-objects/#disqus_thread)(或者输入实时聊天内容)

如果你想要更多像这样的高质量文章，[注册来获得我的每周电子邮件，里面有来自 Code Web 和互联网的精选内容](https://codetheweb.blog/newsletter)(没有垃圾邮件)。

如果你碰巧是一个欣赏我作品的现实生活中的超级英雄，请给我买一杯或者两三杯咖啡，或者让 T2 成为我的赞助人。

下次见，我将会谈到如何在 JavaScript 中修改 HTML 和 CSS。这是 JavaScript 最重要的部分之一，也是所有三种 web 语言的交集。

* * *

> ### **[想要更多这样的精彩教程？注册编码网络周刊！](https://codetheweb.blog/newsletter/)**
> 
> **这是一封每周一封的电子邮件，里面包含了从网络编码和互联网学习网络开发的最佳资源**
> 
> ([了解更多...](https://codetheweb.blog/newsletter/))

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)