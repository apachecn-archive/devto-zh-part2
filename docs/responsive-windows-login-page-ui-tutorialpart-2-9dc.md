# 响应式 Windows 登录页面 UI 教程—第 2 部分

> 原文：<https://dev.to/munamohamed94/responsive-windows-login-page-ui-tutorialpart-2-9dc>

[![](../Images/70d7003264fe6af42059c5f102e7660b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IIxUYCuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lh91jbl42v1m9ahfyhtr.png)
*原本贴在介质上。

谁准备好第二部分了？

在本教程的第 2 部分，我们将使用 SCSS 变量、混合和媒体查询使 Windows 登录页面具有响应性。迷茫？别担心。你能行的！一如既往，本教程是初学者友好的。尽情享受吧！:)

如果你还没有，看看本教程的第 1 部分，了解我们是如何构建、设计和制作登录页面动画的。

### **【SCSS 变量(针对屏幕尺寸)**

```
//Variables
$mobile-width: 340px;
$tablet-width: 500px; 
```

Enter fullscreen mode Exit fullscreen mode

SCSS 的美丽在于它的特色，其中一个特色就是变化。

那么什么是 SCSS 变量呢？它们基本上是一种存储信息的方法，这些信息可以在整个样式表中重用。要使某个东西成为变量，您可以使用' $ '后跟变量名和您想要存储在该变量中的信息。

为了开始使登录页面响应的过程，我们首先使用两个 SCSS 变量$mobile-width 和$tablet-width 来存储我们希望登录页面响应的设备的宽度。这里，我们将 mobile-width 变量设置为 340 像素，将 tablet-width 变量设置为 500 像素。

### **媒体查询(使用 mixins)**

```
//Mixin - Media queries
@mixin mobile {
   @media (min-width: #{$mobile-width}) and (max-width: #{$tablet-width - 1px}) {
     @content;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下一步是我们的媒体提问。我们将使用前面声明的变量和一个 mixin 来使我们的媒体查询更容易操作，并提高代码的可读性。

我们将从 mixin 开始，其中嵌套了媒体查询。我们使用前面声明的变量来定义媒体查询中的屏幕大小。有了@content，我们可以将内容块传递给 mixins。

具体来说，我们有一个名为 mobile 的 mixin，其中包含一个媒体查询，该查询指出在移动设备宽度(340 像素)和平板设备宽度(500 像素)之间，应该应用 CSS 规则块。

### **应用 mixin 使用[@包含](https://dev.to/include)**

```
@include mobile {
  #login-page-container {
    font-size: 12px !important;
  }
  #login-page-inner {
    width: 300px !important;
  }
  #user-image-container {
    width: 110px !important;
    height: 110px !important;
  }
  .fa-user-o {    
    line-height: 110px !important; 
  }
  #password {
    width: 200px !important;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的最后一件事是应用包含媒体查询的移动 mixin，该查询适用于屏幕大小在$mobile-width 和$tablet-width 之间的设备。我们通过使用 [@include](https://dev.to/include) 并传递我们想要在其中应用的 CSS 规则来做到这一点。我们的目标是当屏幕尺寸介于我们之前在移动 mixin 中声明的尺寸之间时，使登录页面组件更小。

对于任何与我们在 mobile mixin 中声明的屏幕尺寸不同的屏幕尺寸，我们在前面的[第 1 部分](https://dev.to/munamohamed94/responsive-windows-login-page-ui-tutorial---part-1-----26k1)中在样式表中输入的 CSS 声明都适用。

你会看到术语，！重要的是，在样式表的这一部分中进行 CSS 声明之后。**这个名词！重要的'**'是一个规则，用于覆盖任何以前在样式表中设置的 CSS 声明。

我们首先将#login-page-container 的默认字体大小从 16 像素改为 12 像素。

#login-page-inner 的宽度设置为 300 像素，对于#user-image-container，我们将宽度和高度设置为 110 像素。我们设置用户图标的行高。fa-user-o，设置为 110 像素，使其在新调整的#user-image-container 中垂直居中。最后，我们给#password 一个 200 像素的宽度。

[![](../Images/5bb9d9ee231159aad29c0f8c028781af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_LIWDo_D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qggg66wurg0xdnk3ztcl.gif)

就是这样！在上面的 GIF 中，你可以看到我们的登录页面如何响应不同的屏幕尺寸。

#### **为了方便访问，我把这两部分教程中的笔收集起来，放在 Codepen 上的一个集合中。你可以在这里查看[。](https://codepen.io/collection/AEWVLV/)**

#### **我希望你喜欢本教程的第二部分！下次见！** ✌️