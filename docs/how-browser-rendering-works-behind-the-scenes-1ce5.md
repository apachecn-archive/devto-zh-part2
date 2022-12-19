# 浏览器渲染的工作原理—幕后

> 原文：<https://dev.to/bnevilleoneill/how-browser-rendering-works-behind-the-scenes-1ce5>

### 浏览器渲染如何工作——幕后

[![](../Images/b8d87c563b9cac909646de55efb07112.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lbir5Xqh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVmD21Exnic6eQxj5xGrA-Q.png)

这篇文章的目的是用非常简单的术语解释你的浏览器将 HTML、CSS 和 JavaScript 转换成你可以与之交互的工作网站的步骤。

了解您的浏览器将网站变得生动的过程将使您能够优化您的 web 应用程序以获得更快的速度和性能。

如果你准备好了，我们开始吧。

### 简介

浏览器到底是怎么渲染网站的？

我将很快解构这个过程，但是首先，重要的是回顾一些基础知识。

一个**网络浏览器**是一个软件，它从一个远程服务器(或者一个本地磁盘)加载文件并显示给你——允许用户交互。

我知道你知道什么是浏览器:)

然而，在浏览器中有一个叫做浏览器引擎的软件。

在不同的浏览器中，有一部分浏览器会根据接收到的文件来判断向您显示什么。这被称为**浏览器引擎。**

浏览器引擎是每一个主流浏览器的核心软件组件，不同的浏览器厂商对其引擎的称呼也不尽相同。

火狐的浏览器引擎叫 Gecko，Chrome 的叫 Blink，恰好是 Webkit 的一个分支。不要让这些名字迷惑了你。它们只是名字。没什么严重的。

为了便于说明，请假设我们已经有了一个通用的浏览器引擎，这个浏览器引擎将以图形方式表示，如下所示。

[![](../Images/143ce98bee9e8e9f97c6c2783969e3a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KO-9VVf5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AkUR7XRBCjzTTMgAfsanTSQ.png) 

<figcaption>插图由我:</figcaption>

如果你感兴趣的话，你可以看看各种浏览器引擎的对比。

在本文中，我交替使用“浏览器”和“浏览器引擎”。不要让那迷惑你。重要的是，您知道浏览器引擎是负责我们正在讨论的内容的关键软件。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 发送&接收信息

这不应该是一门计算机科学网络课，但是你可能记得数据是以字节大小的“包”的形式通过互联网发送的。

[![](../Images/a1547733a9d948acda3467968b653e20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nw7lBiXg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ArTbp2SJxvoQyKSIA6TGHEw.png) 

<figcaption>电脑说:给我发送字节的数据！(由我插画)</figcaption>

我想说明的一点是，当你编写一些 HTML、CSS 和 JS，并试图在浏览器中打开 HTML 文件时，浏览器会从你的硬盘(或网络)中读取 HTML 的原始字节。

[![](../Images/74dce88f60727d6bcb67110862fecef2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g91gy7_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AGSw1oqEpbPo0NmwG_73bPw.png) 

<figcaption>电脑接收字节数据(由我插画)</figcaption>

明白了吗？浏览器读取数据的原始字节，而不是您编写的代码的实际字符。

我们继续吧。

浏览器接收数据字节，但它不能真正做任何事情。

数据的原始字节必须转换成它能理解的形式。

这是第一步。

[![](../Images/7fb6044ee009db7f62c1b771958ea61b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CPYIXBSH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AIb2Ufggiy67xg02Jp8CYhQ.png) 

<figcaption>尽量不要忘记这一点:)(由我插画)</figcaption>

### 从 HTML 的原始字节到 DOM

浏览器对象需要处理的是文档对象模型(DOM)对象。

那么，DOM 对象是如何派生出来的呢？

嗯，很简单。

首先，数据的原始字节被转换成字符。

[![](../Images/c5f9e0368329c1c0074b374f2ae96016.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_KD5BvzJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AYdURVl_Qkxv9Lf4Ja583-w.png) 

<figcaption>耶。从字节到字符！</figcaption>

您可能会从您编写的代码字符中看到这一点。这种转换是基于 html 文件的字符编码完成的。

此时，浏览器已经从原始数据字节变成了文件中的实际字符。

角色很棒，但不是最终的结果。

这些字符被进一步解析成称为标记的东西。

[![](../Images/72c13bde7a6632558f3759a8ad2b6eed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1E-6Ar1t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A23wqjUorWI2fkCJ_AN5a2w.png) 

<figcaption>从人物到令牌！</figcaption>

这些代币是什么？

文本文件中的一堆字符对浏览器引擎没什么好处。

如果没有这个标记化过程，这一串字符只会产生一串无意义的文本，即 html 代码，而不会产生一个实际的网站。

当您用。html 扩展名，您向浏览器引擎发出信号，将该文件解释为 html 文档。浏览器“解释”该文件的方式是首先解析它。

在解析过程中，尤其是在标记化过程中，文件中的每个开始和结束 html 标记都被考虑在内。

解析器理解尖括号中的每个字符串，例如""，"

"，并了解适用于它们的一组规则。例如，表示锚标记的标记与表示段落标记的标记具有不同的属性。

从概念上讲，您可能会将令牌视为某种数据结构，其中包含有关某个 html 标记的信息。本质上，一个 html 文件被分解成称为**标记**的小解析单元。这是浏览器开始理解你写的东西的方式。

[![](../Images/a0a8e992acd1f8d8fa21736a57cbd113.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0pWMr1jA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AvIwWeznQhir5EPWDOyo5sw.png) 

<figcaption>一个令牌的概念性插图(插图由我)</figcaption>

代币很棒，但它们也不是我们的最终结果。

完成标记化后，标记将被转换为节点。

您可能认为节点是具有特定属性的不同对象。事实上，解释这一点的更好方法是将节点视为文档对象树中的一个独立实体。

节点很棒，但它们仍然不是最终结果。

现在，这是最后一点。

创建这些节点后，这些节点被链接到一个称为 DOM 的树数据结构中。

DOM 建立了父子关系、相邻兄弟关系等。

每个节点之间的关系都是在这个 DOM 对象中建立的。

现在，这是我们可以做的事情。

[![](../Images/cddbbef78db2ec1b2b756c69fa4fee7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WddyZo0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AugZgXZkxbzeIia7Z3jP76A.png) 

<figcaption>一个用图形表示 DOM 的例子(我来举例)</figcaption>

如果你还记得《网页设计 101》，你不会在浏览器中打开 CSS 或 JS 文件来浏览网页。不，你打开 HTML 文件，大多数时候是以 index.html 的形式

这正是你这样做的原因:在任何事情发生之前，浏览器必须将 html 数据的原始字节转换成 DOM。

[![](../Images/d662c4dc2911e708f5de61580a1fa5f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q7LoXcLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ALlo-v3XG_gEP_Xlgy0qtjg.png) 

<figcaption>HTML 先进去——永远！(由我插画)</figcaption>

根据 html 文件的大小，DOM 构造过程可能需要一些时间。不管文件有多小，不管文件有多大，都要花一些时间(不管多短)。

[![](../Images/24e4c085a98ec6671cbc4850637c1825.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8QyxQ4bu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AROuUBS5eZ1DKk2RnFDfh6Q.png) 

<figcaption>耶。从令牌到节点和 DOM！</figcaption>

### 但等。CSS 呢？

DOM 已创建。太好了。

一个典型的带有 CSS 的 html 文件会有如下所示的样式表链接:

```
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" media="screen" href="main.css" />
</head>
<body>

</body>
</html> 
```

当浏览器接收数据的原始字节并开始 DOM 构造过程时，它还会请求获取链接的 main.css 样式表。

一旦浏览器开始解析 html，在找到 css 文件的链接标签时，它同时发出获取该标签的请求。

正如您可能已经猜到的，浏览器也接收 CSS 数据的原始字节，无论是来自互联网还是您的本地磁盘。

但是这些 CSS 数据的原始字节到底是用来做什么的呢？

### 从 CSS 的原始字节到 CSSOM

你看，当浏览器接收到 CSS 的原始字节时，类似的处理 HTML 的原始字节的过程也被启动。

我的意思是，数据的原始字节被转换成字符，然后被标记化，节点也被形成，最后，树结构也被形成。

什么是树形结构？

大多数人都知道有个东西叫 DOM。同样，还有一种 CSS 树结构，称为 CSS 对象模型，简称为 CSSOM。

你看，浏览器不能处理 HTML 或 CSS 的原始字节。这必须被转换成它能识别的形式——而这恰好就是这些树形结构。

[![](../Images/7467e82857336a82ebaed5196635cc8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iAtHCGDn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A5GYEa442MdwmhPGJbGagGw.png) 

<figcaption>同样处理 CSS 字节！</figcaption>

CSS 有一种叫做[级联](https://dev.to/bnevilleoneill/how-css-works-understanding-the-cascade-3bag)的东西。层叠是浏览器决定将什么样式应用于元素的方式。

由于影响一个元素的样式可能来自一个父元素，例如通过继承，或者已经在元素本身上设置，CSSOM 树结构变得很重要。

为什么？

这是因为浏览器必须递归地遍历 CSS 树结构，并确定影响特定元素的样式。

一切都好。

浏览器有 DOM 和 CSSOM 对象。我们现在能把一些东西渲染到屏幕上吗？

### 渲染树

我们现在得到的是两个**独立的**树形结构，它们似乎没有共同的目标。

[![](../Images/c362f59877aabe75531eb36b20e3fee2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IeQMs3hk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AuqHMI5h_gcrycAXgwnq8hw.png)

<figcaption>DOM 和 CSSOM 是独立的树形结构(我来举例)</figcaption>

DOM 和 CSSOM 树结构是两个独立的结构。

DOM 包含关于页面 HTML 元素关系的所有信息，而 CSSOM 包含关于元素样式的信息。

好了，浏览器现在将 DOM 和 CSSOM 树组合成一个叫做**渲染树**的东西。

[![](../Images/1170be03116513f8ade1235c7142feae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wYwH6uKq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A10ytkQcfKdbfGQxvYj2-5A.png) 

<figcaption>DOM + CSSOM =渲染树(由我插画)</figcaption>

呈现树包含页面上所有可见 DOM 内容的信息，以及不同节点所需的所有 CSSOM 信息。

注意，如果一个元素被 CSS 隐藏，比如使用 display 无，该节点将不会显示在渲染树中。

隐藏元素将出现在 DOM 中，但不会出现在呈现树中。

原因是渲染树结合了来自 DOM 和 CSSOM 的信息，所以它知道不要在树中包含隐藏元素。

渲染树构建完成后，浏览器进入下一步，**布局**！

#### 摆开。

渲染树构建完成后，下一步是执行“布局”。

现在，我们已经获得了屏幕上所有可见内容的内容和样式信息——但是我们实际上还没有向屏幕呈现任何内容。

首先，浏览器必须计算页面上每个对象的确切大小和位置。

这就像将页面上要呈现的所有元素的内容和样式信息传递给一个天才的数学家。这位数学家然后用浏览器视窗计算出每个元素的确切位置和大小。

[![](../Images/19d14bfa77682775276f4024ada441bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6M-XaBI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AMMJ5DCdRhZMdy8K7CFAsZw.png) 

<figcaption>啊，耶！布局进行中！(由我插画)</figcaption>

很神奇，对吧？

这个布局步骤考虑了从 DOM 和 CSSOM 接收的内容和样式，并进行所有必要的布局计算。

你有时会听到这个“布局”阶段，也称为“回流”。

### 让艺术家出来

现在计算出了每个元素的确切位置，剩下的就是将元素“画”到屏幕上。

想想吧。我们已经获得了在屏幕上实际显示元素所需的所有信息。让我们把它展示给用户，好吗？

是啊！这正是这个舞台的意义所在。

根据计算出的内容(DOM)、样式(CSSOM)和元素的确切布局信息，浏览器现在将单个节点“绘制”到屏幕上。

最后，元素现在被渲染到屏幕上！

### 渲染-阻塞资源

当你听到“渲染阻塞”时，你会想到什么？

嗯，我的猜测是，“阻止在屏幕上实际绘制节点的东西”。

如果你那样说，你绝对是对的！

优化网站的第一条规则是尽快将最重要的 HTML 和 CSS 交付给客户。

DOM 和 CSSOM 必须在成功绘制之前构造，所以 HTML 和 CSS 都是渲染阻塞资源。

关键是，你应该尽可能快地把你的 html 和 css 送到客户端，以优化你的应用程序第一次呈现的时间。

### 但等。JavaScript 呢？

一个像样的 web 应用程序肯定会用到一些 JavaScript。那是必然的。

JavaScript 的“问题”是您可以使用 JavaScript 修改页面的内容和样式。记得吗？

这意味着，您可以从 DOM 树中删除和添加元素，也可以通过 JavaScript 修改元素的 CSSOM 属性。

这太棒了！

然而，这是有代价的。

考虑下面的 HTML 文档:

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>
</body>

</html> 
```

这是一份非常简单的文件。

style.css 样式表有一个如下所示的声明:

```
body {
  background: #8cacea;
} 
```

这样的结果是:

[![](../Images/1359210c86a8270516830b07bf1a03fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ty_GPoQH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Adwe_vIFxXA0zzMHwlvS47w.png) 

<figcaption>基本呈现 html 页面。</figcaption>

一个简单的文本和图像呈现在屏幕上。

根据前面的解释，浏览器从磁盘(或网络)读取 html 文件的原始字节，并将其转换为字符。字符被进一步解析成标记。

一旦解析器到达带有的行，就会请求获取 CSS 文件 style.css

DOM 构造继续进行，一旦 CSS 文件返回一些内容，CSSOM 构造就开始了。

一旦我们引入 Javascript，这个流程会发生什么变化？

嗯，需要记住的最重要的一点是，每当浏览器遇到脚本标记时，DOM 构造就会暂停！

整个 DOM 构造过程会暂停，直到脚本执行完毕。

[![](../Images/4ad11e385025f56107ca5b0d9d7741a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vkkXj2j7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A7Zku0ogP6C2ua43E4Y5ImA.png) 

<figcaption>别忘了这一点。(由我插画)</figcaption>

这是因为 JavaScript 可以改变 DOM 和 CSSOM。因为浏览器不确定这个特定的 Javascript 会做什么，所以它采取了预防措施，停止了整个 DOM 的构建。

这能有多糟？

让我们看一看。

在我之前分享的基础 html 文档中，我们介绍一个带有一些基础 JavaScript 的脚本标签:

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
</head>

<body>

    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>

    <script>
        let header = document.getElementById("header");

        console.log("header is: ", header);
    </script>
</body>

</html> 
```

在 script 标记中，我正在访问 id 为、header 为的节点的 DOM，然后将它记录到控制台。

这很好，如下所示:

[![](../Images/e42b20edf329272973db9d320d8ae9ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A9i-8fzb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ad4uD0EVuVSgdML7_07tNbw.png)T3】DOM 操作成功

但是，您注意到这个脚本标签被放在 body 标签的底部了吗？

让我们把它放在头部，看看会发生什么:

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
    <script>
        let header = document.getElementById("header");

        console.log("header is: ", header);
    </script>
</head>

<body>

    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>
</body>

</html> 
```

一旦我这样做了，头变量就被解析为 null。

[![](../Images/208affd4c0e3a1cef8d416cf6c01bb68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1OTJORD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A9h-RJFkX76TT4JuyRxTyiA.png)T3】DOM 操作失败:(

为什么？

很简单。

当 HTML 解析器在构造 DOM 的过程中，发现了一个脚本标记。此时，body 标记及其所有内容还没有被解析。

DOM 构造会暂停，直到脚本执行完毕:

[![](../Images/fa10310fff4e1ee5b1f43911f1b551db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HbUI09NQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/687/1%2AMBY_mv3GKMgv4Rb2GjhiKg.png)

<figcaption>DOM 建设暂停</figcaption>

当脚本试图访问 id 为 header 的 DOM 节点时，它已经不存在了，因为 DOM 还没有完成对文档的解析！

这就引出了另一个重要的问题。

脚本的位置很重要。

[![](../Images/251f998f524d9ee394a154c8d477e207.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EY_6V0UV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AmhK63PiEos6yZtRN5kwSBQ.png) 

<figcaption>记住这个！(由我插画)</figcaption>

这还不是全部。

如果将内联脚本提取到外部本地文件 app.js，行为是一样的。DOM 构建仍处于暂停状态:

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
    <script src="app.js"></script>
</head>

<body>

    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>
</body>

</html> 
```

这还不是全部。

如果这个 app.js 不是本地的，而是必须通过互联网获取，会怎么样？

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
    <script src="https://some-link-to-app.js"></script>
</head>

<body>

    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>
</body>

</html> 
```

如果网络很慢，并且获取 app.js 需要几千毫秒的时间，那么 DOM 构造也会停止几千毫秒！！！

这是一个很大的性能问题，而且还不止这些。

请记住，JavaScript 也可以访问 CSSOM 并对其进行更改。例如，这是有效的 JavaScript:

```
document.getElementsByTagName("body")[0].style.backgroundColor = "red"; 
```

那么，当解析器遇到一个脚本标签但是 CSSOM 还没有准备好的时候会发生什么呢？

答案很简单。

Javascript 执行将会暂停，直到 CSSOM 准备就绪。

[![](../Images/6cb0580c38dd3d8cd90d698364e61af5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hiynrvwD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AUeR5LCcuy5OtswIzjnF1iQ.png) 

<figcaption>好记吗？不完全是。让它沉下去！(由我插画)</figcaption>

因此，即使 DOM 构造在遇到脚本标记之前停止，CSSOM 也不会发生这种情况。

对于 CSSOM，JS 执行等待。没有 CSSOM，就没有 JS 执行。

### 异步属性

默认情况下，每个脚本都是解析器拦截器！DOM 构造将总是停止。

不过，有一种方法可以改变这种默认行为。

如果将 async 关键字添加到 script 标记中，DOM 构造将不会停止。DOM 构造将继续进行，当 DOM 准备好时将执行脚本。

这里有一个例子:

```
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    Medium Article Demo
    <link rel="stylesheet" href="style.css">
    <script src="https://some-link-to-app.js" async></script>
</head>

<body>

    <p id="header">How Browser Rendering Works</p>
    <div><img src="https://i.imgur.com/jDq3k3r.jpg"></div>
</body>

</html> 
```

### 关键渲染路径

在此之前，我们已经讨论了接收 HTML、CSS 和 JS 字节并将其转换为屏幕上的渲染像素的步骤。

这整个过程被称为**关键渲染路径**。

优化网站性能就是优化关键的渲染路径。

一个优化良好的站点应该进行渐进式渲染，而不是阻塞整个过程。

[![](../Images/80cd79a7e7c56c2df10d7219e69139df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WpnzQ8d4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ALLox1r59dn47IslTmGuz5g.png)

这就是一个 web 应用程序感知速度快慢的区别。

一个经过深思熟虑的关键呈现路径(CRP)优化策略使浏览器能够通过区分加载资源的优先级和加载顺序来尽可能快地加载页面。

### 结论

了解了浏览器如何呈现 HTML、CSS 和 JS 的基础知识后，我恳请您花时间探索如何利用这些知识来优化页面速度。

一个好的起点是 Google Web 基础文档的[性能部分](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *