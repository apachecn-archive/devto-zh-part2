# CSS 选择器的终极列表

> 原文：<https://dev.to/neshaz/the-ultimate-list-of-css-selectors-44jg>

作为一名前端开发人员，我每天要写大约 238 行 CSS，我需要这个。以下文本中没有任何东西是真正属于我的，它只是从每个人都可以获得的资源中收集的(不仅仅是 [w3schools](https://www.w3schools.com) )。在这篇文章的底部，你有这张**终极列表图片**，你可以轻松下载。

**注意空格！**

## 绝对常青树

`*`
所有元素被选中。

`.banana`
选择香蕉类的所有元素。

`#banana`
选择 id 为 banana 的所有元素。

`p`
所有的< p >元素都被选中。

## 相对常青树

`div, p`
所有< p >和< div >元素被选中。

`div p` -后代选择器
选择< div >元素内的所有< p >元素。

`div > p` -直接子元素
选择直接父元素为< div >的所有< p >元素。
*反过来也一样(p < div)*

`div + p`
所有< p >元素的位置紧跟在< div >元素被选中之后。

`div ~ p`
选择前面有< div >元素的所有< p >元素。

## 特定类别

*这个类常用于属性:target、title、lang 和 href* 。

`[title]`
选择所有具有标题属性的元素。

`[title=banana]`
title 属性中具有‘banana’值的所有元素。

`[title~=banana]`
title 属性值中包含‘banana’的所有元素。

`[title|=banana]`
title 属性值以‘banana’开头的所有元素。

`[title^=banana]`
title 属性值以‘banana’开头的所有元素。

`[title$=banana]`
title 属性值以‘banana’结尾的所有元素。

`[title*=banana]`
title 属性值包含子串‘banana’的所有元素。

## 准常青树

这些选择器被称为* *伪类 *和*，它们必须与其他选择器呆在一起，并描述其状态或属性*。

`:hover`
处于悬停状态时选择元素。

`:link`
选择链接的元素(一次带 href 属性)。

`:visited`
选择已被访问过的元素。

`:active`
选择激活(点击)的元素。

`:focus`
当元素被聚焦时选择元素。

`:checked`
选中时选择元素。

`:required`
选择具有所需属性的元素。

`:optional`
选择没有所需属性的元素。

`:first-child`
选择父元素内的第一个元素。

`:nth-child(number)`
根据数字选择子元素(如果是 2，则选择第二个子元素，如果是 2n，则选择 steam 位置的所有子元素)。

`:only-child`
仅当元素是唯一的子元素时才选择它。

`:first-of-type`
选择该类型的第一个元素。

`:nth-of-type(number)`
根据一个数字选择该类型的子元素(如果是 2，则选择该类型的第二个，如果是 2n，则选择该类型在 steam 位置的所有子元素)。

`:only-of-type`
仅选择类型唯一的元素。

`:empty`
选择不包含文本或子元素的元素。

更多的伪类请看这篇文章

接下来的几个选择器被称为伪元素，它们只选择元素的一部分

`::before:`
在元素前添加内容(例如，开始引用、图标、图像...).

`::after`
在元素后添加内容(如右引号、clearfix...).

`::first-letter`
选择元素的第一个字母。

`::first-line`
选择元素的第一行。

`::selection`
选择被选中元素的一部分。

## 独立热播

`* + *` -脑叶切除的猫头鹰
处理其他元素的所有元素。

[![css-selectors---ultimate-hits-1](img/92fc674e043b9bb720965a0ef4ab410e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zChYX1mY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/conteimg/2018/02/css-selectors---ultimate-hits-1.png)

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-selectors/)上。