# 如何不使用 HTML 表格元素制作表格

> 原文：<https://dev.to/flippedcoding/how-to-make-a-table-without-using-the-html-table-elements-5e11>

有一天你会做一个需要桌子的项目。您可以选择用几种不同的方式制作表格:使用 HTML、使用 CSS 或使用 JavaScript。它们各有利弊，但是如果你不想处理 HTML 中所有的标签或者用 JavaScript 编写代码，你可以使用 CSS。

使用 CSS 制作一个简单的表格相对来说比较简单。你需要知道的只是 HTML 和 CSS 的基础知识。要制作一个没有元素的表格，您将使用大量的元素。元素的好处是它们不像

元素那样预先加载了样式。这意味着您可以应用样式，而不必担心为什么有些东西看起来不合适。

我们将快速浏览用这种方法制作桌子的指南。你首先需要的是你的 HTML。为了简单起见，我们将制作一个 3 x 3 的桌子。

```
<div class="table">
   <div class="column">
       <div class="row"></div>
       <div class="row"></div>
       <div class="row"></div>
   </div>
   <div class="column">
       <div class="row"></div>
       <div class="row"></div>
       <div class="row"></div>
   </div>
   <div class="column">
       <div class="row"></div>
       <div class="row"></div>
       <div class="row"></div>
   </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到这些

标签上有一些类。我喜欢保持事情简单，所以我们将只使用一些基本的名称:表，行和列。仅根据类名，您可能就能猜出每个元素是什么。

目前，如果你在浏览器中打开它，你什么也看不到。那是因为这些元素上没有任何样式。现在是写 CSS 的时候了。

```
.table {
    display: flex;
}

.column {
    margin-bottom: 1em;
    width: 100%;
}

.row {
    border: 1px black solid;
    height: 3em;
    margin: auto;
    width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这张桌子的样式并不复杂。需要注意的重要一点是 table 类的 flex 值。这是使列 div 彼此相邻的原因。在 row 类中，您将看到 border 和 height 属性的值。你的桌子不一定要有这些才能工作。他们现在就在这里，所以我可以向你展示这是什么样子。

[![basic CSS table](img/c7ce4b7d26c750719525b8351a5dae6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Btsh_d78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s6jsh1dmito2vs7psql6.JPG)

现在你可以在行 divs 中写任何代码，它会出现在这个表中。这绝不是使用 CSS 制作表格的唯一方法。我在 CSS Flexbox 和 CSS Grid 上写了一整套东西，它肯定会向你展示更多的东西。

去用这段代码做一个你自己的表格，并使用它。任何时候你玩代码，你都会学到新的东西，即使是相对小的东西。

我希望这教会了你一些新的东西！或者至少能让你忙上几分钟。:)

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)