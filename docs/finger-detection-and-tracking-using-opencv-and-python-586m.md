# 基于 OpenCV 和 Python 的手指检测和跟踪

> 原文：<https://dev.to/amarlearning/finger-detection-and-tracking-using-opencv-and-python-586m>

**TL；博士这里的代号是**。

手指检测是许多计算机视觉应用的重要特征。在该应用中，使用基于直方图的方法将手从背景帧中分离出来。阈值和过滤技术用于背景消除，以获得最佳结果。

在检测手指时，我面临的一个挑战是将手从背景中区分出来，并识别指尖。我将向你展示我在这个项目中使用的追踪手指的技术。要查看实际的手指检测和跟踪，请观看此视频。

[https://www.youtube.com/embed/P3dUePye_-k](https://www.youtube.com/embed/P3dUePye_-k)

在你想追踪用户手部运动的应用中，肤色直方图将非常有用。这个直方图然后被用来从图像中减去背景，只留下图像中包含肤色的部分。

一种简单得多的检测皮肤的方法是找到某个 RGB 或 HSV 范围内的像素。如果你想了解更多关于这种方法的信息，请点击[这里](https://docs.opencv.org/3.4.2/df/d9d/tutorial_py_colorspaces.html)。

上述方法的问题在于，改变光线条件和肤色确实会干扰皮肤检测。而另一方面，直方图往往更准确，并且考虑了当前的光照条件。

[![Hand over the rectangles](../Images/8b5e53a9dee39dab3bbccfe835d8e967.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iIEtBPzW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aaijxoqwmrkyx8epxq4t.png)

绿色的矩形绘制在框架上，用户将手放在这些矩形内。应用程序从用户的手上提取肤色样本，然后创建一个直方图。

*矩形用以下函数绘制:*