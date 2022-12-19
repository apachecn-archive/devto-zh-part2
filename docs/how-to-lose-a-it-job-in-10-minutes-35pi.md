# 如何在 10 分钟内丢掉工作

> 原文：<https://dev.to/albinotonnina/how-to-lose-a-it-job-in-10-minutes-35pi>

#### 白板编码面试会让你丢掉一份工作

最近，我非常接近在一家大公司找到一份工作。我顺利地通过了筛选程序和[带回家的任务](https://github.com/albinotonnina/react-redux-magichat-demo-booking-system)，但我没能通过最后一个阶段，一对一或二对一的面试:

*   **白板编码面试**:算法。
*   技术面试:JavaScript，CS，React。
*   文化契合度:那个。
*   **第二次白板面试**

### 出了差错

有几个错误导致了这次暂时的失败。

我可能已经通过了技术测试和文化适应(让我们假设一下)，但是我承认在白板编码面试中表现很差。

回想那一天，我不能期待任何不同的结果。

事实是:**我不准备做白板面试**。

### 白板访谈

> 在某种程度上，白板面试是对你知识深度的技术测试和社会实践的混合
> ( [来源](https://skillcrush.com/2016/03/29/rock-your-next-whiteboard-test/)

我们都知道那是什么，对吧？*这和代码没多大关系，这和你的解决能力有关，如果等等也没关系……*

我是一名前端开发人员，在我编码的时候，我通常不会实现类似面试的算法或者表达我的思维过程，我大部分时间都在做交互式 ui。

这些测试到底能告诉一个候选人多少？

[有人](https://medium.freecodecamp.org/why-is-hiring-broken-it-starts-at-the-whiteboard-34b088e5a5db)甚至说:

> 在现实生活中，你很少会在编码过程中凭记忆即兴创作出一个算法。您几乎总是要使用一个现有的库，它有自己的测试套件，并且通过了其他开发人员的审查。
> 
> 你真正需要能够回忆起算法的唯一世界将是一个后世界末日的世界，在那里，所有连接到互联网的计算机的硬盘都被烧毁，所有基础学术论文和计算机科学教科书的副本都化为灰烬。

我的观点倾向于与前面提到的作者的观点一致，尽管我愿意相信这样的技能(擅长白板面试)是关于拥有一套非常好的其他技能，软的和硬的。

### 先减轻情节

我会列出这个清单，因为你可能会涉及其中一些，但最重要的是，我还是要发泄一下😄

*   我没有用我的第一语言接受采访。在压力和现场情况下，用英语交谈突然变得更加困难。
*   我是一个自学前端开发人员。我缺乏学术准备。
*   在我的职业生涯中，我没有接受过很多采访。他们中没有多少人有白板面试这种测试。
*   我不太做公开演讲。不幸的是，目前还不多。

你可以反对这些都不是真正的减轻情节，你是对的。

从定义上来说，减轻处罚的情节是我们无法控制的:事实是我可以**提高我的英语**，**做更多的 CS** ，**做更多的采访**，**做更多的公开演讲**。

* * *

我会试着用那天我应该做的方式解决第一个白板测试，我会试着在这里报告这个过程。

### 测试

```
;['Tokyo', 'London', 'Rome', 'Donlon', 'Kyoto', 'Paris'][
  // YOUR ALGORITHM
  (['Tokyo', 'Kyoto'], ['London', 'Donlon'], ['Rome'], ['Paris'])
] 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。如果您旋转每个城市的字母，您可能会也可能不会匹配另一个城市。如果你这样做了，把它们放在一个单独的数组中。

在工作中，你每天都在逃离数千行代码的迷宫，向这些人展示如何解决这个简单甚至有点滑稽的问题有多难？拿着我的啤酒。

是啊…

让我们假设事情有所不同。

### 完美的白板面试

*朗朗上口的段落标题，虽然不对。这就是我解决这个测试的方法，包括代码和思想表达。谁知道还有多少其他方法！*

**我:**非常感谢。我很乐意为你解决这个问题(小弓)

**我:**第一个问题，字母只是‘旋转’还是可以随机混在一起？

**拉里**(为了保护面试官的隐私，我把他们的名字改成了一个很花哨的名字):轮换就好。第一个字母放在最后，等等。

**我:**对。

拉里:对。

**我:**对！我想首先我需要一种方法来轮换每个城市的字母。东京会变成东京，然后是京都，哦，太酷了！我们现在有京都了！好的，我需要一个函数来做这件事。

我:我还需要一种方法来循环输入的城市，“旋转”字母，做一些匹配，最后将它们分组在一起。我看到输入和输出都是数组，它们包含相同的值，唯一的区别是两个数组的深度，一个是平面数组，一个是数组的数组。

还是我:我现在要写一些伪代码来验证我的想法。稍后我也要测试一下，因为我很棒。😏

```
function groupCitiesByRotatedNames(cities) {
  // use a variable to contain the output.
  let output = []
  // loop through each of the cities
  //  rotate the name in any possible combination
  //  check if the output contains a city that matches a combination
  //   add that city to the array containing the match
  //   otherwise add the city to the output as a new array
  return output
} 
```

Enter fullscreen mode Exit fullscreen mode

自信如摇滚明星的我:让我们来伪测试我的伪代码(😛).

```
const input = ['Tokyo', 'London', 'Rome', 'Donlon', 'Kyoto', 'Paris']
console.log(groupCitiesByRotatedNames(input))
// That's how it would build up. The final output would be the last array:
// [
//     ["Tokyo"]
// ]
// [
//     ['Tokyo'],
//     ['London']
// ]
// [
//     ['Tokyo'],
//     ['London'],
//     ['Rome']
// ]
// [
//     ['Tokyo'],
//     ['London', 'Donlon'],
//     ['Rome']
// ]
// [
//     ['Tokyo','Kyoto'],
//     ['London', 'Donlon'],
//     ['Rome']
// ]
// [
//     ['Tokyo','Kyoto'],
//     ['London', 'Donlon'],
//     ['Rome'],
//     ['Paris']
// ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们开始实现这个算法。

首先，该实用程序旋转城市名称的字母。我将创建一个实用函数:

[![](../Images/6071e09e6eb52087248ff27ed3da79f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QB2-4Ri3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A5ZIRI7F8UHLyINpwKsxuqg.png)

我:让我们把这个函数弄得更流畅一点(可读性更差一点)。极度自信。

[![](../Images/7da4613844b069ed8e72a1678fc722c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--94RqgULa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A-9LrYX5VIlAidtcwYeMidQ.png)

**我:**对！我喜欢 reduce 方法，我会再次使用它！(它也是非常实用的程序，所以用在面试中很酷😎)

> `reduce()`方法对一个累加器和数组中的每个元素
> 应用一个函数(从左到右),将其减少到一个值
> ( [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) )。

我们的单个值就是期望的输出。

#### 我们来总结一下这个算法做了什么:

我们检查城市数组中的每个城市，我们轮换名称，我们将每个组合与累加器值进行匹配。如果我们找到了什么，我们就把新城市添加到包含匹配的数组中(用 [Array.splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) ),否则我们就[把包含新城市的新数组推给](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)。

就是这样:

[![](../Images/f78e1610fd1e6b6c276123ee512d9d6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YlER6ZMz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A2gJXt8ixBlEl5Vuh0M281A.png)

现在一起！

[![](../Images/0ff19e0bd1413647c14aabbd23a9883a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PBuEz-YC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AD2hQ-9ZV53aVnFvwjRcsbQ.png)

[在这里得到要旨](https://gist.github.com/albinotonnina/e5eb9589f3a2322678b75461ac230181)。

### 实际白板面试

我从解决方案开始。我说，好，我需要一个 reduce 方法。我立即接近董事会，在不真正了解计划的情况下编写实际代码。我确实有一个解决方案的想法，基本上就是我在前一章写的那个，但是我没有清晰地表达出来，我没有真正地向我可怜的面试官持续地解释。所以我迷失在代码中，我好几次失去了我的思路，我还在糟糕的伪代码和实际代码的混合中发现了语法错误。在这一点上没有办法扭转局势。像这样的表现会对面试的结果造成很大的损害，特别是如果你让这影响到你这边的其他过程。

### 现在该怎么办

练习，练习，练习。

拿一块白板，选择一个问题，对着空气大声说出来，然后写下来，多多练习。

练习白板面试，学习常规。你的套路，任何套路。像吉他上的一首歌，纸牌魔术或任何危险的特技一样学习它。

你需要准备一个演讲，它就像一个演示。

> 这就像一个带有变量的演示文稿。

一个变量就是问题。

我会这样走:

*   我将花一些时间分析需求(1 分钟)
*   如有必要，我会提出问题并得到答案(3 分钟)。
*   我会停下来想一个方向(几分钟，不要害怕保持沉默一会儿)。
*   我将提出初步解决方案，并从面试官那里获得反馈(5 分钟)。
*   我将再次暂停并选择一个解决方案(2 分钟)。
*   我将写伪代码(5 分钟)
*   我将测试我的伪代码(5 分钟)
*   最后，我将把伪代码转换成实际代码。(直到必要时)

大约 30 分钟后，我会以一种愉快而协调的方式解决这个问题。

### 结论

[![](../Images/26db53eb1aac431a08a31d23f546cf72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qulSZpMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ADw-_bnTlziQ_hucVUQ0uJA.png)

### 感谢阅读

感谢[亚当·麦金托什](https://twitter.com/agm1984)和[乔希·科莫](https://twitter.com/JoshWComeau)的校对:)

[![](../Images/093f8cf863d2d8c709628bc76eaaac49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zaEYIvVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Andz2zpAwq7qvQhhQI0ieLQ.png)

你好，我叫阿尔比诺·汤妮娜，我是一名前端工程师，目前在伦敦工作，做不好白板面试，你可以在 [Twitter](https://twitter.com/albinotonnina) 或 [Github](https://github.com/albinotonnina) 或 [Instagram](https://www.instagram.com/albino_tonnina/) 或城市周围找到我。