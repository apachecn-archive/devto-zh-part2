# Xamarin 的自定义效果。名为 AiForms 的表单。效果

> 原文：<https://dev.to/muak_x/custom-effects-for-xamarinforms-named-aiformseffects-47og>

我认为 Xamarin。表单控件没有足够的基本功能。但是使用 Xamarin。效果使我们能够给控件添加各种功能。所以我做了一个名为 AiForms 的效果库。为了充分利用 Xamarin 的作用。表单控件。

## 储存库

[https://github.com/muak/AiForms.Effects](https://github.com/muak/AiForms.Effects)

## Nuget

[https://www.nuget.org/packages/AiForms.Effects/](https://www.nuget.org/packages/AiForms.Effects/)

您需要将这个 nuget 包安装到 PCL 项目和每个平台项目中。

### 为 iOS 项目

要在 iOS 上使用，需要在 AppDelegate.cs 中写一些代码。

```
public override bool FinishedLaunching(UIApplication app, NSDictionary options) {
    global::Xamarin.Forms.Forms.Init();

    AiForms.Effects.iOS.Effects.Init();  //need to write here

    LoadApplication(new App(new iOSInitializer()));

    return base.FinishedLaunching(app, options);
} 
```

## 最小设备和版本等

iOS: iPhone5s，iPod touch6，iOS9.3
Android:版本 5.1.1(仅限 formsapcompactivity)/API 22

### 特性

|  |  |
| --- | --- |
| AddCommand | 向视图添加命令。 |
| AddDatePicker | 向视图中添加 DatePicker 函数。 |
| AddNumberPicker | 向视图添加 NumberPicker 函数。 |
| AddTimePicker | 向视图中添加 TimePicker 函数。 |
| AddText | 在视图中添加一行文本。 |
| 添加触感 | 将触摸事件(开始、移动、结束、取消)添加到视图。 |
| AlterLineHeight | 改变标签和编辑器的行高(行距)。 |
| 水彩画 | 改变不能改变的元素颜色。 |
| 边境 | 向视图添加边框。 |
| 占位符 | 在编辑器中显示占位符。 |
| SizeToFit | 调整字体大小以适合标签大小。 |
| ToFlatButton | 将一个按钮改为平面(适用于 Android)。 |

## 概述

### 添加命令

这是向视图添加命令功能的效果。这提供了诸如点击动作、长点击动作、触摸反馈颜色、点击声音和改变 IsEnabled 属性的功能。

[https://www.youtube.com/embed/YdFHtPSuRg4](https://www.youtube.com/embed/YdFHtPSuRg4)

### AddDatePicker

这就是向视图添加日期选择器的效果。当点击一个视图时，显示一个日期选择器，当选择一个日期时，该日期将反映在日期属性值中。此外，如果设置了 Command 属性，将会执行该命令。

在 iOS 上，如果设置了 TodayText 属性，将显示今天的按钮，如果点击该按钮，将选择今天。

[https://www.youtube.com/embed/5vwkbO2yeeE](https://www.youtube.com/embed/5vwkbO2yeeE)

### 添加数字选择器

这是在视图中添加数字选择器的效果。当点击一个视图时，显示一个数字选择器，当选择一个数字时，该数字将反映在数字属性值中。此外，如果设置了 Command 属性，将会执行该命令。

[https://www.youtube.com/embed/8VlmCXYVeJ8](https://www.youtube.com/embed/8VlmCXYVeJ8)

### AddTimePicker

这是向视图添加时间选择器的效果。当点击一个视图时，显示一个时间选择器，当选择一个时间时，该时间将反映在时间属性值中。此外，如果设置了 Command 属性，将会执行该命令。

[https://www.youtube.com/embed/LA5oQ1m4WxA](https://www.youtube.com/embed/LA5oQ1m4WxA)

### AddText

这是在视图中添加一行文本的效果。例如，如果您使用这个效果，您将能够显示诸如验证和字符计数器等信息。通过指定每个相应的属性，您可以更改文本位置(左上、右上、左下、右下)、文本颜色、字体大小和边距。

[https://www.youtube.com/embed/MgCa4PxtbGc](https://www.youtube.com/embed/MgCa4PxtbGc)

### 添加触摸

这是向视图添加触摸事件(开始、移动、结束、取消)的效果。每个触摸事件都提供位置属性，可以取 X 和 Y 位置。

[https://www.youtube.com/embed/9zrVQcr_Oqo](https://www.youtube.com/embed/9zrVQcr_Oqo)

### 改变高度

这是改变标签和编辑器行高的效果。如果指定了 Multiple-property 的值，行高将成为字体高度* Multiple 属性值。

[https://www.youtube.com/embed/FEWoP_xFD1k](https://www.youtube.com/embed/FEWoP_xFD1k)

### 改变颜色

这是改变视图中不能正常改变的部分的颜色的效果。这支持滑块和开关这样的视图。

[https://www.youtube.com/embed/FJ9QDLe5wlU](https://www.youtube.com/embed/FJ9QDLe5wlU)

### 边框

这就是给视图添加边框的效果。可以通过指定每个相应的属性来更改边框的宽度、颜色和半径。

尽管在 iOS 上，默认情况下 Entry、Picker、DatePicker 和 TimePicker 都有边框，但是可以通过将 Width 值更改为 0 来隐藏边框。

[https://www.youtube.com/embed/UPnQvpv8WWs](https://www.youtube.com/embed/UPnQvpv8WWs)

### 占位符

这是在编辑器中显示占位符的效果。这仅支持编辑器。

[https://www.youtube.com/embed/MbBgXKNVw2U](https://www.youtube.com/embed/MbBgXKNVw2U)

### SizeToFit

这是使字体大小调整到适合标签大小的效果。这只能用作标签。

[https://www.youtube.com/embed/yMjcFOp38XE](https://www.youtube.com/embed/yMjcFOp38XE)

### ToFlatButton

这是 Android 上把按钮改成平面的效果。如果使用这种效果，你将能够设计出像 iOS 一样的按钮。这也将使你能够改变 BorderRadius，BorderWidth 和 BorderColor，它们是 Android 上默认的按钮属性。

[https://www.youtube.com/embed/4aqQoZltPfE](https://www.youtube.com/embed/4aqQoZltPfE)

## 总结性的

如果你能使用这个图书馆，我将不胜感激。

如果有什么问题、请求、bug 报告，请向 GitHub issues 或 twitter ( [@muak_x](https://twitter.com/muak_x) )反映。

谢谢你。