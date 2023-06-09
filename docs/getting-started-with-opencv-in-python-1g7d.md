# python 中的 OpenCV 入门

> 原文：<https://dev.to/rapidnerd/getting-started-with-opencv-in-python-1g7d>

开源计算机视觉库(OpenCV)是利用机器学习的经典和先进的视觉库。它有能力构建应用程序，例如:识别对象，对视频中的人类行为进行分类，跟踪相机移动，跟踪移动的对象，等等。它是用 python 和 C++提供的，Github 或类似的平台上可能还有其他包装器。

首先，我们需要 python 版本，如果你没有这个版本，你可以在:[https://www.python.org](https://www.python.org)下载

我们还需要一些库，首先是 OpenCV 库，来安装它，输入如下:

```
pip install opencv-python 
```

Enter fullscreen mode Exit fullscreen mode

如果您愿意，还可以安装 contributor kit(不需要)

```
pip install opencv-contrib-python 
```

Enter fullscreen mode Exit fullscreen mode

在 OpenCV 项目中，你会发现你会经常使用数字系统，我推荐使用库 Numpy。在本例中，这不是必需的，但是您可以通过在终端中输入以下内容来安装 numpy

```
pip install Numpy 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了图书馆，让我们开始有趣的东西。在这个例子中，我们将拍摄多个人(或你自己)的照片，并应用分割 HSV、饱和度和色调过滤器，以及显示按位过滤器。结果应该是这样的

[![If this shows I probably broke something](img/32e4bfd720b0f1199791cca0f8873bfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PNITdePx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/kLfO9gl.png)

代码

```
 import cv2

img = cv2.imread("mult.jpg", 1)  # image reading
# converting it into Hue, saturation, value (HSV) 
hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)  

# the : in an array in python means that we're going to slice that part of the array 
h = hsv[:, :, 0]
s = hsv[:, :, 1]
v = hsv[:, :, 2]

hsv_split = np.concatenate((h, s, v), axis=1)
cv2.imshow("Split hsv", hsv_split) 

#  some of the values require multiple variables, hence why ret is shown multiple times 
ret, min_sat = cv2.threshold(s, 40, 255, cv2.THRESH_BINARY)
#  showing an image is very simple, first argument is the name, second is the image we wish to show cv2.imshow("Sat filter", min_sat) 

ret, max_hue = cv2.threshold(h, 15, 255, cv2.THRESH_BINARY_INV)  # will do the inverse of the normal threshold 
cv2.imshow("Hue filter", max_hue)

# the final image is the min saturation and the max hue put together 
final = cv2.bitwise_and(min_sat, max_hue)
cv2.imshow("Final", final)

cv2.imshow("Original image", img)

#  the windows will display until a key is pressed, this is using key characters, in this case we're using escape, which is 27 but 0 also works cv2.waitKey(0)
#  destroy all windows will prevent you from having to mass spam the kill keys cv2.destoryAllWindows() 
```

Enter fullscreen mode Exit fullscreen mode

我们结束了。要对此进行测试，只需运行

```
python test.py 
```

Enter fullscreen mode Exit fullscreen mode

在某些操作系统中，您可能需要运行

```
python3 test.py 
```

Enter fullscreen mode Exit fullscreen mode

非常简单的 OpenCV 介绍，这个库很有潜力。
一些有用的链接:
[OpenCV 文档](https://docs.opencv.org/)
[Numpy/辣文档](https://docs.scipy.org/doc/)
[Python 文档](https://docs.python.org/3/index.html)
[链接到示例中使用的图片](https://i.imgur.com/YtiyImV.jpg)