# 介绍 Faviator:一个简单易用的图标生成器

> 原文：<https://dev.to/ycmjason/introducing-faviator-a-simple-easy-favicon-generator-32g5>

[![logo](../Images/200b4933b76acf676bcd731223326c02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GnN5asJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.faviator.xyz/assets/logo.png)

> 如果你喜欢这个想法，就开始这个项目:[https://github.com/faviator/faviator](https://github.com/faviator/faviator)

## 快速链接

github:[https://github.com/faviator/faviator](https://github.com/faviator/faviator)T2】游乐场:[https://www.faviator.xyz/playground](https://www.faviator.xyz/playground)

## 故事

> (本节与**完全**无关，请随意跳到 TL；博士部分。)

我是一名音乐家。我写歌。我弹吉他，弹钢琴，唱歌。当我试图记录自己的演唱时，我发现一件绝对令人恼火的事情是:大多数歌词不适合一页纸。以约翰·梅耶(我最喜欢的艺术家)的这首歌为例。不知道你用的是什么显示器，但它完全不适合我的 12 英寸 macbook。这意味着我必须用鼠标来滚动页面。由于我的手已经在忙着弹吉他/钢琴，歌曲必须在录音中继续，这基本上是一个不可能完成的任务。

作为一名典型的软件工程师，我发现问题并寻求解决方案。我开始了一个叫做[拆分歌词](https://split-lyrics.ycmjason.com/)的项目。这个想法很简单:

1.  获取歌词的链接
2.  抓住歌词
3.  把歌词分成两半
4.  并排显示每一半。

现在我做了这个简单的应用程序。我给它设计了一点风格，开始想也许它应该有自己的图标。一些简单的东西，也许是一个圆形的正方形，蓝色背景，两个字母“SL”。

我打开 mac 上的应用程序仪表板，将鼠标悬停在[草图](https://sketchapp.com/)徽标上。我对自己说，为什么创建一个简单的图标就这么复杂呢？我总共要花多少时间来为我未来所有的随机项目创建图标？人类在创造一个图标上总共花了多少时间？(lol，这可能有点结束了，但是你明白了...)

作为一名典型的软件工程师，我看到一个问题并寻求解决方案。然后我创建了 [faviator](https://www.npmjs.com/package/faviator) 。在 TL 中继续阅读；节博士了解更多信息。

## TL；速度三角形定位法(dead reckoning)

所以基本上我做了一个名为[faviator](https://www.npmjs.com/package/faviator)(favicon generator)的库，它允许我们轻松地创建一个简单的图标。你可以在[游乐场](https://www.faviator.xyz/playground)和它玩一玩。

用法非常非常简单。只需传入一个配置，然后您就可以得到您的图标。就这么简单！你可以生成 SVG，PNG 和 JPG 的图标。

还有一个命令行工具，允许你不用写一行代码就能生成一个图标。

要安装命令行工具，只需执行

```
> npm install -g faviator 
```

Enter fullscreen mode Exit fullscreen mode

使用示例:

```
faviator --size '300'\
         --text 'F'\
         --dx '-2.5'\
         --dy '2'\
         --font-size '70'\
         --font-family 'Dancing Script'\
         --font-color '#ffffff'\
         --background-color '#f5beb7'\
         --border-width '3.5'\
         --border-color '#feeeec'\
         --border-radius '50' 
```

Enter fullscreen mode Exit fullscreen mode

上述命令将在`./favicon.png`生成如下图标。
[![logo](../Images/200b4933b76acf676bcd731223326c02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GnN5asJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.faviator.xyz/assets/logo.png)

字体家族是从谷歌字体中抓取的，你可以输入任何一种字体的名字。

除了你在上面看到的选项，还有 4 个选项，`--dx`、`--dy`、`--rx`和`--ry`，我鼓励你在 [faviator 游乐场](https://www.faviator.xyz/playground)自己找出它们是什么。

生成图标后，只需将它放入项目中，并添加一个指向它的链接标签:

```
<link rel="icon" href="/favicon.png" type="image/png"> 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

我想进一步简化创建简单图标的过程。我想扩展平台([https://www.faviator.xyz/](https://www.faviator.xyz/))并提供直接返回图标的端点。所以我们可以直接使用图标，甚至不需要安装 faviator。基本上 faviator 即服务将是下一个里程碑。你怎么想呢?给我一些想法！