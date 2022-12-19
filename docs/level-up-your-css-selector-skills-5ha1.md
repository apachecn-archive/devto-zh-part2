# 提升你的 CSS 选择器技能

> 原文：<https://dev.to/bnevilleoneill/level-up-your-css-selector-skills-5ha1>

[![](../Images/a2da57eaa39cad10402503b8e5e331df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T-Itb0LQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ac-SHpWHyrJ2ozRbqI2U5sQ.jpeg)

我使用 CSS 已经很多年了，但是有一件事我直到最近才深入探讨，那就是 CSS 选择器的话题。

我为什么需要这么做？我们现在对选择器了如指掌，对吧？问题是(至少对我来说)随着时间的推移，很容易习惯于在每个项目中使用相同的可信任的选择器集来完成你需要做的事情。

所以我决定对 CSS 选择器做一个深入的回顾，并且发现了一些有趣的选择器，它们要么对我来说是新的，要么以一种我以前从未想到过的方式使用。

我还发现了一些很酷的新选择器，它们将在未来推出，但还没有广泛应用。

我邀请你和我一起看看各种类型的 CSS 选择器。其中有多少你已经在日常工作中使用了？我很想知道。

准备好提升你的 CSS 选择器技能了吗？那好吧，我们走。

### 组合子选择器

让我们从熟悉的领域开始。组合选择器被用来选择子元素和兄弟元素，并且已经存在很长时间了。

*   常规子选择器(空格)。例如 A B
*   直接子选择器。例如 A > B
*   相邻兄弟选择器。例如 A + B
*   通用兄弟选择器。甲~乙

相邻的选择器 A + B 你应该很熟悉，选择紧跟着 A 的元素 B，但是一般的兄弟选择器 A ~ B 呢？这将选择 a 之后的所有兄弟元素 B。

这里有一个它们都在起作用的例子:

[https://medium.com/media/62c0884db0465a2fafb41d8f3a16a665/href](https://medium.com/media/62c0884db0465a2fafb41d8f3a16a665/href)

选择 New York 行是因为它紧跟在第一行之后，最后两个城市被突出显示，因为 general sibling 选择器匹配第四个城市之后的所有城市。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 属性选择器

我真的很喜欢属性选择器。当您需要将包含属性的元素与特定值进行匹配时，它们非常灵活。

[https://medium.com/media/3df63743e5520581894d5df62e87d715/href](https://medium.com/media/3df63743e5520581894d5df62e87d715/href)

这个例子演示了选择所有的复选框输入元素，并对它们相关的标签应用样式，使它们加粗并染成蓝色。

然后，我们用特定的名称 chk2 覆盖复选框的样式，并将其关联的标签涂成红色。请注意，其他表单元素标签不受影响，也没有应用标签样式。

属性选择器不仅仅用于表单元素，它们还可以针对任何元素的属性。您可以匹配任何属性，而不仅仅是官方支持的属性。此外，您可以检查属性的存在，如下所示:

```
button[icon] 
```

这匹配包含图标属性的<button>元素。它将匹配属性，无论它是空的还是设置为特定值。</button>

更多示例:

[https://medium . com/media/c 15 a 68 ed 07 c 93 e 48 ceca 45d 618772 c 10/href](https://medium.com/media/c15a68ed07c93e48ceca45d618772c10/href)

第一个链接没有目标属性，因此不匹配。接下来的两个链接是匹配的，因为它们要么具有空的目标属性，要么具有特定的值。最后，最后一个链接被设置为粉红色，因为它匹配 fluffy 属性。它的值是不相关的，只是必须存在以匹配 a[fluffy]选择器。

一个实际的例子是突出显示没有 alt 属性的图像。该属性是可访问性所必需的，因此确保所有图像元素都包含该属性对于 SEO 非常重要。

我们可以使用下面的规则来实现这一点:

```
img:not([alt]) {
 border: 2px red dashed;
} 
```

[https://medium . com/media/87765 a 30 b 26 a 1316 CB 829 e 604 af 99432/href](https://medium.com/media/87765a30b26a1316cb829e604af99432/href)

如果您想要匹配属性值的特定部分，那么有一些非常有用的选择器可用。

A[attr <sup>=val] —属性</sup>以值开始。

A[attr|=val] —属性以值开始，或者是以破折号分隔的列表中的第一个。

A[attr$=val] —属性以值结束。

A[attr*=val] —值出现在属性中的任何位置。

A[attr~=val] —值匹配空格分隔列表中的属性。

下面是每种方法的一个例子:

[https://medium.com/media/b38466141b43ade5415217ee13f309cb/href](https://medium.com/media/b38466141b43ade5415217ee13f309cb/href)

前两个示例非常相似，只是[attr|=val]也匹配后跟破折号分隔的字符串的值。这对于匹配语言属性很有用。例如

。

使用[attr$="val"]和::after 可以很容易地匹配文件扩展名，也可以很容易地显示匹配的文件。请注意 attr()和连接的使用，用一个静态字符串连接它。

A[attr*=val]显示了无论使用什么协议或子域，如何匹配特定的域。

最后，我们有一个[attr~=val],它非常适合匹配由空格分隔的值列表组成的属性中的值。这只匹配整个单词，而不是单词片段，因为*=操作符会匹配单词的复数形式。

以上所有属性选择器的例子都是区分大小写的。但是我们有锦囊妙计。如果我们在右方括号前插入一个 I，我们就可以打开区分大小写的匹配。

[https://medium.com/media/f29fd8e85539763d32c07f0aa3c3a71f/href](https://medium.com/media/f29fd8e85539763d32c07f0aa3c3a71f/href)

除了 Internet Explorer 和 Microsoft Edge 之外，大多数主流浏览器都支持不区分大小写的匹配。

### 用户界面选择器

如果您曾经处理过样式表单，那么您肯定以前遇到过这些类型的伪类:

*   :已启用
*   :已禁用
*   :已检查

例如，我们可以使用:选中以样式化一个简单的待办事项列表。

[https://medium.com/media/f99000113072f13e53f88b347886757e/href](https://medium.com/media/f99000113072f13e53f88b347886757e/href)

这是非常标准的，但是我们还有其他一些有趣的伪类。

:default 匹配一组相关元素中的一个或多个默认元素。这也可以与重置按钮类型相结合。

[https://medium.com/media/bae750c17c2114ff9af8a408c896e66c/href](https://medium.com/media/bae750c17c2114ff9af8a408c896e66c/href)

我们可以使用伪类直接用 CSS 匹配输入值是否有效，以及在提交表单之前检查是否需要任何元素。

*   :有效
*   :无效
*   :必需
*   :可选(即非必需)[https://medium . com/media/92285467 f 37051 D8 C7 a82d 8482 ea 35 b 5/href](https://medium.com/media/92285467f37051d8c7a82d8482ea35b5/href)

如果你开始在个人电子邮件输入框中输入，那么它必须是有效的。但是，工作电子邮件地址始终是必填的，并且必须有效，因此不能留空。还要注意我们如何链接伪类(例如:required:invalid)来实现我们所需要的。

接下来，我们有两个伪类可以匹配一个表单元素(支持 min 和 max 属性)是否在范围内。

*   :在范围内
*   :超范围[https://medium . com/media/f 64 b 6 bed 4 ea 9 c 51 ACF 9 e 55 a 75 e 86 b 25d/href](https://medium.com/media/f64b6bed4ea9c51acf9e55a75e86b25d/href)

同样，我们可以使用 reset 按钮类型来重置 number input 元素的默认值。

为了圆满完成这一部分，让我们来看看:read-only、:read-write 和:placeholder-showed 伪类。

[https://medium.com/media/3f99dcfdf3cf122ee23a744ab3bf80b8/href](https://medium.com/media/3f99dcfdf3cf122ee23a744ab3bf80b8/href)

使用这些可以让您轻松匹配只读或可写(可编辑)的元素。匹配的元素也不一定是表单输入字段，如示例笔中所示。

最后，:placeholder-showed 将匹配尚未交互的元素，并且仍然显示默认的占位符文本。这个特殊的选择器应该小心使用，因为它还没有得到广泛的支持。

### 结构选择器

结构选择器非常强大，它根据元素在 DOM 中的位置匹配元素。它们让您可以灵活地用 CSS 匹配元素，否则需要 JavaScript 来做同样的事情。

这种类型的选择器不同于目前展示的选择器，因为其中一些选择器允许您传递一个参数来修改选择器的工作方式。

例如:nth-child()接收一个值，该值将匹配相对于其父容器的特定子元素。

因此，如果我们有一个条目列表，下面的选择器将匹配第三个条目:

```
ul:nth-child(3) 
```

然而，参数不必是简单的数字，它可以是一个简单的表达式，这使得伪类更加强大。

有效表达式包括:

*   ul:第 n 个子元素(2)-匹配第二个子元素
*   ul:第 n 个子元素(4n) —匹配每第 2 个子元素(4，8，12，…)
*   ul:第 n 个子元素(2n + 1) —匹配每第 2 个子元素，偏移量为 1(1，3，5，…)
*   ul:第 n 个子元素(3n-1)—匹配每第 2 个子元素，偏移量为负 1(2，5，8，…)
*   ul:n-child(奇数)-匹配奇数编号的元素(1，3，5，…)
*   ul:第 n 个子元素(偶数)—匹配偶数编号的元素(2，4，6，…)

表达式变量 n 总是以零开始，所以为了精确地计算出哪些元素将匹配，从 n 作为零开始，然后 n 作为 1，依此类推，以编译元素列表。

您可以将简单表达式与以下结构选择器一起使用:

*   :第 n 个子代()
*   :第 n 个最后一个孩子()
*   :第 n 个类型()
*   :n-last-of-type()[https://medium . com/media/d 9d 014 EC 25 ce ACF 054 ad 1 ca 18 e 293d 90/href](https://medium.com/media/d9d014ec25ceacf054ad1ca18e293d90/href)

:n-last-child()和:n-last-of-type()与:n-child()和:n-of-type()非常相似，只是它们从最后一个元素而不是第一个元素进行匹配。

通过尝试不同的组合，你可以很有创造性地使用选择器。例如，前面的笔示例包含选择器:

```
ul:last-of-type li:nth-last-of-type(2)::after {
  content: “ (2nd from end)”;
  /\* Other styles… \*/
} 
```

这与第二个无序列表中倒数第二个列表项之后的伪元素相匹配。如果你正在努力解码一个复杂的选择器，那么最好是从右到左阅读，这样就可以从逻辑上解构它。

下一组选择器是专门的结构化选择器，因为它们只匹配特定的子元素。你不能给他们传递表达式来修改他们的行为。

*   :第一个孩子
*   :最后一个孩子
*   :独生子女
*   :一流的
*   :last-of-type[https://medium . com/media/8571 c 883 a 687450 e 9 b 65 f 5898 bb 19549/href](https://medium.com/media/8571c883a687450e9b65f5898bb19549/href)

乍一看，这里发生了很多事情，使用这些类型的选择器时需要小心，因为可能会得到意想不到的结果。

例如，您可能想知道为什么标签中的**等等……**文本是蓝色的。实际上，所有的部分内容都是蓝色的，因为它是主 div 容器的最后一个子容器。其他部分元素通过其他选择器覆盖它们自己的颜色，使单个段落保持蓝色。

### 内容选择器

这些属于用于匹配内容的一组专门的选择器。可供我们立即使用的有:

*   *一线
*   *首字母
*   * selection[https://medium . com/media/52 acedd 023 b 7199768 DBE 3c 77d 44556 b/href](https://medium.com/media/52acedd023b7199768dbe3c77d44556b/href)

* first-line 和::first-letter 仅适用于块级元素。也要小心，只在特定的元素上使用::首字母，否则每个段落都会有首字下沉，这可能不是你想要的！

目前还没有一些令人兴奋的内容选择器，但是当它们被支持的时候，它们将会打开所有的可能性。

以下是需要注意的内容选择器列表:

*   非活动选择——非活动窗口内的选定内容
*   *拼写错误——检查可编辑元素的拼写和语法
*   语法错误—匹配语法错误
*   标记—匹配列表项标记
*   *占位符—匹配表单元素的占位符文本

### 杂项选择器

我们刚刚有时间提到几个不属于前面任何类别的选择器。别担心，我们快完成了！不幸的是，其中大多数都是实验性的，所以您必须等待一段时间才能在生产中使用它们。

:target 选择器将 id 与当前 URL 的一部分相匹配的元素作为目标。因此，如果我们有一个 id 为 part1 且 URL 为:
的元素

```
[https://mysite.com#part1](https://mysite.com#part1) 
```

我们可以将该元素与:
匹配

```
:target { border: 1px red solid; } 
```

如果您有一个大的选择器，那么:matches()可以帮助简化它。例如，如果您有以下选择器:

```
nav p.content,
header p.content,
main p.content,
sidebar p.content,
footer p.content {
  margin: 15px;
  padding: 10px;
} 
```

那么可以简化为:matches()并且等价于:

```
:matches(nav, header, main, sidebar, footer) p:content {
margin: 15px;
padding: 10px;
} 
```

不错！这将有助于使样式表更具可读性。

接下来我们有:any-link，它是一个方便的选择器，与:link 和:visited 组合在一起的功能相同。

所以这两个选择器实际上是相同的:

```
:any-link {
  color: red;
}
:link, :visited {
  color: red;
} 
```

这就把我们带到了本文中要讨论的最后一个选择器:

*   :目录()
*   :郎()

这两者都与你网站的语言有关。

:dir()接受一个参数 ltr 或 rtl，这取决于你想要匹配的文本的方向，目前只有 Firefox 支持它。

所以:dir(rtl)将所有元素与 rtl 方向的内容相匹配。

HTML 文档中的每个元素都可以使用 lang 属性设置自己的语言。

```
<div lang=”en”>The language of this element is set to English.</div>
<div lang=”el”>Η γλώσσα αυτού του στοιχείου έχει οριστεί στα ελληνικά.</div>
<div lang=”is”>Tungumál þessa þáttar er sett á íslensku.</div> 
```

相同的基本文本被输入到三个

tags but with the specific country added to the end of the content. Also, the country codes used in the lang attribute represent the corresponding country.

*   英语语言
*   el —希腊语
*   is —冰岛语

这

elements can be matched using the :lang() selector:

```
:lang(en) { colore: red; }
:lang(el) { colore: green; }
:lang(is) { colore: blue; } 
```

这里有一支笔来演示:

[https://medium . com/media/370 d54 C1 A8 c 0 aa 521 aa 159 DBF 136 c 375/href](https://medium.com/media/370d54c1a8c0aa521aa159dbf136c375/href)

好消息是:lang()选择器已经得到了所有主流浏览器的支持。

### 资源

如果您在试图找出一个选择器时遇到困难，或者需要更深入地研究 CSS 规范，这里有一些有用的资源，您可能想看看:

*   [MDN 网络文档](https://developer.mozilla.org/en-US/)
*   [CSS 规范](https://www.w3.org/Style/CSS/specs.en.html)
*   我能使用吗

### 最后…

我希望这篇文章对你有用。我在温习 CSS 选择器技能和尝试各种可能性的过程中获得了很多乐趣。

现在你可以用纯 CSS 做很多很酷的事情，这在几年前是不可能的。

这让那些可以用纯 CSS 做一些非常高级的样式和动画的设计者很兴奋，而且所有这些都不需要一行 JavaScript 代码。

快乐造型！

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *