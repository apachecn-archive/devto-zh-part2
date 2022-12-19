# 使用 CSS 网格来混合和匹配设计模式

> 原文：<https://dev.to/brob/use-css-grid-to-mix-and-match-design-patterns-j02>

在之前的教程中，我描述了[如何用 CSS 网格](https://bryanlrobinson.com/blog/2017/07/26/howto-css-grid-layout-to-make-a-simple-fluid-card-grid/)创建一个简单的流体卡网格。在本教程中，我们将更进一步，以有趣的方式创造促销空间。

使用网格，我们将获得以下优势:

*   语义、非嵌套的 HTML
*   作为实际可访问内容的图像，而不是背景图像
*   具有简单断点更改的移动优先设计
*   与所用设备的优势相匹配的不同设计模式

为此，我们将使用:

*   格式良好的 HTML
*   网格易于重叠
*   命名网格线和命名网格区域

## 设计

[![Design on multiple screens](../Images/296c4215c96cfc507a3dc5cac4c13add.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aVNgsjo4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.com/images/responsive-grid-break-design.jpg)

我们将为我们的推广空间设置三种不同的设计。

在手机上，我们会有一个背景为深色覆盖的图像，前景为文本和按钮。

在更大的屏幕上——平板电脑和横向移动电话——我们将使用相同的宣传片设计，但将它们分成两个部分。

在比平板电脑更大的设备上，我们将为每个宣传片安排一整行，图片在文本旁边，没有覆盖。

## 标记

```
<section class="promos">
    <div class="promo">
        <h3 class="promo__headline">This is a headline for a promo space</h3>
        <img class="promo__image" src="https://images.unsplash.com/photo-1521379770009-7bafd3ec7a23?ixlib=rb-0.3.5&q=85&fm=jpg&crop=entropy&cs=srgb&ixid=eyJhcHBfaWQiOjE0NTg5fQ&s=ce4848266618366ad30d7d35764c0bf4" alt="Ocean Scape">
        <p class="promo__text">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam ac arcu mollis, semper est ac, tincidunt purus. Cras efficitur maximus augue vitae volutpat. Nullam consectetur diam a dictum facilisis. </p>
        <a href="#" class="promo__button">Learn More &raquo;</a>
    </div>
    <div class="promo">
    ... etc
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

甜蜜而简单。促销有一个父容器，每个促销有一个子容器。在里面，我们有我们的内容。一个`h2/3/4`、`p`、`a`和一个`img`。没错，我们使用一个`<img>`标签作为背景图片。

如果你认为你的图片是你内容的一部分，而不是为了营造氛围，那么使用一个`<img>`标签是合适的，有语义的方式来写它。我们将在下一节看到如何让它看起来像一个背景图像。

## 精心制作我们的初始网格

```
.promo {
    display: grid;
    grid-template-rows: 10vh repeat(3, auto) 10vh;
    grid-template-columns: 1fr;
    grid-template-rows: 10vh auto auto auto 10vh;
    grid-template-areas: '....'
                         'headline'
                         'text'
                         'button'
                         '....';
}
.promo__headline {
    grid-area: headline;
    align-self: end;
}
.promo__text {
    grid-area: text;
} 
.promo__button {
    grid-area: button;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是力量开始的地方。我们将用一列五行来定义网格。

五行允许我们垂直居中我们的内容。第一行和最后一行将是空白的，但具有相同的高度设置。中间三行中的每一行都根据其内容使用关键字`auto`来确定大小。

最后，我们通过在`grid-template-areas`中指定一个网格区域名称来告诉浏览器在哪里放置我们的内容。然后，我们将`headline`、`text`和`button`关键词分配给匹配的内容类别。

在该声明中，每行由一个字符串表示，各列由空格分隔。在这种情况下，只有一列和五行。第一行和最后一行由一个或多个句点表示。这允许这些网格单元是空白的。

一个小提示:通过给标题分配`align-self: end`,我们在标题和文本之间建立了更紧密的联系。

## 将图像转换成背景

在这一点上，你可能已经注意到我们还没有设计我们的`<img>`。

为了使图像跨越容器的整个高度和宽度，我们需要告诉它填充所有的行。我们可以使用`grid-row: 1 / 6`，但是我们需要在不同的断点重新定义它。

网格的当前规范没有考虑到网格模板区域的重叠。因此，我们将通过重新定义网格模板的列和行来创建“命名行”。

```
.promo {
      grid-template-columns: [image-start] 1fr [image-end];
      grid-template-rows: [image-start] 10vh auto auto auto 10vh [image-end];
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了三件大事:

1.  方括号`[image-start]`内的字符串创建一个命名行。
2.  包含`-start`和`-end`的两条命名线将创建一个命名区域。
3.  行中的两条命名线和列中的两条命名线创建一个二维网格区域。

通过定义这四行，我们现在可以在我们的图像上调用`grid-area: image`，它将填充整个宣传片。

```
.promo__image {
        grid-area: image;
        align-self: stretch;
        object-fit: cover;
} 
```

Enter fullscreen mode Exit fullscreen mode

默认值`normal`与`stretch`非常相似，但不会影响图像等具有固有纵横比的事物。

您将注意到另外两个属性:`align-self`和`object-fit`。

Align-self 将允许图像填充整个网格单元。通过允许图像拉伸，它填充了空间。Object-fit 和关键字`cover`允许一个`<img>`标签具有与`background-size: cover`相同的功能。

## 添加混合混合模式叠加

[![The mobile design](../Images/4f6b2fca89ac296bb58b840fdbc90b72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FAMacjiK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.com/images/mobile-responsive-grid.jpg)

通常，[我会在 CSS 中提倡一个::after 元素来处理变暗的覆盖图](https://bryanlrobinson.com/blog/2018/04/30/how-to-css-after-elements-for-background-overlays/)。在这种情况下，因为我们的主要背景是一个`<img>`标签，所以一个`::after`元素将不起作用。

为了解决这个问题，我们可以在整个宣传片区域添加一个`background-color`，并在图像上使用`mix-blend-mode`来混合这两个区域。

为了避免混合所有的元素，我们可以添加一个 z 索引来影响我们的堆叠上下文。通过将我们的图像的 z-index 设为 0，将我们的文本元素的 z-index 设为 1(或更大)，只有图像和背景颜色会被混合。

## 为平板和大手机添加断点

这是容易的部分。对于较大的手机和平板电脑，让我们设置一个断点，将我们的宣传片并排放置。

在本例中，我们有两个促销，所以我们将网格设置为两列。如果您有动态内容，您可以探索创建一个流体卡网格。

```
@media (min-width: 640px) and (max-width: 1024px) {
    .promos {
        display: grid;
        grid-template-columns: 1fr 1fr;
        grid-gap: 1rem;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/45ca6d67b5185c7c7c1b2048eee95489.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LDbw094N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.com/images/responsive-just-desktop.jpg)

## 现在为了娱乐:桌面重新配置

平板电脑很简单:增加一个额外的列。完成了。对于桌面，让我们为每个宣传片创建一行，让他们喘口气。

要构建新的网格模板，我们需要做的就是添加一列并调整网格模板区域。

```
grid-template-columns: 1fr 1fr;
    grid-template-areas: '.......  image'
                         'headline image'
                         '  text   image'
                         ' button  image'
                         '.......  image';
    grid-column-gap: 20px; 
```

Enter fullscreen mode Exit fullscreen mode

由于我们命名了网格区域，它们将自动流入它们的新家。

现在我们的网格已经就位，让我们做一些风格上的选择来巩固这个设计。

让我们通过设置宽度和自动边距，在宣传片的两边添加一点空白。

我们还会将宣传片的字体颜色和背景颜色重置为适合宣传片所占空间的值，并将文本向左对齐，而不是居中对齐。

```
 .promos {
        width: 95vw;
        margin: auto;
    }
    .promo {
        background-color: transparent; // Turns background color off
        color: black; // Inverts text color
        text-align: left; // My preference for this style of promo
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 宣传片上的备选图片和文字位置

如果你和我一样，看到那些画面在屏幕右侧重复出现会让你感到困扰。我们可以通过添加一个类和一个网格模板区域重置来解决这个问题。

```
.promo.even {
    grid-template-areas: 'image ....'
                         'image headline'
                         'image text'
                         'image button'
                         'image ....';
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们就有了一套有趣的设计模式，它们与观看它们的设备最匹配。和往常一样，[你可以玩 CodePen](https://codepen.io/brob/pen/dKWdVB?editors=1000) 上的代码。

有哪些其他的设计模式可以与网格的力量很好地结合在一起？请在评论中告诉我你认为什么会有趣！

### 免费电子书-常用 CSS 网格模式

CSS 网格是网页布局的一次革命。在这本电子书中，我介绍了 Grid 更容易、更好、更有创造性地解决的 3 种设计模式，以帮助我们的设计朝着更好的方向发展。

[立即下载！](https://store.codecontemporary.com/solving-three-design-problems-with-css-grid/buy)