# 什么叫不能再编辑了？

> 原文：<https://dev.to/jermdavis/what-do-you-mean-it-s-not-editable-any-more-307h>

所以，当我从 Sitecore 研讨会回来时，时差让我很难受，这个问题上周突然出现在我的 bug 队列中。QA 报告测试页面上的某个组件不允许编辑一个字段。它在过去曾经工作过，但是行为突然改变了，以至于一个字段在体验编辑器中不再有“你可以编辑这个”的覆盖。我花了比预期更长的时间来找出原因…

## 问题:

在体验编辑器中打开“显示控件”会显示以下内容:

[![](../Images/b86308c3946cbc52e400bffc976a9304.png)T2】](https://jermdavis.files.wordpress.com/2018/10/fieldyoucantedit.png)

该页面的其他栏都是可编辑的，但“这段文本…”栏是不可编辑的。奇怪的是，它上面出现了一个空的编辑控件。查看控件后面的视图，有两个可编辑的字段:

[![](../Images/b8ad0802e01516c95288ef9d3ac18367.png)T2】](https://jermdavis.files.wordpress.com/2018/10/badfieldview.png)

但是在您在浏览器中看到的标记中，似乎有一个可编辑的字段，里面什么也没有，后面是我们字段的文本，再后面是一个空元素:

[![](../Images/81b49fbfe32b1e0dacf432f594c1fc9b.png)T2】](https://jermdavis.files.wordpress.com/2018/10/badfieldmarkup.png)

那里发生了什么事？

## 被 HTML 规范咬了！

答案真的很简单:

元素有关于它们被允许包含什么的规则。规则说你可以在一个段落中放入“[语法内容](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_categories#Phrasing_content)”。并且那些规则排除了其他的< p/ >元素，或者< div/ > s

损坏字段的内容是富文本，查看它包含的 HTML:

[![](../Images/13bd20a44e94167ac5ef8fa71d548254.png)T2】](https://jermdavis.files.wordpress.com/2018/10/fieldcontents.png)

标记中还有另一个

…

这就是破坏编辑行为的原因。玻璃编辑控件在一个

内，但该字段包含一个

。所以浏览器看到了无效的 HTML，并在字段内容的

开始之前关闭了第一个(视图)

。这意味着 Sitecore 的体验编辑器标记被应用于第一个(空的)元素，而不是浏览器创建的第二个元素——其中包含字段的文本…

## 所以……

要记住的关键是:当你为你的视图做 HTML 时，不要把

元素放在富文本字段周围，除非你想把富文本限制为在

中有效的元素...