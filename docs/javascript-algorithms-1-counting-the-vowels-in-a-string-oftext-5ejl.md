# Javascript 算法#1:计算一串文本中的元音字母

> 原文：<https://dev.to/worldclassdev/javascript-algorithms-1-counting-the-vowels-in-a-string-oftext-5ejl>

* * *

[![](img/6271876fe952149652094bbebe5432c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6ayFjApj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ne3lavoy7gyxyvm984t.png) 
这些年来，随着 Javascript 越来越强大的功能不断扩展，它已经获得了很多关注。它已经从仅仅是一种用于制作酷的交互式 web 界面的客户端脚本语言发展到广泛用于服务器端，用于移动应用程序、桌面应用程序、嵌入式系统、IOT 等等。

然而，越来越明显的是，尽管有各种各样的框架可以增强 Javascript 开发人员的能力，但最终都归结于对基础知识的理解。操纵普通 Javascript 以最有效的方式做事的能力是区别所在。如今有如此多的 Javascript 开发人员，这让我想知道我们中有多少人真正了解自己的东西。

在本系列中，我们将使用普通 Javascript 实现各种算法。我们将探索各种解决方案，并研究每种方法的利弊。我们还将发布 CodePen show 的链接，展示这些实现。当你尝试自己重新创建这些解决方案，并尝试改进它们时，你会受益匪浅。

我们建议您首先尝试自己动手，只参考代码来获得正确的方向，确认您的解决方案，并研究其他方法。算法的范围将从编写基本算法到高级算法，因此，其中一些挑战确实非常具有挑战性。不要气馁。这是所有事情的关键，也是你作为一名开发人员成长的方式。所以请坐好，享受旅程。大家一起成长吧！

# 挑战

基本上，我们希望能够接收任意长度的文本字符串，并返回文本中找到的元音数。

# 算法思维

[![](img/605a73fed9184da2ba06763bfb5da4e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VzWJ0T2w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/SBgHo3lCTfubIHmEo7Or_0_w9ZqNj9KDG3zkYus.jpeg)

“诸位！这没有我想象的那么简单”阅读上面的挑战陈述，您会注意到陈述**“接收一串文本”**。如果你习惯于编程，这应该会让你想起函数的概念。我们可以写一个函数，它有一个叫做“文本”的参数。文本可以是任意长度的字符串，当函数被调用时，它将作为参数传递给函数。

接下来，在函数中，我们必须**遍历文本并搜索英语元音** (a，e，I，o，u)的出现。

然后函数**返回找到的匹配(元音)总数**。这应该会让人想到“返回语句”，因为它们基本上是停止一个函数的执行，并从该函数返回值。

# 代码实现

哦哦耶！！！我们把无聊的东西都扔了。这些算法不会自己写吧？我们会探索两种方法来实现这一点。首先，我们将遵循迭代方法，然后使用正则表达式。

## 一种迭代方法

在迭代方法中，我们必须遍历传递的字符串中的每个字母，然后检查它们是否与任何元音匹配。在运行文本之前，我们将初始化一个计数器，并赋一个零值。在有匹配的情况下，我们会增加计数器。很简单，对吧？

这是我们的实现:

```
/*
An iterative approach to counting the number of vowels in a
string of text.
*/
const vowels = ["a", "e", "i", "o", "u"]

function countVowelsIterative(text) {
// Initialize counter
let counter = 0;

// Loop through text to test if each character is a vowel
for (let letter of text.toLowerCase()){
    if (vowels.includes(letter)) {
       counter++
    }
}

// Log formatted response to console
console.log(`The text contains ${counter} vowel(s)`)

// Return number of vowels
return counter
}

/* 
    ============== Test Case ===================
*/
countVowelsIterative('I am a world-class developer using iterations');
// Logs "The text contains 16 vowel(s)" to the console
// returns 16 
```

Enter fullscreen mode Exit fullscreen mode

这是不言自明的，对吧？让我们复习一些要点。

*   首先，我们声明一个常量“元音”,它包含一个由五个英语元音组成的数组。
*   接下来，我们利用 for…of 循环遍历文本的每个字母。如果你不熟悉这一点，for…of 循环基本上创建了一个在[可迭代对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)上迭代的循环。可迭代对象可以是字符串、数组、映射、集合等。你可以在这里了解更多[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)
*   注意我们是如何在循环中将文本中的所有字母转换成小写的。这是因为，我们不想错过传递的文本中大写元音的情况(相信我，这不是故意的)。
*   接下来，在循环中，我们使用 if 语句检查所选字母是否包含在我们之前定义的元音数组中。相应地，我们在元音数组上调用 includes()方法来确定数组是否包含所选的字母，根据情况返回 true 或 false。点击了解更多关于 includes()如何工作的信息[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
*   如果条件评估为真，我们递增计数器。
*   循环之后，我们将格式化的消息记录到控制台，告诉我们元音的数量，然后返回与找到的元音数量相等的计数器。

Wheeewww！！！感觉真好。现在让我们考虑一种稍微高级一点但简洁的方法。

## 使用正则表达式

根据我的经验，正则表达式对大多数开发人员来说是一个相当大的麻烦。通常，我们无法理解语法及其应用。因此，当我们需要使用它们时，我们通常会从网上获取一些片段。那好吧。让我们试着改变这一切！

基本上，正则表达式帮助我们在字符串中找到模式或字符/字符组合。明白为什么这与我们相关了吗？它们将帮助我们在传递的文本中找到所需的字符。通过扩展，正则表达式可以帮助我们做更多不寻常的事情，比如实现内容过滤器。然而，我最喜欢正则表达式的一点是，它的基本原理在所有语言中都是一样的。

事不宜迟，让我们检查解决方案。如果你想学习更多关于 Javascript 中正则表达式的知识，请浏览 Mozilla 开发者网络的文档[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)。

下面是我们的实现:

```
/*
    Using Regular Expressions to count the number of vowels in a 
    string of text.

*/

function countVowelsRegex(text) {
    // Search text with Regex and store all matching instances 
    let matchingInstances = text.match(/[aeiou]/gi);

    // Check if matching instances exist then calculate length
    if(matchingInstances) {
        // Log formatted response to console 
         console.log(`The text contains ${matchingInstances.length} vowel(s)`) 

         // Return number of vowels
        return matchingInstances.length
    } else{
        return 0
    }
}

/* 
    ============== Test Case ===================
*/
countVowelsRegex('I am a world-class developer uisng Regex');
    // Logs "The text contains 13 vowel(s)" to the console
    // returns 13 
```

Enter fullscreen mode Exit fullscreen mode

我想你不需要回顾这一个，你会吗？好吧！好吧！我们来看看解决方案。

*   我们在函数中做的第一件事是对文本调用 match()方法，该方法返回在匹配作为文本参数传递的正则表达式后找到的匹配数组。点击了解更多关于 match()如何工作的信息[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)
*   正则表达式指定要在括号[]中查找的字母。对于简单模式，正则表达式通常定义在一对斜杠内。注意到结尾斜杠后面的字符“gi”了吗？
*   “g”代表全局搜索，在第一次匹配后不返回，从上一次匹配的末尾重新开始后续搜索。
*   “I”代表不区分大小写的搜索，这使得整个表达式不区分大小写(例如/xyz/i 将匹配 xyz)。
*   接下来，我们使用一个条件来检查是否找到任何匹配的实例。的。如果找到匹配项，上面使用的 match()方法返回匹配项的数组，如果没有找到匹配项，则返回“null”。因此，在条件中，如果“matchingInstances”评估为真值(这是找到的匹配的数组)，我们记录一个格式化的消息，显示元音的数量，这与数组的长度相同。然后我们也返回这个数字。另一方面，如果它评估为一个 falsy 值，我们返回 0，因为这意味着没有找到匹配。

# 评价&总结

我们现在已经成功地实现了一个算法，它可以计算 Javascript 文本字符串中元音的数量。

[![](img/ebcbd6d8e8d7280653cf92e052c8c0bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUUmsq9S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/Dd99tsKRaKtALNXDvjQn_0_OikT1OG_q9MPkOQ3.jpeg)

让我们评价一下这两种方法。迭代方法虽然不如另一种方法简洁，但它更容易，也更符合逻辑，尤其是对初学者而言。然而，正如下面的结果所示，Regex 方法得到了更好的优化。
[![](img/55342c948b7aef5473da82011b9b7b10.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--9MS4UzTA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/IpJeMXs1TIaK9KGUZOqH_1_RWbGd1aYfPpjTlM6SdRckQ.png)

点击[此处](https://jsperf.com/vowel-counter)自行运行这些测试。

也许我们可以说正则表达式方法赢了？然而，这两种方法都有效，可以用在面试中。

* * *

这让你兴奋吗？请在下面的评论区告诉我。对我来说真的是。我们成功地研究了两种实现算法的方法，这种算法可以帮助我们计算给定文本中元音的数量。在这个过程中，我们学习了一些 Javascript 方法和正则表达式。这些工具是现代 Javascript 开发人员工具箱中非常重要的项目。

请随意用其他方式实现它，并探索每种方法的优缺点。也在评论区分享给大家(可能是你笔的链接)。我们期待着见到他们。也要提问。我肯定我们会找到答案的。

如果你觉得这篇文章对你有帮助，请与他人分享。你免费接受，免费给予。我也不会介意一片掌声你懂的(*)。*

 *在推特上和我联系好吗？ [@worldclassdev](http://twitter.com/worldclassdev)

**[在这里订阅更新](https://medium.com/dev-process) &敬请期待本系列的下一篇文章。***