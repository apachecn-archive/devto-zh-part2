# 世界上最简单的“静态站点生成器”

> 原文：<https://dev.to/gypsydave5/write-and-deploy-a-super-fast-web-site-in-30-seconds-with-no-framework-4lab>

开博客最快的方法是什么？Wordpress？哲基尔？数百个静态站点发生器中的任何一个？

不是，是`pandoc`和`now`。

## 什么是`pandoc`？

[Pandoc](http://pandoc.org/) 是一个“通用文档转换器”——一把将文档类型 a 转换为文档类型 b 的瑞士军刀。它适用于许多事情，但今天它将适用于将 Markdown(我们喜欢写的内容)转换为 HTML(我们在网站上阅读的内容)。它可以通过我用过的几乎所有软件包管理器安装——在 Mac 上，只是`brew install pandoc`。

## 什么是`now`？

[现在](https://zeit.co/now)是来自 Zeit.com 的一个有用的产品，用于快速部署应用*。这是伟大的投掷东西，看看它是如何工作的。它将提供 Docker 图像、NodeJS 应用程序和静态站点。一开始我并不相信，但现在我相信了。今天，我们用它来部署我们的静态博客网站。用简单的`npm i -g now`就可以安装，但是还有其他方法。*

 *## 各就各位，准备集合...

所以，确保你已经安装了`pandoc`和`now`，你在一个干净的目录中，并且你已经连接到互联网。你知道你最喜欢的编辑器是什么。如果你没有最喜欢的，挑一个你最不讨厌的。

## ...走吧。

在你最不讨厌的编辑器中快速打开一个名为`hello-world.md`的文件，写下如下内容:

```
# Hello World

Hello world, this is the world's fastest blog! 
```

Enter fullscreen mode Exit fullscreen mode

省省吧-快点，快点！现在在终端中运行这个。什么？我没告诉你要开终端会议吗？快，打开一个运行:

```
pandoc --output=index.html --to=html5 --standalone hello-world.md 
```

Enter fullscreen mode Exit fullscreen mode

忽略警告，我们没有时间解释！马上发货！现在！现在！

```
now 
```

Enter fullscreen mode Exit fullscreen mode

答应我。

`now`会把你的`index.html`放到网上。它甚至会把上传的网址放到你的剪贴板上。所以，打开你最不讨厌的网页浏览器，放入网址。

成功！

另外 15 秒是沐浴在你成就的光芒中——这是你应得的。

## 更？

什么，你想在你的博客里有不止一篇博文？你是疯了还是贪心？还是两者都有？如此奢华，千禧一代被宠坏了，还被称为“雅哒雅哒雅哒”...

当然可以！试试这个:打开一个名为`my-second-post.md`的新文件，在里面写你的第二篇文章——我不在乎你写些什么！

现在写下`index.md`——像这样:

```
# My Quick Blog

- [Hello World](hello-world.html)
- [My second post](my-second-post.html) 
```

Enter fullscreen mode Exit fullscreen mode

现在

```
pandoc --output=index.html --to=html5 --standalone index.md 
```

Enter fullscreen mode Exit fullscreen mode

```
pandoc --output=hello-world.html --to=html5 --standalone hello-world.md 
```

Enter fullscreen mode Exit fullscreen mode

```
pandoc --output=my-second-post.html --to=html5 --standalone my-second-post.md 
```

Enter fullscreen mode Exit fullscreen mode

最后，再一次

```
now 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍好！将新的 URL 粘贴到您的浏览器中...

嘣！你现在有一个博客，有一个索引页和两篇文章。跳一小段舞——您已经使用现有工具创建了一个静态站点生成器——这是 Unix 的方式。万岁！

## 可以尝试的东西

*   我们是程序员——我们不喜欢做两次事情！写一些东西来循环你目录中的`.md`文件，用`pandoc`把它们变成`.html`，而不是手工处理每个文件。Bash，Ruby，JavaScript——任何最简单的！
*   每次部署博客时都必须更改它的 URL，这一点都不好玩。有一种方法可以将一个部署别名化为一个永久的 URL——为什么不看看这是如何实现的呢？
*   你的博客*丑*。不打算撒谎。你应该添加一些 CSS。`pandoc`有一个在 html 中包含 CSS 文件的方法——你需要在你的`pandoc`调用中添加标志`--css=file.css`(一旦你写了一些好看的 CSS)。
*   不要忽视警告！看看如何将元数据添加到你的 Pandoc markdown 中——这些都在 Pandoc 文档中。

玩得开心！

* * *

编辑:感谢@calendee 指出我的错误！*