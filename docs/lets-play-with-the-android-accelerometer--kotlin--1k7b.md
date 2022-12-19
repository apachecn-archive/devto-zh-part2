# 让我们玩玩 Android 加速计+ Kotlin

> 原文：<https://dev.to/enzoftware/lets-play-with-the-android-accelerometer--kotlin--1k7b>

这篇文章最初发表在[媒体](https://medium.com/@enzoftware/lets-play-with-the-android-accelerometer-kotlin-%EF%B8%8F-ed92981b0a6c)上

* * *

# 关于传感器的一般方面

Android 设备往往配有一系列传感器，使设备能够与周围的环境进行交互。

这已经很好了，我们可以在我们的应用中做一系列的可能性，这些传感器允许终端了解你周围的环境，但传感器只不过是从外部捕捉信息的电子部件，应用程序应该将这些信息转换为数据来使用它们。

[![sensors](../Images/f4768cd224fa80314fcddd622e3132f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QLKcY5fp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AHgTref-c5Lh_kX33yQC8-g.jpeg)

使用传感器的活动必须实现接口 SensorEventListener，该接口将强制我们实现方法**onAccuracyChanged()**y**onSensorChanged()**。

当一个传感器的精度改变时，将执行方法 **onAccuracyChanged()** ，而 **onSensorChanged()** 将在每次其中一个传感器发生变化时执行。

# 打造加速度计 app

android 中的传感器主题非常大，一篇文章根本无法涵盖，因此在这种情况下，我们将构建一个加速度计应用程序 metallball💥。

[![final app](../Images/0aec8fc505dffbdb58c3ced0dcd0e890.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_V1Lajyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AXvbMaUpE-EWxZ-2ZTjppgw.gif)

但首先要具体介绍一下这个传感器。

## 加速度计

利用这种传感器，有可能确定终端在现实世界中的方位🌎，将设备的中点视为坐标轴。

[![device](../Images/ea0996a597d11542d9db73a36e0eb5eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WXmW8Igw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AoyiKkfu-3zGIIqwk9mLIzw.png)

加速度计计算 3 个轴(x，y，z)中每个轴的线性加速度；每个轴都有自己的加速度计，因此可以单独接收数据。

# app 代码

在 **MainActivity** 文件中，您可以访问振动服务，因此不要忘记将它复制到**和 AndroidManifest** 文件中。

```
<uses-permission android:name="android.permission.VIBRATE" /> 
```

Enter fullscreen mode Exit fullscreen mode

这里是该应用程序的完整代码，如果你有问题，张贴它能够回答。