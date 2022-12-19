# Haptik 如何使用 Python 动态生成图像

> 原文：<https://dev.to/vinayjn/how-haptik-generates-images-on-the-fly-with-python--1pnl>

**计算机图形学**教我们如何操作屏幕上的像素来绘制美丽的形状、艺术的字体、引人注目的插图、**“让我看起来更好”**照片滤镜等等。硬件制造商、研究人员、软件开发人员共同努力打造伟大的产品:智能手机、智能手表、智能电视、相机，所有这些都与计算机图形的研究息息相关。

尽管计算机图形发展如此之快，并且像 Adobe Photoshop、Adobe Illustrator、Sketch 这样的软件的发展在很大程度上使我们的生活变得更容易，但我们仍然不能用它们即时生成图像。为了做到这一点，我们需要达到一个没有拖放、没有花哨的全选加粗键盘快捷键、没有裁剪和复制粘贴的水平。

> 我们不能通过时间旅行到达那里，但肯定有代码！

### 入门

来吧，打开你最喜欢的文本编辑器，跟着我，我将帮助你在图像上绘制动态文本数据。我假设您的计算机上已经安装了 **[Python](https://www.python.org/downloads/)** 和 **[pip](https://pypi.python.org/pypi/pip)** ，但是如果没有，请按照链接中的步骤来设置开发环境。完成设置后，在 shell 中执行下面的命令来安装 **Pillow** (更多细节[在这里](https://pypi.python.org/pypi/Pillow))及其依赖项。

```
pip install pillow 
```

Enter fullscreen mode Exit fullscreen mode

既然您已经安装了所有的依赖项，让我们继续写一些代码。Pillow 是一个扩展的库，但是出于我们的目的，我们将使用下面的类:

*   [Image](http://pillow.readthedocs.io/en/3.1.x/reference/Image.html) :为我们的问候背景创建一个图像对象
*   [ImageDraw](http://pillow.readthedocs.io/en/3.1.x/reference/ImageDraw.html) :创建一个绘图上下文
*   [ImageFont](http://pillow.readthedocs.io/en/3.1.x/reference/ImageFont.html#PIL.ImageFont.truetype) :我们将在问候语上绘制的文本的字体

让我们取下面的背景图片，并用下面的代码初始化它:

[![Background](../Images/1b3b0ba8fbc93ca0a950013d8bfc326d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S7-3QoJs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/haptik.ai/tech/wp-content/uploads/2018/01/background.png)T3】

```
# import required classes 
from PIL import Image, ImageDraw, ImageFont

# create an Image object with the input image 
image = Image.open('background.png')

# initialize the drawing context with
# the image object as background 
draw = ImageDraw.Draw(image) 
```

Enter fullscreen mode Exit fullscreen mode

为了创建 ImageFont 对象，我们还需要字体(ttf，otf)文件，你可以使用你选择的任何字体，这里我将使用 [Roboto](https://fonts.google.com/specimen/Roboto) 字体，它可以从 [Google Fonts GitHub](https://github.com/google/fonts/tree/master/apache/roboto) repo 下载。

```
 # create font object with the font file and specify
# desired size 
font = ImageFont.truetype('Roboto-Bold.ttf', size=45)

# starting position of the message 
(x, y) = (50, 50)
message = "Happy Birthday!"
color = 'rgb(0, 0, 0)' # black color 
# draw the message on the background 
draw.text((x, y), message, fill=color, font=font)
(x, y) = (150, 150)
name = 'Vinay'
color = 'rgb(255, 255, 255)' # white color draw.text((x, y), name, fill=color, font=font)

# save the edited image 
image.save('greeting_card.png') 
```

Enter fullscreen mode Exit fullscreen mode

下面是执行上述代码后得到的结果:

[![Greeting](../Images/7333beb95a60c7646ca352e35cedc2f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--97FK3h0t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/haptik.ai/tech/wp-content/uploads/2018/01/greeting_card.png)

对于某些字体，您可能需要传递一个可选参数 **encoding** ，它告诉 ImageFont 模块在打开字体文件时使用哪种编码。

计算机图形有一个倒置的坐标系，**原点(0，0)** 位于图像的左上角。这里的 x 代表文本框离左边的距离 **(x=0)** ， *y* 代表离上面的距离 **(y=0)** 。

当你保存图像时，你可以通过可选参数如**优化**和**质量**来控制输出图像的大小。

```
image.save('optimized.png', optimize=True, quality=20) 
```

Enter fullscreen mode Exit fullscreen mode

这生成了质量降低但尺寸更小的输出图像**optimized.png**。

### 我们在哪里用 Python 做枕头？

在工作期间，我最近开发了一个功能，要求即时创建一个排行榜图像**和用户特定的测验分数数据。仅仅用几行代码，我就能创造出这样的图像:**

[![Haptik Weekly Quiz Leaderboard](../Images/3145f98c2e97d7bd1abaa7d787e42e60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L7LjTT9t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/haptik.ai/tech/wp-content/uploads/2018/01/image_1.png)

瞧啊。它看起来很棒，我们决定将在旅途中创建图像的想法用于其他用例。我们目前使用 Pillow 来生成笑话、励志名言、星座运势、每日一词等图像。实时地使用来自不同 API 响应的数据。

[![Haptik Motivational Quote & Word of the Day](../Images/792ff11c8d5ee3576b6dc7d92143f7c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Th_m1xu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/haptik.ai/tech/wp-content/uploads/2018/01/image_2.png)

我们在这篇文章中使用的代码不足以绘制如上图所示的文本框。我将写**另一篇文章**,重点是文本对齐，将长文本分成多行，控制两行之间的间距等等。

我最近读了 [@ben](https://dev.to/ben) 的[帖子](https://dev.to/ben/how-devto-dynamically-generates-social-images--2c2n)，他写了关于 [Dev.to](https://dev.to) 如何动态生成社交图像。我意识到我在工作中也做过类似的事情，为什么不与社区分享呢？

请在下面的评论区给我你的反馈。

这篇文章最初是为 Haptik 科技博客撰写的。