# CSS 列

> 原文：<https://dev.to/neshaz/css-columns-1ipc>

自杂志布局以来，文本分隔选项就不是最流行的文本选项了(例如，[文本下划线](https://kolosek.com/css-text-underlined/)更有趣)。就像印刷设计一样，网页设计也有专栏。最好和最清楚的使用方法是 CSS 中的**列选项。**

从内容和设计的角度来看，文本内容扩展到整个网页是很无聊的，所以这种情况很少见。请注意，博客内容因其性质而被排除在外。

因此，网页上的文本内容被组织在宽度更小的元素中，因此不需要分栏。在列出所有这些事实之后，上周我需要一个关于我正在做的设计的专栏，它是完全合理的(但是左对齐)。

链接到列属性的属性有:

*   **column-count** -指定一个元素应该分成多少列，
*   **列宽** -指定列的宽度，
*   **列-间隙** -指定列之间的间隙，
*   **分栏-嵌线** -设置分栏之间的嵌线(是设置嵌线宽度、嵌线颜色、嵌线样式的简写属性)，
*   **column-span** -指定一个元素应该跨越多少列。 *Internet Explorer 9(及更早版本)和 Firefox 不支持跨列*
*   **列填充** -指定如何填充列(自动或平衡)。

*columns 属性只接受 column-count 和/或 column-width 属性。*

让我解释一下这些属性。

**column-width**属性指定了建议的最佳列宽。**如果定义了列计数，列宽值将被覆盖。**

**列填充**属性仅在 Firefox 中可用。具有列填充平衡的内容将自动平衡高度受限的多栏布局中的内容。当给定高度限制时，其他浏览器将总是按顺序填充列。如果你想在所有的浏览器上都有相同的行为，只需设置`column-fill: auto`。

**可以这样编辑的内容必须是下一个元素< p >、< div >、< h >、< ul >和< ol >** 中的一个。如果没有定义列的高度，则划分到列中的内容定义高度。

## 例子

此示例表示将内容分成两列。

(我用[渐变](https://kolosek.com/everything-you-need-to-know-about-gradient/)来呈现内容流)

```
 <style>
        div{
            -webkit-column-count: 2; /* Chrome, Safari, Opera */
            -moz-column-count: 2; /* Firefox */
            column-count: 2;          
            }
    </style> 
```

[![CSS columns example with content that contain all columns width](img/6cac0c7c7622229fff3cdb6da52b58b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Su7MH9Um--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://kolosek.com/conteimg/2018/02/Blog_-_CSS_columns_-_1.png)

此示例表示将内容分成定义宽度的列，并在它们之间定义间隙。

```
 <style>
        div{
            -webkit-column-width: 300px; /* Chrome, Safari, Opera */
            -moz-column-width: 300px; /* Firefox */
            column-width: 300px;      
            -webkit-column-gap: 150px; /* Chrome, Safari, Opera */
            -moz-column-gap: 150px; /* Firefox */
            column-gap: 150px;
            }
    </style> 
```

[![CSS columns example with content that contain all columns width](img/97c4ca730e3c4ef1e9792f3a9bdad1da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tOwjp7VP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://kolosek.com/conteimg/2018/02/Blog---CSS-columns---2.png)

此示例显示了交错排列在 3 列中的内容，并在它们之间定义了规则。

```
 <style>
        div{
            -webkit-columns: 3; /* Chrome, Safari, Opera */
            -moz-columns: 3; /* Firefox */
            columns: 3;      
            -webkit-column-rule: 1px solid #dbdbdb; /* Chrome, Safari, Opera */
            -moz-column-rule: 1px solid #dbdbdb; /* Firefox */
            column-rule: 1px solid #dbdbdb;
            }
    </style> 
```

[![CSS columns example with content that contain all columns width](img/7c5c00bba00edc2433ce3ce8b946874b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sMYS2Srg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://kolosek.com/conteimg/2018/02/Blog---CSS-columns---3.png)

最后一个示例将内容表示为列和元素，包括所有列宽。

```
 <style>
        div{
            -webkit-columns: 3; /* Chrome, Safari, Opera */
            -moz-columns: 3; /* Firefox */
            columns: 3;      
            }
        h1{
            -webkit-column-span: all; /* Chrome, Safari, Opera */
            column-span: all;
            }
    </style> 
```

[![CSS columns example with content that contain all columns width](img/4956cd4a0294c1557bb7563f657853dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d3w2a2Dz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://kolosek.com/conteimg/2018/02/Blog---CSS-columns---4.png)

我相信这就是 CSS 中关于列属性的所有内容。希望有帮助！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-columns/)上。