# 网页设计速成班:从一个非设计师到另一个

> 原文：<https://dev.to/aspittel/a-web-design-crash-course-from-one-non-designer-to-another-2o2m>

首先我要说，我不是专业的设计师。也就是说，我喜欢建造漂亮的东西，并在这方面取得了一些成功。许多开发人员似乎被设计搞得不知所措，所以我想写一篇关于如何改善你的网站视觉效果的快速提示的文章。规则是用来打破的，但是如果你想尽可能容易地创建一个伟大的网站，这里有一些很好的经验法则！

## 颜色

我真的很喜欢在我的网站上玩颜色，但需要有一个平衡和协调的配色方案，让网站看起来有凝聚力和专业。重要的是，这些颜色不冲突，它们有一个合理的对比度，并且它们是一致的。

### 一致性

下面是我对编程的禅宗的配色方案:

[![](img/b2ffe7a2ff06378a7e62b9f45d5e52c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rnEExeU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ilzz2bi90odgmd31kaa.png)

我使用相同的十六进制代码为绿色，粉红色和灰色为我的背景，标题，文字和按钮。

您可以使用 CSS 变量来确保您的颜色在整个代码中是相同的。

```
body {
  --pink: #CF92B7;
  --green: #59876B;
  --grey: #4A4A4A;
}

h1 {
  color: var(--pink);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用 SCSS 或其他 CSS 预处理器，这甚至更容易！

就你的主题所涉及的颜色数量而言，四种颜色通常是个不错的选择。也许可以选择其中的两种中性色(如黑色、白色或灰色)和两种更鲜艳的颜色。单色配色方案使用一种颜色的阴影肯定也可以工作！

当我使用彩虹配色方案时，我将彩虹视为一种颜色，然后对其他颜色使用两种中性色。例如，在 alispit.tel 上，我用深灰色的文字，白色的背景，然后用彩虹色的字母和随机的形状。

[![](img/0687b3eef54fa3009b56f22cfafd5262.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8TtQOHn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqzx4w8xiutfc8k8vths.png)

### 色彩冲突

确保你们的颜色搭配起来很好看，而且不冲突，这很重要。通常情况下，色轮上彼此相反的颜色会发生冲突。当然，你可以利用冲突的颜色制作优秀的网站，但这更困难，最好留给设计师。一些例子是红色和绿色或橙色和紫色。

此外，记住颜色的色调——如果你用的是冷色调的粉色，冷色调的绿色可能比暖色调的绿色更好搭配。暖色调的颜色底色更红，冷色调的颜色更蓝。

### 对比

确保你网站上的颜色之间有一个合理的对比度。如果没有，阅读你的内容会困难很多。这里有一个很棒的网站，可以帮你检查你的对比度。灯塔测试也会帮你检查这个！一般来说，浅色放在深色上面，深色放在浅色上面！

[![](img/b8836282bd1cf164cbcd91f6c3cd8195.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S25hY6s2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dghbsk1rdvfg7x64adhl.png)

### 颜色含义

如果你在一个品牌的网站上工作，不同的颜色对读者来说有不同的隐含意义，它们可以传达不同的情绪，所以记住这些是有帮助的。

### 选择颜色的小技巧

有很多很棒的网站可以让你更容易挑选出协调的调色板。

当我建立一个新网站时，我有时会使用 Coolors 发电机。我在 Instagram 上也关注[这个账号](https://www.instagram.com/colours.cafe/)，看到喜欢的调色板就会保存下来。最后，如果我只想看到一堆颜色并从中选择，我会使用物化图上的[颜色](https://materializecss.com/color.html)页面——我喜欢它们的颜色！

## 文本

在大多数网站上，文字将是最关键的部分。你需要它易于阅读，并强调最重要的内容。

这是一个很难读懂的文本的例子:

[![Example of hard to read website content text](img/7bb385beea8a98b3364f23cadf303113.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0l8fg6ME--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnfaittf0ieri65c9ssw.png)

让我们努力使它更具可读性！

### 间距

为文本内容增加间距。有几种类型的间距可以使你的内容更具可读性。第一个是在页面两侧添加填充。

#### 在你内容的两侧

在很多网站上——文本不会跨越页面的宽度，它会在一个容器内，只占据页面的一部分。这使得内容阅读起来更舒服，因为你的眼睛需要移动得更少，页面会看起来更好。W3C 建议每行少于 80 个字符。

```
<style>
  .container {
    width: 80%;
    margin: 0 auto; /* centers the div on the page */
  }
</style>

<div class="container">
  <p>
    Lorem ipsum dolor amet master cleanse cloud bread brunch pug PBR&B actually. 
    Thundercats marfa art party man bun gluten-free sriracha. DIY tofu cred blue bottle etsy. 
    Retro listicle normcore glossier next level etsy lumbersexual polaroid pour-over 90's 
    slow-carb health goth banjo. Unicorn chicharrones 8-bit poke glossier.
  </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

#### 行高

我们可以增加行高来增加文本行之间的空间。最新的 Android 操作系统为通知增加了更大的行高，这使得它们更容易一目了然。

此外，为了便于访问，最好增加更多的行高 W3C 推荐的是 1.5 到 2.0。还记得高中得翻一倍的空间作文吗？我们在这里做同样的事情，只是这次在线！

```
p {
  font-size: 18px;
  line-height: 2.0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将把下图中左边的文字转换成右边的文字。

[![Line height demo](img/cfd3d33da57d64f9fbeed4a5c647adbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4CzTwBve--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cbisc63b25u5zeqineqv.png)

#### 段落填充

我还会确保在你的段落之间添加填充，以便于区分它们。

```
p {
  padding-bottom: 27px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Paragraph padding demo](img/9d6409316ed88227da6dea1f7da8caab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HGGWeOtY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hdmjzfvp8amrybxuom3n.png)

#### 字间距

如果您在标题中使用全大写字母，您可能希望在单词之间增加更多的间距，以便更容易区分它们。

这是我博客的标题，增加了单词间距:

[![](img/d13db09dc5adeb84ade60609dd08de85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GAih_wzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nmomzzh6cjgux3hla3wz.png)

这里没有:

[![](img/662e537b853b1dddb5d65498e02d1073.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uqMCE-1O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bxwy39ckyepsy9x67bos.png)T3】

```
h1 {
  word-spacing: 9px;
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个更容易阅读！

### 对齐

如果内容靠左对齐，并且对于英语和其他从上到下从左到右的语言没有对齐，那么阅读起来就很容易。你的内容默认会这样！只要确保上面没有`text-align: center;`就行了！你可以随意将你的标题或者你的文本放在中间，但是要让长的内容块靠左对齐。

### 可读字体

有些字体比其他字体更容易阅读。对于 web 上的内容，阅读无衬线字体通常更容易。衬线是一些字体中字母末端的小点——你可以在下图的绿色圆圈中看到衬线！

[![Sans-serif vs serif font](img/bd982a8e23361ea9944c406fd1a5d9ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FgTqM-GM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3tcr6ctnasr68j398dt.png)

我的经验是，每个网站的标题使用一种装饰字体(想想草书或者非传统字体)或者衬线字体，内容使用无衬线字体。

#### 我最喜欢的一些字体:

**衬线和装饰**

*   太平洋
*   [义](https://fonts.google.com/specimen/Righteous)
*   [龙虾](https://fonts.google.com/specimen/Lobster)

无衬线

*   [机器人](https://fonts.google.com/specimen/Roboto)
*   侧
*   [Montserrat](https://fonts.google.com/specimen/Montserrat)
*   [Fira Sans](https://fonts.google.com/specimen/Fira+Sans)
*   Arial -这个在你的电脑上

### 颜色

由于对比度太大，白底黑字会导致眼睛疲劳。我使用深灰色作为我的内容。然后，仍然有很多对比，但没有黑色文本那么多。

```
p {
  color: #4A4A4A;
} 
```

Enter fullscreen mode Exit fullscreen mode

背景也是一样，纯黑通常不是最好的选择，深蓝色或灰色会让你的内容更容易阅读。

### 上浆

对于你的内容应该有多大，应该用什么度量单位(比如 em 和 px)，有不同的看法；然而，大多数人倾向于 16-18 像素，并为在你的网站上放大或缩小的人提供这个比例。

不同类型的标题应该比你的内容大。此外，使用不同的字体粗细来区分文本。

[![](img/a965d04f8c3ea054eed71a6031fd8e85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fz29zYIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrph5ceq4a65eay8ius4.png)

## 一旦我们改变了这些东西...

我们的内容变得更容易阅读！

[![](img/147a5e21a4b3ffb4e4b2565103e84e40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EHI05k9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/avf7izy6wupe3b2avwbw.png)

## 背景

让背景图片看起来不错，尤其是上面有文字的时候，真的很难。我会避免背景图片，除非它们对网站至关重要。如果你仍然决定使用背景图片的话，我将给出一些替代方案和解决方案。

在下图中，文字很难阅读。

[![](img/05dffa278d43917b32227bb91570db5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FtGA3I7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37e48lmj1kntlvvrdjfl.png)

### 分割页面

如果您想保留图像，可以拆分页面，使图片显示在一半，文本显示在另一半。

[![](img/66e46e6ecce093527ef392c41b23c900.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sxyt7QrA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zd2b9sinuafl0jcyjmr4.png)T3】

```
<style>
  .container {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
</style>

<div class="container">
  <div class="text">
    My Text
  </div>
  <img src="path/to/img">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 使用不太忙的图片

如果你想保留整个页面的背景图片，如果可能的话，使用简单的图片，不要太多。在我上面使用的这个例子中，有很多颜色和大的文本，用户仍然可以阅读。使用一幅风景或图案的图像会减少干扰。

### 添加文字阴影

您还可以在文本下方添加文本阴影，以提高可读性。

```
.text {
  text-shadow: #4A4A4A 1px 1px 8px;
} 
```

Enter fullscreen mode Exit fullscreen mode

随着上面的改变和字体大小的增加，背景图片上的字体变得更容易阅读！

[![](img/4099761d536e3678959fcfe1850a6f99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YvmBFGIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4v847v222h2alswqb6jm.png)

### 背景颜色

我不会用超级亮的颜色作为你的背景。几乎任何颜色都很难在它上面阅读，这可能会使人们的眼睛疲劳。

## 号召行动

当你建立一个网站时，通常会有一些你想让用户做的事情。在电子商务网站上，你希望用户买些东西。对于投资组合，你可能希望观众联系你。在博客上，你希望用户订阅。当你设计一个网站的时候，记住这个动作。你可能希望在网站的多个地方使用明亮的颜色、较大的文本或“号召行动”的特征。你可能还想确保它在网站上随处可见。

例如，我博客的订阅表单总是在侧边栏中，并且上面有颜色来吸引读者的注意。在我的文件夹中，我在主页、关于和联系页面上有指向我的社交媒体账户的链接。

强调重要的内容在每个网站上看起来都不一样，但是在你做的每个设计选择中，记住你希望用户采取的行动是很重要的。

## 更多阅读

*   [粉碎杂志](https://www.smashingmagazine.com/articles/) -这里有很多设计文章
*   [用草图设计](https://dev.to/aspittel/designing-with-sketch-42jp)——我写的一篇关于如何使用设计软件 Sketch 的文章，我用它来设计一切。许多步骤也适用于其他设计软件。
*   这是一本非常棒的书，讨论面向初学者的网页设计。
*   UI 设计的小细节——一个出色的幻灯片，经历了 LinkedIn 的重新设计——这个更高级一点。

## UI 框架

当你设计一个网站时，你可能不想从头开始，所以使用 UI 框架可能会有帮助。其中一些包括 [Bootstrap](https://v4-alpha.getbootstrap.com/) ，我会确保定制它，因为它被大量使用；[物化](https://materializecss.com/)，遵循 Google 的材料设计理念；还有[索环](http://grommet.io/)，我最喜欢的美学设计——它也强调了易用性。

## 设计检查表

因为这是一篇有很多技巧的相当长的帖子，我想把最重要的信息提取到一个清单中。它是用 GitHub markdown 编写的，所以你可以把它复制并粘贴到一期杂志或 markdown 文件中。然后，您可以选中该框，或者用一个`x`替换每个行动项的空格。

```
## Color
 - [ ] Uses consistent colors throughout
- [ ] Uses non-clashing colors
- [ ] The color choice makes sense for the business purpose of the site

## Text
 - [ ] The text has padding on both sides.
- [ ] The line-height is 1.5-2.0
- [ ] There's padding between paragraphs
- [ ] If your headings are in all caps, there's increased word spacing
- [ ] Blocks of text are un-justified
- [ ] Blocks of text are left-aligned
- [ ] Sans-serif fonts are used for body text
- [ ] A maximum of two fonts are used - one decorative or serif and one sans-serif
- [ ] body text isn't pure black on white
- [ ] body text is 16-18px and is scalable

## Backgrounds
 - [ ] Use a pattern or simple image
- [ ] A text shadow is used to make headings readable
- [ ] The background isn't too bright

## Calls to Action
 - [ ] Important information is highlighted in order to attract user interaction 
```

Enter fullscreen mode Exit fullscreen mode

## 保持联系

如果你喜欢这篇文章并想阅读更多，我有一个[每周简讯](https://mailchi.mp/b4216331e284/zen-of-programming)，里面有我本周最喜欢的链接和我的最新文章。还有，[发微博](https://twitter.com/aspittel)告诉我你最喜欢的设计技巧！我还有很多东西可以在本文中讨论，比如响应性、空白和对齐，所以如果你想看到更多这样的帖子，请告诉我！