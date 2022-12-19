# 我的开发墓地，第 1 部分

> 原文：<https://dev.to/washingtonsteven/my-development-graveyard-part-1-3obc>

Github 墓地的概念是由 Isaac Lyman 在 [this dev.to post](https://dev.to/isaacandsuch/github-graveyards-ill-show-you-mine-49lh) 中提出的。这个想法是通过所有你从来没有接触过的旧项目，并记住你从哪里来，以帮助你到达你要去的地方。我立刻喜欢上了这个想法，并决定开始我自己的想法！

背景:这是一个简单的单页面网站，主要是测试 Github 页面是如何工作的。我在 2018 年新年的时候有了这个想法，创建一个明年的倒计时，看起来像《塞尔达传说:马约拉的面具》中的“剩余 x 天”卡片。

[![The Legend of Zelda: Majora's Mask - Dawn of the First Day](../Images/80ed9d7c330848bb24967a6c85aa11e2.png)T2】](///static/zelda-dc906193c8804853019bc397fc94300f-5979c.jpg)

**我学到了什么**

*   使用 Javascript 日期
*   匹配样式的附加练习

**死亡原因:**这其实是活在 http://aterriblefate.net[的](http://aterriblefate.net)。自从 2018 年 1 月 1 日的测试产生有限的结果以来，已经出现了一些错误(主要是由于间距和文本大小)。这从来都不是一个严肃的项目，所以我的注意力很快就被转移了。

## [华盛顿 steven.github.io](https://github.com/washingtonsteven/washingtonsteven.github.io)

背景:我的个人网站的一个旧版本，用 Jekyll 构建，用于托管在 Github 页面上(现在我用的是 Gatsby，在 Netlify 上托管)。

**我学到了什么**

*   使用 Jekyll 进入静态站点生成器的第一步
*   Github 页面/域管理

**为什么会死:**我不喜欢我想出来的设计，主要是。这也是在我一头扎进 react 之后，我渴望尝试更多基于 react 的项目(比如在 Gatsby！).

## [反应-JSON-组件](https://github.com/washingtonsteven/react-json-component)

**背景:**随着我对 React 了解的越来越多，我遇到了一些 JSX 做不到的情况。在这种情况下，我直接进入`React.createElement`来创建我的组件。我最终编写了一些 login 来获取一些特殊格式的 JSON，并输出实际的 React 组件。

下一步是创建一个库来为您干净地完成这项工作，而不是我编写的包装在其他应用程序特定逻辑中的逻辑

**我学到了什么**

*   更深入地研究 React 组件创建
*   顺便提一下，我还深入研究了在 npm 上创建和发布 React 组件的必要性(尽管这个组件从未发布过)。此外，考虑使组件足够通用，以适应人们可能会遇到的各种情况。

死亡原因:实际上，它基本上已经寿终正寝了。这更像是一次反应的练习，而不是一个最终的活生生的项目。安息吧。

## [摩根](https://github.com/washingtonsteven/genmo)

背景:在 [ReactVN 出现之前](http://stevenwa.sh/project/react-vn)，我第一次尝试文本冒险是一个小的命令行工具。我最终的计划是让这成为一个模拟，可以在一个虚构的世界中产生一系列的行为，并产生一个短篇故事提交给 [NaNoGenMo，国家小说产生月](https://nanogenmo.github.io/)。

**我学到了什么**

*   这是我第一次研究构建命令行工具，并深入研究节点系统调用
*   我还将摩根构建成一个 API，可以集成到任何类型的应用程序中，事实上，我还在 Slack bot 中加入了一个呼叫和响应类型的冒险。

**死亡原因:**我希望有一天能清理这个问题，但很多逻辑最终都变成了“意大利面条”,修复和修补一个又一个错误，而不是整体考虑问题。在这一点上，更新和修复将会非常麻烦，并且会局限于最初的设计思想。这里的下一步将是进行大规模重构，达到可能重写的程度。

* * *

这就是第 1 部分！像许多人一样，我过去有一堆项目要看，所以期待下一个吧！