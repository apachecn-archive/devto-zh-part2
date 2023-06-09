# 用于 JavaScript 演示和原型的 ObservableHQ 笔记本

> 原文：<https://dev.to/hugo__df/observablehq-notebooks-for-javascript-demos-and-prototypes-3558>

ObservableHQ 是一个由[迈克·博斯托克](https://twitter.com/mbostock)(D3 可视化库的创建者)[杰里米·阿什肯纳斯](https://twitter.com/jashkenas)(从他的 [Twitter 简历](https://twitter.com/jashkenas)中“制作 CoffeeScript、Backbone.js、下划线和其他杂物】 )和[汤姆·麦克赖特](https://twitter.com/tmcw)(大演示框架、simple-statistics 和 documentation.js 的创建者，以及其他方面的 D3 贡献者)正在构建的平台。

Observable 不仅仅是另一个 JavaScript 沙箱。它将笔记本范式引入 JavaScript 项目，熟悉 Jupyter 笔记本的人，这相当于用 JavaScript 代替 Python。事实证明 JavaScript 非常适合这种范式。

此外，JavaScript 开发人员已经对反应性有了一定的了解，因为大多数前端框架和视图库都建立在它的基础上。这是一种不同的反应，因为该库只重新渲染或重新计算应用程序中需要它的部分(参见 Vue、Angular、React)。

你可以在媒体上阅读 Bostock 的原始帖子，详细介绍现在的 ObservableHQ(当时称为`d3.express`)、[“一种更好的编码方式”。在那篇文章中，解释了数据可视化的原因和方式。描述了一种“更好的方式”，即“*集成发现环境”*，或者更简单地称为“笔记本”-s](https://medium.com/@mbostock/a-better-way-to-code-2b1d2876a3a0)

那篇启示录是一篇 20 分钟的阅读，但是这里是**TL；博士:**

1.  视觉化通常是一次性的——“*视觉化的目的是洞察力，而不是图片。”*
2.  反应对于构建可视化和从数据中发现洞察力非常有用
3.  视觉输出非常有用，尤其是在探索阶段
4.  一个设计良好的笔记本系统有一个好处，那就是不需要太多的努力就可以让代码块重用(或者重新打包成一个库)
5.  任何人都可以从任何地方访问基于网络的笔记本电脑(如果这是想要的行为)

我特别喜欢第 4 点和第 5 点，因为它们不是一般笔记本的卖点，而是 ObservableHQ 对这个问题的看法。您最终得到的是一个设计良好的基于网络的反应式笔记本电脑环境，适合进行原型开发。

*   特征
    *   细胞和反应性
    *   以任意方式显示任意数据
    *   以任意方式获取数据输入
    *   感觉就像在使用视图库
    *   其他笔记本/模块的导入
*   示例项目
    *   运行简单的表情符号搜索
    *   编写直接的 SVG 可视化
    *   用 web APIs 做有趣的事情
    *   纯 JavaScript 演示
    *   CSS 演示
*   令人兴奋的路线图

## 特性

### 细胞与反应性

ObservableHQ 的评估模型基于相互反应的细胞(你可以在这里阅读更多关于[笔记本和细胞的信息](https://beta.observablehq.com/@mbostock/introduction-to-notebooks)

> 细胞可以是任何东西。例如，一个单元格可能包含几段文本，如下所示。或者一个单元格可能包含图像、视频、图表或其他图形和交互元素。单元格还可以包含数据结构，如数字、字符串、数组和对象。功能也是。

以下是博斯托克对反应性的看法(点击查看[全文):](https://beta.observablehq.com/@mbostock/introduction-to-code)

> 引用其他单元格的单元格在被引用的值发生变化时会自动重新计算。

所以发生了两件事:

1.  你可以把任何东西塞进细胞里
2.  你的细胞有一个明显的依赖图，根据它它们将重新评估

这意味着你可以确定，例如，如果你做了一个`fetch`，它将只运行一次，除非你手动让它重新评估，或者`fetch`依赖于变化。这与你破解 JavaScript 时的情况形成了鲜明的对比，每次运行代码都会导致一次`fetch`。

这种反应意味着我们不必使用宝贵的大脑容量来跟踪程序的一部分处于什么状态。状态就在那里，在其他单元格中为我们显示出来。因此我们得到了更快的试错(更正式的“迭代”)周期。

我们也不需要担心`pending`与`resolved`的承诺，可观察值与排放值，或者 ObservableHQ 所说的生成器。ObservableHQ 以同样的方式处理所有这些:它给你数据，而不是底层的抽象，不用做`.then(console.log)`作为健全检查是多么令人耳目一新。

### 以任意方式显示任意数据

说到`console.log`，输出和观察数据并不涉及`console.log`或`debugger`，由于编程模型，跟踪数据和变化是简单明了的。

呈现数据和文本就像使用带有特殊模板文字标签(即`html`、`md`)的 JavaScript 字符串插值一样简单。这意味着你可以基于你的数据写内联 SVG，就像你写 JSX 一样(当然，有一点语法上的干扰):

```
html`
  ${
    someData.map(
      (el, i) => `<text x=0 y={i * 20}>${el}</text>`
    )
  }
` 
```

### 以任意方式获取输入的数据

**用户输入**

```
viewof value = html`<input type="text" value="default-value">` 
```

**网络资源**

```
fetch('my-url').then(res => res.json()) 
```

**文件上传**

```
viewof file = html`<input type="file">`
// use the file as audio or something
fileDataUri = URL.createObjectURL(file) 
```

(参见后面的音频波形示例)

### 感觉就像在用视图库工作

由于反应性和它处理数据的方式，使用 ObservableHQ 感觉很棒，很像 React/Vue 的热重装，除了你没有开发环境中断

### 其他笔记本/模块的导入

我没有用过这么多(可能是因为我没有建立任何有趣的可视化)，据我所知，你可以将笔记本的一部分导入另一部分。

## 样本项目

### 运行简单的表情符号搜索

[![](img/2be445291a5fe77f67aa9b393a8401bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBPYwxR6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3006710316AC061FF0A027D62F4C495878AF5093095B2AF2C7634CDBA1D340A0_1538598277834_image.png)

[https://beta . observablehq . com/@ Hugo df/search-and-download-e moji-from-github](https://beta.observablehq.com/@hugodf/search-and-download-emoji-from-github)

### 写直 SVG 可视化

[![](img/3dfbe96d4d2328228d9129d24b056642.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2T6Ckwoa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3006710316AC061FF0A027D62F4C495878AF5093095B2AF2C7634CDBA1D340A0_1538598311941_image.png)

[https://beta . observablehq . com/@ Hugo df/Reddit-contributions-per-week-graph](https://beta.observablehq.com/@hugodf/reddit-contributions-per-week-graph)

### 用 web APIs 做有趣的事情

[![](img/08124a39163eb3a1b25a4da9f57efefd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UkZdytJ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3006710316AC061FF0A027D62F4C495878AF5093095B2AF2C7634CDBA1D340A0_1538598350347_image.png)

[https://beta.observablehq.com/@hugodf/audio-waveforms](https://beta.observablehq.com/@hugodf/audio-waveforms)

### 纯 JavaScript 演示

[https://beta . observablehq . com/@ Hugo df/recursion-to-wrap-http-requests](https://beta.observablehq.com/@hugodf/recursion-to-wrap-http-requests)[https://beta . observablehq . com/@ Hugo df/count-something-in-something-else](https://beta.observablehq.com/@hugodf/count-something-in-something-else)

### CSS 演示

[https://beta . observablehq . com/@ Hugo df/bonus-daily-situations-you-can-solve-without-javascrip](https://beta.observablehq.com/@hugodf/bonus-everyday-situations-you-can-solve-without-javascrip)

## 令人兴奋的路线图

还有更多功能会让 ObservableHQ 变得更好，比如笔记本嵌入。

你可以在 [ObservableHQ 论坛](https://talk.observablehq.com/)和[变更日志](https://beta.observablehq.com/collection/@observablehq/changelog)中找到更多信息。

[unsplash-logo
车头时距](https://unsplash.com/@headwayio?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)