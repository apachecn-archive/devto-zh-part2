# Javascript 算法挑战|第 1 部分

> 原文：<https://dev.to/martinnrdstrm/javascript-algorithms-challenges--part-1--hc7>

这是我将要撰写的关于 Javascript 编码挑战以及如何解决它们(在某些方面)的系列文章的开始。

[![Alt text of image](img/303f837d110f8e60a9558d126fd542e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qv_TZdgv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ApZWMN0lPVfh3XmddXwPWoQ.jpeg)

# **为什么还要费心去读这个？**

如果你打算成为一名优秀的程序员，我敢打赌，你们中的一些人已经计划在未来找一份工作。当你得到一份工作时，你必须经历一个过程，其中包括面试和可能的编码挑战。有些可以写在公司的白板上或者你家里的电脑上。公司通常进行这些类型的测试是因为:

*   这表明你知道你的编程逻辑
*   这表明你有批判性思维能力
*   它显示了你如何在压力下工作
*   它只是向公司展示了你如何编码的大致情况

因此，了解并更好地编写算法是有好处的，因为这可能是公司希望你编写的。

因此，我决定开始一个系列，“Javascript 算法挑战”，在这里我将提出不同的挑战，并展示如何解决它们(在某些方面)，以使您更好地理解编写算法并理解它们。

# **先决条件**

我将使用 Node.js 来运行 Javascript 代码，所以您应该在继续之前安装它。

我已经为这篇文章创建了一个要点，所以你可以快速开始！就是这里:

```
/*
Author: Martin Nordström
Created: 2018/03/09 
Keep coding!
*/

// REVERSE A STRING CHALLENGE
// Return string in reverse
function reverseString(str) {}

// PALINDROME CHALLENGE
// Return true if palindrome and false if not
function isPalindrome(str) {} 
```

Enter fullscreen mode Exit fullscreen mode

当您安装了 Node.js 并下载了 Gist 之后，您就可以开始了！:-)

# **挑战 1 —反一串**

由于这个挑战非常简单，我将介绍几种解决方法，这样你就可以知道有很多不同的方法可以达到相同的效果。我们将从以下内容开始:

`function reverseString(str) {}`

我们可以做的第一件事是创建一个新变量，我们将把它设置为用`split()`传入的字符串。因为`split()`把一个字符串变成一个数组，并接受一个分隔符的参数。因为我们希望每个字符都放在它自己的数组值中，所以我们将把参数设置为`''`。会是这样的:

```
function reverseString(str) {
  const strArray = str.split('');
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用数组原型方法 reverse()，它将反转数组( *smart，对吗？*)。所以让我们覆盖当前值。

`function reverseString(str) {
const strArray = str.split('');
strArray.reverse();
}`

为了测试这一点，我们可以调用函数并传入一个字符串，看看这给了我们什么。我们不要忘记在函数的底部放一个`console.log()`。

```
function reverseString(str) {
  const strArray = str.split('');
  strArray.reverse();
  console.log(strArray);
}
reverseString('Hello Medium');
// Output:
[ 'm', 'u', 'i', 'd', 'e', 'M', ' ', 'o', 'l', 'l', 'e', 'H' ] 
```

Enter fullscreen mode Exit fullscreen mode

既然我们得到了逆序的数组，我们可以把它变回一个普通的字符串。我们可以用`join()`来做到这一点，它将一个数组的元素连接成一个字符串并返回该字符串。我们也可以像使用`split()`一样指定一个分隔符。所以让我们返回应用了`join()`方法的新字符串。代码将如下所示:

```
function reverseString(str) {
  const strArray = str.split('');
  strArray.reverse();
  return strArray.join('');
}
reverseString('hello');
// Output: muideM olleH 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但它真的很乱。老实说，我们甚至不需要 strArray 变量！我们能做的就是把这些方法链接在一起，就像这样:

```
function reverseString(str) {
  return str.split('').reverse().join('');
}
reverseString('Hello Medium');
// Output: muideM olleH 
```

Enter fullscreen mode Exit fullscreen mode

除了使用这些方法，我们还可以使用传统的 FOR 循环！在这个例子中，我们将使用一个*递减*的循环。

我们应该做的第一件事是创建一个新的变量，它包含一个空字符串，这个空字符串将存放新创建的字符串。

```
function reverseString(str) {
  let revString = "";  
} 
```

Enter fullscreen mode Exit fullscreen mode

第二步是创建实际的 for 循环。我们将使用字符串长度，但是像这样:`str.length — 1`因为它将对应于字符串中的最后一个字母。在我们的例子中，“m”。只要`i`大于或等于 0，我们也将循环，并在每次迭代后递减 I。然后将`newString`变量添加到自身和数组`str`的索引值中。我们还得归还`revString`。

```
function reverseString(str) {
  let revString = "";

  for (let i = str.length - 1; i >= 0; i--) {
    revString += str[i];
  }

  return revString;

}
reverseString('Hello Medium');
// Output: muideM olleH 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。您现在知道了在 Javascript 中反转字符串的两种方法！

[![Alt text of image](img/2f6b43271673d1d5a62477b6a4fc835e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XQlRFibA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AIQhAOkIruj1N39jF8H13CQ.jpeg)

# **挑战 2—回文**

首先，回文是一个无论向前还是向后都一样的单词或短语。一些例子:

*   安娜；安那（anna 旧时印度货币名）
*   但是大号
*   我们不纯洁吗？“没有先生！”巴拿马的穆迪·诺列加吹牛。“就是垃圾！”讽刺注定一个人的命运；一个新时代的囚犯

所以我们要做的是，如果单词或短语是回文，返回 true，如果不是回文，返回 false。相当简单的东西！

这是我们的起始代码块:

`function isPalindrome(str) {}`

我们可以做的第一件事是反转字符串，将它的值设置为一个变量，因为我们已经知道如何做了，这将是非常简单的！我将采用我们的第一个解决方案，因为它是最实际的(在我看来)。

```
function isPalidrom(str) {
  const revString = str.split('').reverse().join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

因为一个回文向前和向后是一样的，我们可以检查两个不同的字符串是否相等！

```
function isPalidrom(str) {
  const revString = str.split('').reverse().join('');

  return revString === str;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以稍微整理一下代码，当然也可以调用函数并传入一个字符串。

```
function isPalidrom(str) {
  return str === str.split('').reverse().join('');
}
isPalidrom('Hello Medium');
// Output: false 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这将返回`false`，因为*“Hello Medium”*是**而不是**一个赌场！但是*“我们不纯洁吗？”不先生。"巴拿马喜怒无常的诺列加吹嘘道. "简直是垃圾！“讽刺注定一个人；*新时代的囚徒到了！

```
function isPalidrom(str) {
  return str === str.split('').reverse().join('');
}
isPalidrom('Are we not pure? “No sir!” Panama’s moody Noriega brags. “It is garbage!” Irony dooms a man; a prisoner up to new era');
// Output: true 
```

Enter fullscreen mode Exit fullscreen mode

一个简单的想法！如果我们键入`rAceCar`，它将返回 false，因为我们有一个大写的`C? and a capital` A`。但是不要担心，修复超级容易！

 ``function isPalidrom(str) {
str = str.toLowerCase();
return str === str.split('').reverse().join('');
}
isPalidrom('Are we not pure? “No sir!” Panama’s moody Noriega brags. “It is garbage!” Irony dooms a man; a prisoner up to new era');
// Output: true`` 

 ``这里我们只是把字符串变成小写，然后比较两者。

再次恭喜！您现在也知道如何用 Javascript 检查回文了！

[![Alt text of image](img/709df7d96a7e42f736bc64d7c20dc175.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iQ8zaFrh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5izCoteagROfYODvbD39KQ.jpeg)

# **最后一句话**

我希望这能对你有所帮助。这是我的“Javascript 算法挑战”系列的第一部分。我会努力寻找更多有趣且有教育意义的挑战。因此，请在这里或在我的其他社交媒体平台上关注我，以获得关于即将发布的文章的消息！

# **目标**

今年我有很多目标。最重要的一点是成为一个更好的开发人员，同时在 Medium 上拥有 100 名追随者，并在这里发布更多内容，帮助其他人学习新知识。知识就是力量！

# [t1**马丁·诺斯流**](#martin-nordstr%C3%B6m)

[Instagram](https://www.instagram.com/martinnordstromcode/) | [推特](https://twitter.com/martinnrdstrm) | [Github](https://github.com/martinnord)

本文最初发布在 Medium 上。如果你想去那里看看，请点击这个链接:[原文](https://medium.com/@martinnord/javascript-algorithms-challanges-part-1-a56ba47ff43f)``