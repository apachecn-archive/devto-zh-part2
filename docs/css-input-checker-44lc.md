# CSS 输入检查器

> 原文：<https://dev.to/genta/css-input-checker-44lc>

# 简单的东西...酷的结果

这里我们将使用:

:不是()

> not() CSS 伪类表示与选择器列表不匹配的元素。因为它阻止特定的项目被选择，所以它被称为否定伪类。

:占位符-显示

> :placeholder-showed CSS 伪类表示当前显示占位符文本的任何或元素。

## 用 CSS 检查输入

如果我们将它们与输入(带占位符)一起使用，就像这样:

`input:not(:placeholder-shown)`

我们可以管理页面上的内容，当输入表单中有内容时，我们只显示**，**而不使用 Javascript** 。**

 **<center>HTML</center>

```
 <input placeholder="My Placeholder">
<div class="text">Now... you can see me</div> 
```

Enter fullscreen mode Exit fullscreen mode

<center>CSS</center>

```
 .text{
      opacity: 0;
     }

input:not(:placeholder-shown) + .text{
      opacity: 1;
     } 
```

Enter fullscreen mode Exit fullscreen mode

( **+** 选择器将捕捉紧接在`input`之后的`.text`

这样我们就*隐藏了*和`.text`，只是为了在`input`没有占位符的时候显示出来，所以，当我们重写它的时候！

[![cool](img/d0638dee87eb2a9caed0c575d97eb3f5.png)T2】](https://i.giphy.com/media/l4KhStwfccoc7t6IU/giphy.gif)**