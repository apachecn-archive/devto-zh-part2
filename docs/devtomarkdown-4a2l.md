# dev.to 上的文章写法和 Markdown 的解说

> 原文：<https://dev.to/programmingmonky/devtomarkdown-4a2l>

本文简要说明在 dev.to 上发表文章的方法和
文章的编辑风格 Markdown。

没有 dev.to 账户的人可以参考这篇文章

https://dev.to/programmingmonky/devto-25lm

请先创建 dev.to 帐户。

# 关于报道的撰写方法

要转移到报道的制作画面时，单击
画面上部菜单栏的“WRITE A POST”按钮。

[![記事の作成画面](img/99dbdaaac612333d9728f07714120330.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1f5h2W09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u8fvoaot6rzrpkgxaiwz.PNG)

## 文章投稿画面的解说

我想按下“WRITE A POST”按钮，就会显示文章的制作画面。

[![記事の投稿画面](img/991a8281c9161214c259a43a455aaba3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bnp8nK-8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xo7txuilgt1qn1w7zcy7.PNG)

解说这个画面的使用方法

### Upper UI part

[![上部 UI 部分](img/b0bf126861912a386f253a7bbeda07c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hJ62mz5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/57fpx0nzquori1p3n0jo.PNG)

*   1\. `[HELP]`按钮:显示[dev.to 的说明文章](https://dev.to/p/editor_guide)(英文)。
*   2\. `[MARKDOWN]`按钮:显示文章的编辑界面。
*   3\. `[PREVIEW]`按钮:显示文章的预览画面。
    *   可以在此画面中确认编辑后的 markdown 是否正确绘制。
*   4\. `[SAVEPOST]`按钮:临时保存文章的投稿内容。
*   5\. `[Upload Image]`按钮:用于上传图片。
    *   按按钮在资源管理器中选择要上传的图像。
        *   上传图像后，upload 图像的 URL 会出现在按钮的右侧。 ![アップロードされた画像へのリンク](img/1bb9449c388b153eb5ddf06490382112.png)
    *   在文章中嵌入图像的方法由后述的 markdown 解说进行。

### 下部的报道部分

[![記事部分の画像](img/8432403f70f1853e6381c9a7b710ff7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lz3OgH2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8vic4zkmewuovaikn10i.png)

*   1 文章的标题部分。
    *   在此处写入标题、标签等文章的元信息。
*   2 报道正文部分。 在这里用 markdown 写文章

#### 文章标题的解说

我认为文章标题作为初始值如下。

```
---
title: ""
published: false
description: 
tags: 
--- 
```

每一行都表示文章的信息。
说明各行的意思。

*   title :设定文章的标题
*   published :设定公开、非公开。
    *   设定`true`可以公开报道。
    *   设置`false`后，文章将变为非公开。
        *   初始值为`false`。
        *   即使是非公开的，也可以通过共享链接向熟人等限定公开。
*   description :写报道的概要。

    *   虽然不会出现在报道中，但是会被记载在链接到 twitter 和 slack 时的卡片上。
    *   这是贴在 Slack 上的例子。 红框的部分是在 description 上写的内容。

    [![Slackに張った例](img/98fda85d64fb7b4fd1614fce6ef7e658.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rV_I2sj_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lqf6lbuent4i8cqkbcx.png)

*   tags :用 csv 记载那篇报道的类型。 最多可以记载 4 个。

作为实例，本文的标题如下所示。

[![この記事のヘッダ](img/6368e24d5c94922ceca352b7e2a33900.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GUqkP91W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mymi6i5iw5j0v7bco0b7.PNG)

# 关于语法的 markdown

在 dev.to 上利用 markdown 制作投稿文章。
首先简单说明一下关于 markdown 的事情。

正确的定义请参考[维基百科](https://ja.wikipedia.org/wiki/Markdown)等。
向初学者简单说明一下

*   一种简单的文本格式，可以使用 pandoc 或 jekyll 等工具转换为 ihtml 或 pdf
    *   在各种各样的地方被使用，很流行。
        *   光是我知道的就有以下内容。
            *   GitHub 自述. MD (概述源代码的文档)
            *   读我的文本装饰
            *   Kotlin 文档( Kdoc )
            *   博客和 Qiita 的投稿
        *   也就是说，即使是第一次记住 markdown 的人，也很有可能在别的地方有机会使用 markdown。
            *   我想现在为了 dev.to 学习 markdown 以后会有帮助的。
    *   能够很好地表达和转换这样的嵌套项目符号。
    *   与手写 Excel 方格纸或 html 的情况相比，可以用较少的劳力进行编辑记述。
    *   与 html 等常用标记语言相比，呈现前的可读性非常好。

马上把 markdown 和 html 进行比较吧。 首先是马克·邓恩。

```
# 見出し1

先頭をシャープ記号の行は見出しの行になります。
htmlでいうところのh1タグです。シャープを重ねることで
h2,h3 相当のタグを作って入れ子にすることができます。

## 見出し2

リンクのサンプルです。[Googleのトップページです](https://www.google.co.jp/)

[]内の文字が画面に青く表示したいテキストを書きます。()の中にリンク先のURLを書きます。

表のサンプルです。htmlのように複雑なタグを利用する必要はありません。

| id |  type             |  usage content |  amount   | 
|----|-------------------|----------------|-----------| 
| 1  |  snack            |  banana        |  100 yen  | 
| 2  |  Writing utensils |  pencils       |  200 yen  | 
| 3  |  sporting goods   |  soccer ball   |  3000 yen | 
| 4  |  Gifts            |  game software |  4000 yen | 
| 5  |  Consumables      |  water         |  100 yen  | 

markdownの構文を覚えるのが面倒な方は、
[こちらのサイト](https://donatstudios.com/CsvToMarkdownTable)で
csvやtsvの形式から簡単にmarkdownの票に変換することができます。

tsvが使えるのでexcelからもコピペで簡単にこの形式に変換できますよ。

### 見出し3

- 箇条書きのサンプルです
    - 入れ子にすることができます。
    - 同じレベルで並べることもできます
- 特に入れ子にできる件数に制限はないと思います。
- 読みにくくなるので入れ子は3 段程度にとどめておくのがよいでしょう。

# 引用について

引用のサンプルです。

大なり記号>を使うことで引用になります。

>あなたのぶどう畑の実を取りつくしてはならない。
>またあなたのぶどう畑に落ちた実を拾ってはならない。
>貧しい者と寄留者とのために、これを残しておかなければならない。
>わたしはあなたがたの神、主である。
>レビ記 19:10

メールなどで返信した時の形式とよく似ています。 
```

即使在 plane text 的状态下也很容易阅读呢！
如果是 html 的话会变成这样。

```
<h1 id="-1">見出し1</h1>
<p>先頭をシャープ記号の行は見出しの行になります。
   htmlでいうところのh1タグです。シャープを重ねることで
   h2,h3 相当のタグを作って入れ子にすることができます。
</p>
<h2 id="-2">見出し2</h2>
<p>リンクのサンプルです。<a href="https://www.google.co.jp/">Googleのトップページです</a></p>
<p>[]内の文字が画面に青く表示したいテキストを書きます。()の中にリンク先のURLを書きます。</p>
<p>表のサンプルです。htmlのように複雑なタグを利用する必要はありません。</p>
<table>
   <thead>
      <tr>
         <th>id</th>
         <th>type</th>
         <th>usage content</th>
         <th>amount</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>1</td>
         <td>snack</td>
         <td>banana</td>
         <td>100 yen</td>
      </tr>
      <tr>
         <td>2</td>
         <td>Writing utensils</td>
         <td>pencils</td>
         <td>200 yen</td>
      </tr>
      <tr>
         <td>3</td>
         <td>sporting goods</td>
         <td>soccer ball</td>
         <td>3000 yen</td>
      </tr>
      <tr>
         <td>4</td>
         <td>Gifts</td>
         <td>game software</td>
         <td>4000 yen</td>
      </tr>
      <tr>
         <td>5</td>
         <td>Consumables</td>
         <td>water</td>
         <td>100 yen</td>
      </tr>
   </tbody>
</table>
<p>markdownの構文を覚えるのが面倒な方は、
   <a href="https://donatstudios.com/CsvToMarkdownTable">こちらのサイト</a>で
   csvやtsvの形式から簡単にmarkdownの票に変換することができます。
</p>
<p>tsvが使えるのでexcelからもコピペで簡単にこの形式に変換できますよ。</p>
<h3 id="-3">見出し3</h3>
<ul>
   <li>
      箇条書きのサンプルです
      <ul>
         <li>入れ子にすることができます。</li>
         <li>同じレベルで並べることもできます</li>
      </ul>
   </li>
   <li>特に入れ子にできる件数に制限はないと思います。</li>
   <li>読みにくくなるので入れ子は3 段程度にとどめておくのがよいでしょう。</li>
</ul>
<h1 id="-">引用について</h1>
<p>引用のサンプルです。</p>
<p>大なり記号&gt;を使うことで引用になります。</p>
<blockquote>
   <p>あなたのぶどう畑の実を取りつくしてはならない。
      またあなたのぶどう畑に落ちた実を拾ってはならない。
      貧しい者と寄留者とのために、これを残しておかなければならない。
      わたしはあなたがたの神、主である。
      レビ記 19:10
   </p>
</blockquote>
<p>メールなどで返信した時の形式とよく似ています。</p> 
```

笔者是 html 的人，不用浏览器渲染的话就不想读。
我更不喜欢手写。

※当然这个 html 也是从 markdown 生成的哦

我想光是这个比较就能理解 markdown 的简单性了。
因为偏离了本文的宗旨，所以不做介绍。
除了这里介绍的功能以外，现代编辑器中还有很多方便使用 markdown 的结构。

# 关于 Markdown 的方言和在 dev.to 中利用的形式

html 由 W3C 和 WHATWG 这样的团体努力标准化，但 Markdown 的标准化还没有进展。
[CommonMark](http://commonmark.org/) 和[IETF 的提案](https://tools.ietf.org/html/draft-ietf-appsawg-text-markdown-02)等，虽然也有类似的标准化活动，但
还处于不得不利用方言的阶段。 顺便说一下，方言的代表性有以下内容。

*   github 风味降价
*   额外降价
*   堆栈溢出
*   马鲁库
*   博客和 Qiita 独自扩展的东西

话虽如此，差别也只是一点点。
本文介绍的东西除了图片的粘贴以外，应该可以在任何 Markdown 上使用。

*   标题用`#`连缀
*   链接用`[画面表示テキスト](http://から始まるURL)`写
*   分条可以通过开设以`-`为开头的行来进行
*   引用他人的文章在以`>`开头的行中进行
*   图像的刊登如下

[![画像の書き方](img/cc30de233163ce43880cd35f94099baf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IWdBozlW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ib23dcircyw56nfznlec.PNG)

*   只有这个是 jekyll 的方言。 对不起。

*   代码块使用后退( ` )

    *   JIS 键盘用 Shift + @标记出现。
    *   在行中书写时，backtick 用一个字圈起来
    *   如果要分为其他块元素，backtick 用 3 个框起来

代码块的描述示例如下所示。

[![コードブロックのサンプル](img/4ad893bc08cdc9cafefeac41573a20ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hnW3_c-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yswu6ab3yn70h05fv1q2.PNG)

上述 markdown 的记述在文章中显示如下。

[![コードブロックの表示例](img/bdfc39eb5ca5d7c878cd0c193c76292d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wOuice-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1epkmq63wcswswjhmwed.PNG)

dev.to 使用名为 jekyll 的工具的 markdown 格式。
光是上述要素就足以制作 dev.to 的文章，如果想使用 jekyll 用的更发展的句法的话

*   杰基尔·马克登·syntaxやjekyll·马克登·cheetsheetなどで検索する
*   学习 jekyll 教程

请通过等加深理解。
至此。

如果大家能参考本投稿发表很棒的报道的话就太好了。

# 参考链接

*   [哲基尔公式](https://jekyllrb.com/)
*   [Jekyll Markdown 快速参考](https://gist.github.com/roachhd/779fa77e9b90fe945b0c)

# Prepare for the exam

*   刊登样品的 mrkdown→html 转换使用了以下工具。
    *   [借用了 md2pdf](http://md2pdf.higty.xyz/md2html) 从 markdown 转换为 html
    *   [借用 HTML Formatter](https://www.freeformatter.com/html-formatter.html#ad-output) 整理了 html 的 indent。
*   以防万一补充: dev.to 的投稿不需要使用上述内容的转换工作。