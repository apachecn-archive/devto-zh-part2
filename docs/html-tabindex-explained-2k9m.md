# HTML Tabindex 解释道

> 原文：<https://dev.to/neshaz/html-tabindex-explained-2k9m>

在处理元素对焦的时候， [tabindex](https://www.w3schools.com/tags/att_global_tabindex.asp) 的作用很大。该属性指示元素是否可以通过键盘导航被**聚焦和到达。当按下 **Tab 键**时，浏览器将焦点从一个元素转移到另一个元素。通过使用 tabindex，我们可以改变 tab 键顺序**流**。**

当创建任何元素时，应该包括[样式](https://kolosek.com/10-ways-to-improve-your-website-design/)。

## 值

tabindex 接受整数值。它们可以是:

*   **负的**(通常是`tabindex=”-1”` ) -元素不能通过键盘导航到达。
*   `tabindex=”0”` -元素可通过键盘导航访问，但顺序由文档源顺序定义。
*   **正**——元素可以通过键盘导航到达，顺序由值定义。例如，`tabindex=”2”`在`tabindex=”3”`之前是可聚焦的，但是在`tabindex=”1”`之后。

## 用法

Tabindex 可用于以下元素:

*   **输入**，
*   **锚(一)**，
*   **textarea** ，
*   **选择**，
*   **按钮**。

创建这些元素后，给文本一个合适的[行高](https://kolosek.com/css-line-height/)。

## 举例

*HTML*

```
<body>
  <label>This element will be focused second</label>
  <input name=”field1” type=”email” tabindex=”2” />

   <label>This element will be focused first</label>
  <textarea rows=”4”  cols=”5” tabindex=”1”></textarea>

   <label>The second focused last</label>
  <input name=”field3” type=”text” tabindex=”3”/>
</body> 
```

这里我们可以看到选项卡选择的顺序，从最低的**到最高的**。如果一个元素有`tabindex=”0”`，它将被**从**中排除。如果两个元素具有相同的 tabindex 值，将按照它们在元素中出现的顺序选择它们。

如果你觉得这篇文章有趣且有用，可以看看其他主题，比如[相对字体大小](https://kolosek.com/css-relative-font-size/)、 [CSS 列](https://kolosek.com/css-columns/)以及关于 [SASS and LESS](https://kolosek.com/nesting-in-less-and-sass/) 的一些小知识。

## 总结

这篇短文总结了 tabindex 的用法。掌握了这一点，你就能写出更加高效和用户友好的表单。

感谢您的阅读和快乐编码！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/tabindex/)上。