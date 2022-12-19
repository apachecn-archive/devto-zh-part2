# 如何在 Android Studio 中模拟给模拟器的来电或短信

> 原文：<https://dev.to/nikola/how-to-simulate-an-incoming-call-or-sms-to-an-emulator-in-android-studio--3hfg>

[![](../Images/619563dbfb3deef99288a637d3085e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---QYTrAZU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t8tgtgm0vkcj38i89b4.jpg)

*原载于[我的博客](http://www.nikola-breznjak.com/blog/android/simulate-incoming-call-sms-emulator-android-studio/)*

在这个快速技巧中，我将向您展示在 Android Studio 中模拟来电或短信到模拟器是多么容易。

## TL；速度三角形定位法(dead reckoning)

在 Android Studio 中，进入`Tools->Android->Android Device Monitor`

[![](../Images/b470ce853953c22774bbdf6ff45ba2bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PKttRev_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/i3uDl2P.png)

选择左边的仿真器(你必须先运行它——通过`Tools->Android->AVD Manager`来完成)，然后选择`Emulator Control`选项卡，插入数字，选择`Voice`或`SMS`并点击`Call`或`Send`按钮:

[![](../Images/3a76f6d2d2c19c13710e0bb2f22f7540.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HVDKBAkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/yf802Im.png)

查看这两种功能的运行情况:

[![](../Images/c4e6c46ac95e18b2ed0923fc68c22783.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KQ564_vw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/nr0Sj7b.gif)

## 结论

在这篇小技巧中，我向您展示了在 Android Studio 中模拟来电或短信到模拟器是多么容易。

Android Device Monitor 还允许你设置其他东西，比如当前位置、网络偏好(在不太理想的连接情况下测试你的应用)等。

希望这有所帮助💪