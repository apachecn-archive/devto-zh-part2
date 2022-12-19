# 回归时尚:推特自举

> 原文：<https://dev.to/captainsafia/back-in-style-twitter-bootstrap-3345>

又是一个周三，又是一篇博文！我现在进入了坚持每周写三次博客的第四个月。三个月内有超过 36 篇博文。呜哇！我认为在这一点上，它正式算作一种习惯？

无论如何，我又回到了我一直在做的系列文章，研究流行的开源项目的第一次提交。今天，我想深入了解 GitHub 上最受关注的项目之一(也是我过去经常使用的项目)， [Bootstrap](http://getbootstrap.com) 。Bootstrap 是一个前端库，用于构建移动友好和响应迅速的网页。

该项目目前有超过 17，000 个提交，我一路回到了[提交#1](https://github.com/twbs/bootstrap/tree/eb81782cdbdc68aaebe4fa561b5fbb73ef866611) 。

```
commit eb81782cdbdc68aaebe4fa561b5fbb73ef866611 (HEAD)
Author: Mark Otto <markdotto@gmail.com>
Date: Wed Apr 27 13:53:51 2011 -0700

    Porting over all Blueprint styles to new Baseline repo 
```

有意思。所以看起来第一次提交实际上是从现有的样式库中移植一些代码。在查看了与初始提交相关的 README 之后，看起来“Blueprint”是 Twitter 开源之前内部的样式库的名称。

最初的样式表是使用 [Less](http://lesscss.org) CSS 语言扩展实现的。我相信最近，无论是版本 3 还是版本 4，Bootstrap 都迁移到了 Sass 语言扩展。

我还被最初提交的自述文件所吸引，其中列出了项目中的一些待办事项。

```
Write "Using Twitter BP" section **Two ways to use: LESS.js or compiled** Not meant to be 100% bulletproof, but is 90% bulletproof (stats?) **Advanced framework for fast prototyping, internal app development, bootstraping new websites** Can be easily modified to provide more legacy support

Add grid examples back in

Cross browser checks? Show this anywhere?

Add layouts section back in

Point JS libraries to public library links instead of within the repo 
```

我发现这个自述非常…我想真实是最好的词。对于开源项目的提交来说，这感觉非常合适。

我决定进行更多的时间旅行，看看项目接下来的几个提交是什么。

```
$ git log --pretty=oneline
0824ed4e3364bc0b49df89945dd69deff3ee7acd (HEAD) Updated docs styles for footer; updated readme;
b95e99a173f50dbbca2478338f42165532289db7 Updated documentation; added stacked forms; cleaned up spacing; moved all ids to the section element instead of the page header to fix spacing with bookmarked links;
b9d6acf766728b0bf28fe4d7644a80651f9b0e1c More documentation and content changes
677b5554f34d8206b7424796448ee1b5a9ba0e87 Remove the unnecessary global.js file, remove the old baseline grid image, add in hashgrid, update readme to remove finished todos;
eb81782cdbdc68aaebe4fa561b5fbb73ef866611 Porting over all Blueprint styles to new Baseline repo 
```

不出所料，如果你的公司最近开源了一个内部项目，你会做出很多这样的承诺:添加文档，例子，清理回购。我很欣赏这样一个事实，即这些类型的提交是公开的，而不是在开源之前。

我决定弄清楚 Bootstrap 第一次正式发布的时间。

```
git show --name-only v1.0.0
tag v1.0.0
Tagger: Jacob Thornton <jacobthornton@gmail.com>
Date: Thu Aug 18 11:45:49 2011 -0700

v1.0.0

commit ae02a8a30049844a8bd2f81e74c622196b4a8d2a (tag: v1.0.0)
Merge: bd1d38975 6d57f8a3d
Author: Mark Otto <mark.otto@twitter.com>
Date: Thu Aug 18 11:39:22 2011 -0700

    Merge branch 'master' of http://git.local.twitter.com/bootstrap

README.md 
```

事实证明，初始提交和初始发布之间的一切都与准备项目发布相关。

这是一个半有趣的探索。不可否认，项目早期的许多提交和代码都是我所期望的开源项目。

下一篇博文再见！