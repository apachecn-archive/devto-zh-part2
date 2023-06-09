# 用 doggos 学习正则表达式

> 原文：<https://dev.to/aniketsmk/learning-regex-with-doggos-2i20>

比方说，你正在浏览 twitter，遇到了这样一个绝妙的笑话。

> ![🌼Elle🐱Gato🌼 (they/them) profile image](img/47678ef979d0e6f3edf395184b012868.png)T3】🌼她🐱加托🌼(他们/他们)@ ellle _ em![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)狗:博克博克
> 人类:停止吠叫
> 
> 狗如何诠释这种交流
> 狗:大声喧哗
> 人类:也大声喧哗
> 狗:O 酷我们 R 一起大声喧哗现在 FREN 让我们养 GOIN
> 人类:更大声喧哗
> 狗:UR 这么擅长这个14:20

狗的笑话很神奇，我们想和大家分享！即使是那些不在推特上的人。

所以还是复制/粘贴吧！

不幸的是，当你在 twitter 网站上尝试时，你得到的是这个:

狗:博克博克博克人类:别叫了狗是如何理解这种交换的狗:吵闹的声音人类:也是吵闹的声音狗:哦，太酷了，我们现在一起制造吵闹的声音 FREN 让 GOIN 保持人类:更多的吵闹的声音狗:你很擅长这个

哎呀，看起来我们不知何故丢失了所有的换行符。很难读懂那样的笑话。所以我们必须自己格式化它吗？

如果你有正则表达式就不会！

我们希望将文本转换成不同的形式，正则表达式查找替换在这方面做得很好。

### 第 0 步:设置

做正则表达式替换需要什么软件？很多不同的编辑器都内置了这个。我将用 [Sublime Text](https://www.sublimetext.com/) 进行演示，但它内置在我所知道的每一个 IDE 和代码编辑器中，包括 VsCode 和 Notepad++。你所要做的就是寻找复选框或符号。* '表示正则表达式匹配。(不需要的时候记得关掉！)

我所知道的唯一没有它的编辑器是经典的 Windows 记事本。

### 第一步:找模式

找到一个模式。

所有的 regexs 所做的，都是寻找一个特定的模式。所以在你写出来之前，作为一个人，你必须自己识别模式。

一种模式是:找到每个以冒号“:”结尾的单词，在前加一个换行符*，并去掉空格。*

好的，幸运的是 regex 有一个内置的单词匹配器，它就是，哒哒！

\w

那不太对，它匹配一个单词中的每一个*字符*。更多细节[此处](https://www.w3schools.com/jsref/jsref_regexp_wordchar.asp)。

[![](img/7ca02cd47515a611b401b71b14ad54af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KFsFJh00--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATql2LDR-5icz2um7jI0WQg.png)

因此，我们将告诉它与匹配相同的每个邻居一起匹配。为此，只需添加' * '

[![](img/05b02cd90e646833e989d7dc8dc33116.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lIfUXxlh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADaH7-a3e_o8kgtz6OsD_YA.png)

快到了！我们现在只需要告诉它只匹配以“:”结尾的单词，结果正如您所料。只需在其后键入:。

[![](img/5f898fac64b78cae9e2085877c7e9051.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PquPagUj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7-7-FmaZo1y7-DYd6kTMcw.png)

好吧，看起来不错！现在我们如何把所有这些放到一个新的行上？概括地说，我们想在它之前添加一个换行符。我希望你看到的是 find 下面那个可疑的空“replace”文本框。

### 第二步:捕捉每个要替换的单词

现在我们已经匹配了文本，但是每个单词都是不同的。通常当你做查找替换的时候，你只需要输入单词，替换就可以了。当你使用正则表达式并捕获了不同的单词时，你会怎么做？

答案是:

捕获组

<figure>[![](img/690d1f490336b29c8663526d8e87d87f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5qKaghI6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay9rYIxt24YR2P4_7d0JE4Q.png) 

<figcaption>注意正则表达式现在用圆括号(\w*)代替()</figcaption>

</figure>

我们将说我们希望 regex 将整个单词放入一个“捕获组”中。

这意味着，我们可以在查找中引用那个组，甚至按它们被捕获的顺序替换。在这种情况下，只需将()中的任何内容视为变量$1。

所以用同一个单词替换所有这些单词，但是在它前面加一个换行符，你能猜到吗？会是什么呢？

这里有个提示，把这个词想象成变量$1，把一个换行符想象成文本\n。

[![](img/9d70084c855a643af3ac15ae51c18f18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cucSekJd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvJOzKRvhcGNa7Qy7vsZSFA.png)

答案是:

\n$1

和我们之前说的那句话一样。我们希望捕获所有以“:”结尾的单词，并用相同的单词替换它们，但在它们前面加一个换行符。

并且像变魔术一样我们以:

[![](img/7958b193260f0fba86ae0d259e4ffe0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2pzFfNV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AsSqSxkXeUeEZ-OdnjPu57Q.png)

太好了！我们就要得到我们想要的了！我犯了每次这样做时都会犯的同样的错误。我保留了单词中的空格，单词前的空格现在是换行符前的空格，即每行末尾的空格。

在每一行的末尾寻找换行符，我们可以看到这个。

<figure>[![](img/9fbbb4a4609e6211b103b8de35806930.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oa1a8wSv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AfUXgt0eh2qNq46pd-Uwh3w.png) 

<figcaption>这是空格字符，后跟' $ '，在 find 中使用，表示'在末尾'</figcaption>

</figure>

所以我们在每一行的末尾寻找空格，并在那里看到它。

向用户演示如何避免这种情况，只需点击文档的文本并按 ctrl+Z 来取消正则表达式替换，然后重新开始:)

### 第三步:观察异常值

另一件事你可能已经注意到了，有一行叙述没有分号。我们也没有打算让完成的文本在顶部有一个新行。在你寻找的模式中可能总会有一些小的中断，你可以决定是否要用另一个正则表达式来清理这些中断，或者如果只有一个特殊情况，就手工修复它们。

时刻留意那些与你的模式不符的东西！

所以最终在 2 个地方而不是 8 个地方进行了一点手动编辑！我们明白了。

[![](img/f7c43c4fd24c4d527ec32e8793388f8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5ntl6wTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AgFsax4fe5eSOV1hbd2c0KA.png)

最后，你可以把那个笑话的文本发送到你的 slack 频道，假装那是你在开玩笑！

### 反面教材

Regex 非常有趣和强大，我们刚刚开始触及它的皮毛。尽管正因为如此，你很容易在一个可能导致更多麻烦的地方使用它。因此，如果遇到问题，不要忘记尽早提交 git 和 ctrl+z！

<figure>[![](img/cade73add01082204af4f771fd3fbc7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LLYdIT8t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AoGaYyDJNEZ6UL5nN-43YMg.png)

<figcaption>——来自雄壮的 [XKCD](https://xkcd.com/208/)</figcaption>

</figure>

### 实验！

如果你需要一个地方来试用 regex 并获得学习它的帮助，regex 101 网站是很棒的，去看看 https://regex101.com/

[在线 regex 测试和调试器:PHP、PCRE、Python、Golang 和 JavaScript](https://regex101.com/)