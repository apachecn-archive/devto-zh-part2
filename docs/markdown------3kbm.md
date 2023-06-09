# 在 Markdown 中生成面向公司内部的文档、大学的报告、书、幻灯片、所有的东西

> 原文：<https://dev.to/e_ntyo/markdown------3kbm>

## 前言

像我这样的学生或者工程师们，平时是怎么输出信息的呢？

我想根据所属的团队和组织的不同，输出所要求的形式也各不相同。 我的情况是这样的。

| 文档类型 | 要求的形式 |
| --- | --- |
| 大学报告 | 允许的格式，例如 pdf、docx、TeX 和 html |
| 内部文档 | 便于在 Backlog、Slack 和 Google Drive 上共享的格式 |
| Table data | 某种幻灯片形式 |
| 外部文档 | Medium，dev.to 的对应格式 |

看着我身边的人，一定数量的人说:“报告等文件使用的是文字软件和 TeX，演示文稿使用的是专用软件，博客和公司内外知识共享工具使用的是独自书写法。”

但是，每次使用新的服务或软件时，记住其书写和使用方法都很困难，TeX 和专用书写在别人编辑时也可能会变得麻烦

如果能把基本上所有的输出统一起来用 Markdown 记述的话，那就没有心情了。有这样动力的人请阅读以下内容。 写了以下内容。

*   在 Markdown 中生成 pdf/html/docx 格式文档、幻灯片的方法和推荐工具
*   在 Markdown 上进行物品书写的 tips 和便利工具

## 到底为什么是 Markdown 呢

*   有些渲染器会渲染 emoji (超重要)
*   GitHub、Qiita、各种博客服务等，可以应对写作和工程师使用的很多 Web 服务
*   ~~与 TeX 不同，~~对人温柔(可读性地) (甲本浩)，他人容易编辑
*   能写出相当于 html + css 的漂亮文件

### 降价到文書

本来有机会写 Markdown 的人，在认真写眼睛的 Markdown 时，会使用什么工具呢？ 因为现在很多编辑器都实现了面向 Markdown 的 linter、realtime preview 等功能，所以用手边的编辑器写的人可能很多。 即使是那些至今为止只在 GitHub 左右使用过 Markdown 的人，也请尝试一下[HackMD](https://hackmd.io/) 这个 Web 服务。

HackMD 是一种知识积累和共享服务，具备强大的 Markdown 编辑器，支持各种公式代码亮点 UML 等。 写的 Markdown 文件可以像 Google Drive 一样指定浏览/编辑权限进行保存。 能够写公式、源代码、UML 这件事，报告和公司内部文档看起来就足够了！

另外，可以将写好的 Markdown 转换为 HTML/ODF 下载。 要说转换为 html 后有什么高兴的，可以使用 CSS 对布局等进行微调。 这样一来，对报告体裁非常挑剔的教授也可以闭嘴了。

不，还不行。 部分教授患有不用 Word 或 pdf 阅读器就看不懂文档的病，需要转换为`.pdf``.docx`。 你这个混蛋。 但是没关系，使用[pandoc](https://pandoc.org/demos.html) 的话，一个指令就可以把 Markdown 变换为`pdf``.docx`。

pandoc 的具体使用方法的说明将留给其他报道(看链接地的 demo 页面的话几乎都可以。 )虽然也有几个 Web 服务利用 pandoc 在线做`.md`= >`.hogehoge`，但是由于无法设定选项，所以对于“想不杀公式”“想指定 pdf 引擎”等要求 自己最终也在手边构筑了 pandoc 环境。

现在，您已经了解了如何将 Markdown 转换为自己喜欢的外观和各种文档格式。

### 降价到スライド

以上是常见的报道，但这里含有一些有益的信息哦！
下面介绍两种可以在 Markdown 上生成所谓发表用幻灯片的服务。

#### 1。[滑船](https://slideship.com/)

SlideShip 是一种在线编写 Markdown 以创建和发布幻灯片的 Web 服务。
因为试着用了几次发表，所以写下感想后，

##### 好的地方

*   可以快速制作 LT 等简单的发表资料
*   准备了丰富的彩色主题
*   可将幻灯片转换为 pdf 并下载

##### 不好的地方

*   功能微妙地不足
*   无法进行风格的细微调整
*   无法渲染 emoji

#### 2。HackMD

因为 HackMD 是上帝，所以用自己的书写方式(虽说不是很夸张，而是非常简单的东西)写 Markdown 的话，竟然会！ 会出现 HTML 的幻灯片。

[这就是](https://hackmd.io/IwTgTADApiIBwFpgDYAmBjBAWMyIJACNUAzBKAdgFYwSsBDMAZgvUKA=)就是[这样的感觉](https://hackmd.io/p/H1fOE0z_z#/0/1)。

##### 好的地方

*   如上所述，平时用 HackMD 渲染的东西直接变成幻灯片(上帝！ ！ ！ ）
*   因为在`reveal.js`中会生成 HTML(CSS，JS )，所以想讲究的话可以自己弄调整
    *   因为素颜做得比幻灯片好，所以感觉不用欺负

##### 不好的地方

*   还是 beta，各种各样的举动都很可疑

### 降价到本

因为 HackMD 真的是上帝，所以和幻灯片一样的话书也会吐出来。 虽然自己还没有实践，但是[好像会变成这样的](https://hackmd.io/book-example)。 太棒了…

## 总结

因为已经是公元 2018 年了，基本上只要用 Markdown 写下平时的笔记，输出基本上就能解决。 HackMD 是上帝。 谢谢你。

## 有 Markdown (想快点说)

> 表很痛苦

[有在线的发生器](https://www.tablesgenerator.com/markdown_tables#)。

> HackMD 虽然能写 UML，但不会画其他的图，还是 PowePoint 好

~~向微软出卖灵魂吗？ 闭嘴。~~ 在线制作各种图的话，[draw.io](https://www.draw.io) 很方便！