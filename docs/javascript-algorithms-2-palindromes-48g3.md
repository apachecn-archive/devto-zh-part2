# Javascript 算法#2:回文

> 原文：<https://dev.to/worldclassdev/javascript-algorithms-2-palindromes-48g3>

[![](img/7109a79f36c45383abc2f94f60935a03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XEasNYZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7qe4349ug4cqz8ndfdcu.png) 
回文！回文！！回文！！！哦，男孩。我很确定你现在正在想这些是什么。作为一名软件开发人员，你个人知道，当有人在与我交流时说出这样的话，我会笨拙地做手势，并露出令人讨厌的傻笑来表示某种程度的不感兴趣。我真的不喜欢迷惑人。

安全带系上了吗？让我们公平对待这个词。我们走吧。

[![](img/0c9511c8a9d15d7a909d2dbe5fd2f1ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yP4ivGRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a3aakwl0pcjijhs28rnq.jpeg)

回文是一个单词、数字或其他字符序列，向后读和向前读是一样的，例如“夫人”或“赛车”。用一点程序员的话来说，我们可以说它是一串反向排列时不会改变的文本。这个词就这么多了，是吧？

# 挑战

给定一个文本字符串，返回 true 或 false，表明该文本是否为回文。

**PS:** 我记得在安德拉测试的时候接受过一次这个挑战。

# 算法逻辑

挑战说“给定一个文本字符串”，这意味着我们的函数将有一个字符串类型的参数，我们可以称之为“文本”。接下来，我们要评估字符串是否是回文。为此，我们必须首先反转字符串，然后将其与作为参数传入的字符串进行比较。为了避免字母大小写的问题，将文本转换为单一的大小写类型似乎是合理的，无论是大写还是小写。最后，我们将根据我们的评估结果“返回真或假”。当它是一个回文时为真，否则为假。
都说了！你现在应该去代码道场。

# 代码实现

实现回文检查器的方法有很多，这主要是因为有几种方法可以反转一个字符串，也有几种方法可以遍历一个字符串。因此，有一对夫妇的特技和组合可以拉下。然而，我们会考虑以下两种独特的实现方式:

## 直观的方法

好吧，我必须承认这个标题听起来有点误导。这并不是每个人面对这一挑战时会做的第一件事。这真的是解决问题的直接方法。有多直接？你会明白的。

```
/*
The Intuitive Approach: This is mostly a direct
approach that most would follow. We split the
string into characters, reverse the array of characters,
join the characters back to form a string, and then
test the created string against what was originally received.
*/
function palindrome(text) {
// Split, reverse and join string to get reversed text
var reversedText  = text.toLowerCase()
                    .split('').reverse().join('');

return text === reversedText;

} 
```

Enter fullscreen mode Exit fullscreen mode

我敢肯定有人在想“这真的一点都不直接”。哦好吧！让我们揭开“神秘”的面纱，好吗？

*   首先，我们的函数接受一个参数，它是要测试的文本字符串。

*   接下来，我们将字符串中的所有字母转换成小写，然后调用。split()方法，并将一个空字符串传递给它，以便将字符分散到数组中。

*   接下来，我们打电话。reverse()对数组中的元素进行反向重新排序。之后我们打电话。在反转的数组上 join()再次形成一个字符串。

瞧啊。我们有一根颠倒的绳子。请注意我们是如何连续链接所有这些方法，使我们的代码简洁而实用。这也是我喜欢 Javascript 的原因之一。优雅的语法！

*   最后，我们返回比较结果，这是一个布尔值，表明传入的字符串是否等于我们创建的反向字符串。这告诉我们传入的文本是否是回文。

明白吗？！！那很容易，不是吗？让我们试试稍微复杂一点的东西。

## 循环遍历并比较字符

嗯嗯！我认为这是一个稍微复杂的实现。

免责声明:这可能比你想象的更令人困惑。但我会尽我所能分解它。所以，不要害怕！

按照这种方法，我们尝试遍历传入的字符串，并将每个字符与字符串反转时当前位置的字符进行比较。

例如，如果我们测试字符串“developer ”,我们将比较“d”和“r ”,因为如果字符串颠倒过来,“d”将取代“r”的位置。奇怪的标点符号，我知道！*微笑*

相应地，我们也将位置 2 的 e 和位置 2 的 e 进行比较。如果这个字符串是一个回文，所有这些都将被测试为真。现在好了！让代码自己说话。

```
/*
Looping and Comparing using .every(): This approach allows us to
split the sting into an array of characters and then loop through
the characters comparing them with the characters in their
corresponding positions from the right.
*/
function palindrome(text) {
// Split text into array of characters
let charArray = text.toLowerCase().split('');

// Loop through every character and compare with the
// character in its corresponding position if the string
// was reversed. Then store the result
let result = charArray.every((letter, index) => {
return letter === charArray[charArray.length - index - 1];
});

// Return the result of the evaluation
return result
} 
```

Enter fullscreen mode Exit fullscreen mode

**这里戏剧性的音效** …lol…我真的只是太贪玩了。

好吧，我相信你一定已经注意到，学习用核心 Javascript 做惊人的事情是一个有趣而冒险的过程。好吧，我们来复习一下。

*   我们将字符串中的所有字母转换成小写，然后使用。再次 split()将字符串的字符分散到一个数组中。

*   接下来我们使用一个特殊的数组方法。every()循环遍历数组并执行检查。基本上。every()方法测试数组中的所有元素是否都通过了由提供的函数实现的测试。本例中提供的函数接受当前字母及其在数组中的索引作为参数。然后，我们返回该字母与当前占据该字母位置的字母之间的比较结果，如果字符串被颠倒，则该字母将占据该位置。了解更多关于。每()[此处](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)。

*   累积起来。如果测试在所有情况下都通过，则 every()方法的计算结果为 true，否则为 false。评估的结果就是我们存储在变量“result”中的内容，这就是我们的函数返回的内容，作为字符串失败或通过回文检查的指示。

[![](img/f0c1c259390cc3a5c061eed2fb7a96bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rUisXHQN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/psh88tycfkqoba9hb6ve.jpeg)

也许你也注意到了？我们的第二个实现在性能方面存在本质上的问题。也许在你继续这篇文章的其余部分之前，你可以试着自己识别它？

好的，在这里。我们遍历整个字符串，将每个字母与其位置相反的对应字母进行比较。也许拿出纸和笔，试着手动完成，然后你会注意到，一旦你在中间位置的字符串之外循环，你本质上是在重复你在迭代的前半部分已经经历过的比较。那是多余的，你不觉得吗？

为了解决这个问题，我们将添加一个检查来确保一旦到达字符串的中间点就停止循环。我真的希望你能尝试优化它。在看到你能想出什么后，我会在评论区和 twitter 上发布解决方案。尽情享受吧！

# 评价&总结

我们已经研究了用 Javascript 实现回文检查器的两种方法。这两种实现都不错，可以帮助您通过编码面试。

然而，正如我们在这种情况下所关心的，我们需要确定哪一个具有更高的性能特征，因为我们要看一看它们的优缺点。

在我的下一篇文章中，我将探索更多的方法来实现这个算法，因为我们考虑了其他更严格的回文变体，然后我们将运行一个性能测试来确定最高性能。

* * *

请随意用其他方式实现它，并探索每种方法的优缺点。也在评论区分享给大家(可能是你笔的链接)。我们期待着见到他们。也要提问。我肯定我们会找到答案的。

如果你觉得这篇文章对你有帮助，请与他人分享。你免费接受，免费给予。我也不会介意一片掌声你懂的(*)。
鼓掌，这样其他人也可以享受这个！*

 *在推特上和我联系好吗？ [@worldclassdev](https://dev.to/worldclassdev)

点击这里订阅更新&敬请关注本系列的下一篇文章。*