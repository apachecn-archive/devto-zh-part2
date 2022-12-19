# 输入文本[你应该知道的一切]

> 原文：<https://dev.to/neshaz/input-text-everything-you-should-know-7b4>

输入在用户体验设计中是如此重要，它的风格完美地从设计转移到页面上是至关重要的。众所周知，网页演示需要 CSS，但棘手的是如何让它尽可能好。

与 HTML 中的任何其他元素相比，输入有更多的变化、状态和事情,所以，让我们讨论一下关于 post 输入类型文本应该知道的一切。让我们打破输入！

## 输入[type="text"]

输入文本在预期文本的位置显示一个空白方块。这是一个非常常见的元素，在大多数情况下，呈现一个被彩色边框包围的白色元素。在里面添加一个占位符文本，在外面加上一个标签，这是一个很好的做法。

[![Screen-Shot-on-May-4th-at-04_34-PM](../Images/e2d481700e0b61efa2b0eb4d72f75479.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ehvTDj1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-4th-at-04_34-PM.png) 
*默认输入文本*

[![Screen-Shot-on-May-7th-at-10_24-AM](../Images/6e64cbcbb2086d12f27021e689aa860d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OBYwVrVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-7th-at-10_24-AM.png) 
*默认输入文本处于焦点状态*

[![Screen-Shot-on-May-7th-at-09_50-AM](../Images/6b06b8bb19bde1ae965d7be9b7122f15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pARNOIsK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-7th-at-09_50-AM.png) 
*默认占位符文本*

[![Screen-Shot-on-May-8th-at-10_57-AM](../Images/d138fed181014944db9ab6f9f9cc2931.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOaBZGQy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-8th-at-10_57-AM.png) 
*默认自动填充状态*

为了给输入添加想要的样式，有几个标准选项和几个特殊选项。**如果输入有一个标签，它被添加到输入旁边的`label`元素中，它的`for`属性与链接的输入元素的`id`属性具有相同的值。**标签元素的样式可以像标准的 HTML 文本元素一样。

对于输入，改变尺寸、`border`、`background-color`、`border-radius`和`font`选项是标准的。设置占位符文本、焦点、自动填充和验证状态的样式是不标准的。这些非标准选项将在下一篇文章中解释，之后，将给出一些不同选项的例子。

## 一个占位符文本

为了对带有占位符属性的占位符文本集进行样式化，使用了`::placeholder`伪元素。支持所有文本样式(字体、颜色、背景、不透明度、单词间距、文本转换、[行高](https://kolosek.com/css-line-height/)...).在 Firefox 浏览器中，所有占位符都有一个应用的不透明度值，因此为了[重置](https://kolosek.com/css-reset/)该值，有必要定义不透明度。

```
 ::-webkit-input-placeholder { /* Chrome/Opera/Safari */
     ...
 }
 ::-moz-placeholder { /* Firefox 19+ */
     opacity:1;
     ...
 }
 :-ms-input-placeholder { /* IE 10+ */
     ...
 }
 :-moz-placeholder { /* Firefox 18- */
     ...
 } 
```

示例:

```
 ::placeholder{
    color: coral;
    font-size: 14px;
    font-family: sans-serif;
    letter-spacing: .2px;
    padding-left: 5px;
 } 
```

### 结果:

[![Screen-Shot-on-May-7th-at-10_58-AM](../Images/aa62ab93b53eea5de355b5c87e49065b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TjlnCe4q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-7th-at-10_58-AM.png)

## 焦点状态

焦点状态对用户体验很重要，它存在于每个输入元素中。*用于指示当前目标元素。这种状态在元素周围用蓝色边框表示，在 CSS 中用伪类`:focus`选择。*

聚焦元素周围的蓝色边框添加了默认线条`outline: auto 5px -webkit-focus-ring-color`，默认颜色值在浏览器主题中定义。在某些情况下，这种表示会惹恼元素的设计者，有些人会选择在下一段代码中禁用它:

```
 :focus{
     outline:none;
 } 
```

这是一种非常糟糕的做法，应该避免。你可以用自己的方式来设计焦点状态的样式，而不需要包含一个轮廓。

示例:

```
 :focus{
     outline:none;
     border:1px solid coral;
     box-shadow:0 0 5px coral;
 } 
```

### 结果:

[![Screen-Shot-on-May-7th-at-03_39-PM](../Images/fba2265b3da0d6bdaa95d752c6ade8df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XD_MuX9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-7th-at-03_39-PM.png)

在这种情况下(以及任何其他情况下),添加一个[过渡](https://kolosek.com/css-transition/)是个不错的主意！).

## 自动完成状态

Webkit 浏览器提供了一种设置，允许用户自动填写常见表单字段的详细信息。在填写要求填写姓名、地址和电子邮件等信息的表单时，您会看到这种情况。**自动完成字段**有默认的黄色背景，这可能会干扰设计，也可能很容易改变(在这种模式下，你也可以改变文本颜色)。

```
 :-webkit-autofill {
    -webkit-box-shadow: 0 0 0 1000px mintcream inset !important;
 }

 :-webkit-autofill {
    -webkit-text-fill-color: coral !important;
 } 
```

### 结果:

[![Screen-Shot-on-May-7th-at-11_45-AM](../Images/08680b63cddfa2e0d339039f287ba489.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DUSP_fyE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/Screen-Shot-on-May-7th-at-11_45-AM.png)

## 验证状态

有两个相对较新的伪类`:valid`和`:invalid`。这些伪类对于向用户突出显示成功和失败非常有用。当内容有效时，通常会看到**为绿色**，如果内容无效，通常会看到**为红色**，所以现在你可以很容易地用 CSS 来定义这一点。请记住，旧版本的浏览器不支持它。

```
 :valid {
    box-shadow: -5px 0 lime;
    border-left: none;     
 }

 :invalid {
    box-shadow: -5px 0 orangered;
    border-left: none;     
 } 
```

使用 *`box-shadow`选项代替`border-left`是因为当一个元素的边框有不同的样式或颜色*时会有视觉问题

### 结果:

[![validation-examples--1-](../Images/5dbce93f1d66df72a5fe7ca9059fa1f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xmI0WJrb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/validation-examples--1-.png)

## 额外提示

输入字段的另一个问题是在 iOS 上出现的，因为添加了默认样式。有一些额外的圆角和阴影。我发现解决这个问题的最好方法是来自 [daretothink 博客](https://www.daretothink.co.uk/stop-ios-styling-your-input-fields-and-buttons/)的一个:

```
-webkit-appearance: none;

border-radius: 0; 
```

## 
  
好例子

[![input text example](../Images/54fc1652eb0f46a1e3c8bb8f67e27801.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--69S9Dqax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.dribbble.com/users/3375/screenshots/1684401/newsletter-animation-final.gif)
[![input text example](../Images/c7424fbe3c3de79e1d1e4b2000becfa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Sem5R_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://kolosek.com/content/images/2018/05/derna-form-orange.gif)
[![input text example](../Images/5b115ab117f4aa048b3ae466fe28744b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TDswEvP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://kolosek.com/content/images/2018/05/payment-1.gif)
[![input text example](../Images/3725d8b955c4f4c08e37ca9b7e137db9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VdBUoCMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kolosek.com/content/images/2018/05/fb_d_signup.png)
[![input text example](../Images/815dfd07aba3deef6ca9855f6a3bf847.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZI7Vm-1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dribbble.com/users/941294/screenshots/4537544/sign_up_2.png)
[![gif input text example](../Images/c7ec7d1ffea5f6f8d68764911ec04f2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AIA9_HV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.dribbble.com/users/1172503/screenshots/4505740/login-form.gif)
[T25】](https://res.cloudinary.com/practicaldev/image/fetch/s--ud__Wa5---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dribbble.com/users/1706197/screenshots/4475408/daily_ui_2.jpg)

## 关闭字

输入字段设计是你可以经常做的事情，也是你的用户肯定会喜欢的事情。我再次敦促你在造型中使用过渡。

本文原载于 [Kolosek 博客](https://kolosek.com/input-text/?utm_source=dvt)。