# 聪明的代码是不好的。不要写聪明的代码。

> 原文：<https://dev.to/guifroes/clever-code-is-bad-don-t-write-clever-code-5ei>

> 等等！代码不应该是智能的吗？编程很难！我们必须聪明！

没有。

代码应该是简单的。编程不是自作聪明的地方。让我解释一下。

## 什么是巧妙的代码

聪明的代码是这样的代码，当你读它的时候，你的表情是这样的:

<figure>[![Obama looking confused](img/9b04a85b8bcb9f06a5596f5ebbcf32cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8o-09x3w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/guifroes.com/wp-content/uploads/2018/09/confused-obama.jpg%3Ffit%3D300%252C225%26ssl%3D1) 

<figcaption>*“有意思..”*</figcaption>

</figure>

或者像这样:

<figure>[![Will Smith looking confused](img/62da1aedeb2c090eb882372a0b9bb0fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tguJTjHs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/guifroes.com/wp-content/uploads/2018/09/will-smith-is-confused.jpg%3Ffit%3D640%252C387%26ssl%3D1) 

<figcaption>*“哦！我明白了..”*</figcaption>

</figure>

或者像这样:

<figure>[![Mark Ruffalo is confused](img/07ae5ef80687639ca38633ee9d3e4c96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZUw819rQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/guifroes.com/wp-content/uploads/2018/09/mark-ruffalo_1646329c.jpg%3Ffit%3D460%252C288%26ssl%3D1) 

<figcaption>*【日轻】。”*</figcaption>

</figure>

**聪明的代码是应该立即且毫不费力地被理解的代码——****但事实并非如此。**

它可以以多种形式存在。这可能是一个班轮做得太多了。

```
return i > 0 ? i << 2 : ~(i << 2) + 1; 
```

*“搞什么鬼？”*(显然，这将它乘以 4 并使其为正——谁知道呢。从[这里](https://www.simplethread.com/dont-be-clever/)得到的。)

也可能是超设计的一组抽象的类，以至于你花了一个小时浏览继承层次结构，仍然不知道发生了什么。

<figure>[![](img/944d9c91214677f1c3a807cba6d46d84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w-EAvnMv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/guifroes.com/wp-content/uploads/2018/09/tumblr_o16n2kBlpX1ta3qyvo1_1280.jpg%3Fresize%3D1024%252C768%26ssl%3D1) 

<figcaption>*“看到了吗？就这么简单！”*</figcaption>

</figure>

> “但在我看来，这些代码非常简单！有什么问题？”

是的，代码聪明是主观的，见仁见智。对我来说难以理解的事情对你来说可能很容易。这就是我这篇文章的观点:

我们不为自己写代码，我们为他人写代码(而他人可能是未来的你)！

> ![Programming Wisdom profile image](img/c0e04370043baff7812aa9d1c701cd8f.png)编程智慧[@ code Wisdom](https://dev.to/codewisdom)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)“任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”——马丁·福勒2018 年 06 月 12:37 分[![Twitter reply action](img/e5d6036ea29f7fc2ceecadd7806ec22d.png)](https://twitter.com/intent/tweet?in_reply_to=1004341385808539649)[![Twitter retweet action](img/045a8cb35300a0b869b6787434707687.png)](https://twitter.com/intent/retweet?tweet_id=1004341385808539649)521[![Twitter like action](img/1bf915c7b46baf6310d9eb9b48e72a89.png)](https://twitter.com/intent/like?tweet_id=1004341385808539649)1468

聪明的代码是不好的，因为如果它不可理解，它就不可维护。

之所以不好，是因为调试 It 会耗费精力，而这些精力本可以用在更高价值的活动上，比如构建新功能、改进测试、自动化任务，或者尝试能为客户带来价值的新技术和工具。

它之所以不好，是因为它让编码看起来比实际更难。这让人们觉得他们什么都不懂，在我们应该让事情变得更容易、更顺利的地方造成了摩擦。

## 升一级为开发者

关心聪明代码的后果并避免它们是让你成为高级开发人员的原因之一，而不是技巧或晦涩语言结构的数量，或者你用一行代码编写整个程序的能力。

新手写巧妙的代码是常有的事。不是绝对的初学者——他们仍在学习这门语言——而是那些已经很流利并在学习新技巧的人。那些家伙很骄傲，想要炫耀他们所有的技能。

我们应该学习新的技巧，但也要学会何时使用它们。更重要的是:认识到这样一个事实:有能力做某事并不意味着我们应该做。就像你不穿短裤去参加婚礼和不穿西装去海滩一样，任何事情都有特定的时间和地点。

我能认出初学者的一个可靠方法是通过一个人编写的聪明的代码。这表明这个人不太明白软件开发是一种社会活动，没有自我的位置。

从初级开发人员到高级开发人员的大部分转变是这样的理解，我们的目的是为雇佣我们的组织创造价值，代码只是一种工具，应该被如此对待。

因此，如果你想成为一名高级开发人员或技术领导者，你必须掌握并教那些经验不足的人如何保持代码库聪明的代码自由。

## *“好的，知道了。我现在该怎么办？”*

很难不偶尔让一些聪明的代码溜进代码库。毕竟，有些代码并不总是很聪明——对你来说可能很简单，对吗？

这就是为什么我们需要时刻关注自己。以下是一些有帮助的想法:

### **用普通的方式做事**

通常，做某事有不止一种方法。使用最古老、最无聊、最显而易见的方法，任何初学者都能理解。没有魔法，没有框架，没有任何需要额外知识或大脑周期才能理解的东西。而不是做:

```
while (*d++ = *s++); 
```

这样做:

```
strcpy(destination, source); 
```

### **不要试图在硬盘或屏幕上节省空间**

如果你最终得到更多的代码和更大的文件也没关系:存储是便宜的，人们的努力和时间是昂贵的。

提取方法，使用有意义的名称——使用尽可能多的单词来表达你的意图。使用空格、换行符和相应的格式。转本:

```
(people != null) && (people.get(name).getAge() >= 18) ? people2.add(people.get(name)) : people3.add(people.get(name)); 
```

成这样:

```
Person person = allPeople.personNamed(name); if (person.isAllowedToDrink()) { listOfDrinkingPeople.add(person); } else { listOfNotDrinkingPeople.add(person); } 
```

有行为的物体也很有帮助。

### **得到反馈**

结对编程在这方面非常有用。问同事:“这个你懂吗？”“还是这样比较好？”看看他们是不是在做这些表情。

观察你自己对反馈的反应:如果你在读你写的代码时做了个鬼脸，那就换掉它！

进行代码审查——让团队阅读代码并互相反馈。

## 接下来是什么？

现在你知道什么是聪明的代码，为什么它是坏的，以及如何不写它。但是你团队中的其他人呢？如果你想知道如何说服他们写出干净的代码，在我的网站上查看这篇文章。

帖子[聪明的代码不好。不要写聪明的代码。](https://guifroes.com/clever-code-is-bad/)最早出现在[的龟霜上](https://guifroes.com)。