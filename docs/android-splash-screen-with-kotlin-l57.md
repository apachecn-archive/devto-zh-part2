# Android:kot Lin 闪屏

> 原文：<https://dev.to/robertoissc/android-splash-screen-with-kotlin-l57>

关于你的 Android 应用程序是否有闪屏，有不同的观点。

就我个人而言，我认为闪屏是一个很好的地方，你可以在这里展示你的应用程序的良好设计，并给你的用户留下良好的第一印象。

这就是我最近用 Kotlin 在我的 Android 应用上实现闪屏的方式。

## 静态与动态

我用过静态和动态闪屏，这里有一些想法。

### 静态

*   仅在调用 onCreate()之前显示在应用程序的启动中。
*   你没有浪费用户的时间。
*   显示闪屏时，不打算运行操作。

### 愉快的

*   你可以展示一个很酷的动画和自定义设计。
*   动画结束时执行某些操作的灵活性。
*   如果动画没用或者花的时间太长，用户会感到厌烦。

## 静态 Android 闪屏

[![one-breath-splash](../Images/ce16871b0aece3e2c7cfcf6e8461682a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CFUMSrKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://isscroberto.files.wordpress.com/2018/06/one-breath-splash.gif)

### **1。**

创建可绘制的文件，我们将在进入活动中用作背景，通过使用背景，应用程序将显示它，而不必膨胀布局文件。