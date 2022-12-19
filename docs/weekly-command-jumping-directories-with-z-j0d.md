# 每周命令:用 z 跳转目录

> 原文：<https://dev.to/roperzh/weekly-command-jumping-directories-with-z-j0d>

本周我们将回顾我最喜欢和最常用的命令之一。如果您经常使用命令行，那么您可能已经体验过这样的痛苦:调用目录结构并一遍又一遍地键入它们来访问彼此相距很远的目录。

`z`是专门为解决这一问题而设计的，它允许你*在目录中*跳转。

*感谢 [@luislavena](https://twitter.com/luislavena/) 与我分享`z`的存在。*

## 基础知识

```
z [*-chlrtx*] [*regex1 regex2 ... regexn*]
```

听起来很简单，`z`会直接带你到一个与你提供的正则表达式相匹配的目录，不管你在哪里。

值得强调的是，参数是一个正则表达式，如果你很懒的话，它特别有用:我通常输入我想要访问的目录的前两三个字母，这就足够了。

`z`非常容易安装，如果你急于自己尝试，请阅读自述文件中的[安装说明。](https://github.com/rupa/z)

一个重要的陷阱:`z`只能带你到你以前已经访问过的目录:由于它的工作方式，它需要一个学习阶段。

## 工作原理

通过将你访问的目录存储在一个平面文件数据库中，并根据这个列表匹配你的查询，它变得很神奇。该文件默认位于`$HOME/.z`中，但是可以通过`$_Z_DATA`变量改变目录。

工作得如此之好是因为这个目录列表是根据作者所说的“频率”排序的(这个术语显然是由 Firefox 开发者创造的，如果你有更多的信息，请告诉我！)结合了*频率*:你花在一个目录上的时间和*新近度*:你最近在一个目录上的时间。

<figure>[![A bash session using `z` on the left, the flat file database on the right, notice how it gets updated everytime a directory is visited.](../Images/df635c90b24cba5362c71b732906a29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fmJAQGQR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/4419992/38288175-64e10660-37a5-11e8-83b4-665b657adfc8.gif) 

<figcaption>
 *会话左边使用 z，右边使用平面文件数据库:注意每次访问目录时它是如何更新的。* 
</figcaption>

</figure>

当你继续浏览目录时，`z`保持的排名会根据一个简单的公式老化:

> 每次访问时，每个条目的排名都会增加。当等级之和超过 9000 时，所有等级乘以 0.99。等级低于 1 的条目将被忽略。

## 例子

| 命令 | 功能 |
| --- | --- |
| `z foo` | cd 到最常用的目录匹配 foo |
| `z foo bar` | cd 到最常用的目录匹配 foo，然后 bar |
| `z -r foo` | cd 到最高等级的目录匹配 foo |
| `z -t foo` | cd 到最近访问的目录匹配 foo |
| `z -l foo` | 列出所有与 foo 匹配的目录(按频率) |

## 来源

*   [z 库](https://github.com/rupa/z)