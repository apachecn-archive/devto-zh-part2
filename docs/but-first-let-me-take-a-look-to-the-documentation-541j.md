# 但是首先让我看一下文档

> 原文：<https://dev.to/lmbarr/but-first-let-me-take-a-look-to-the-documentation-541j>

这是一个关于如何(不)浪费 2 周工作的恐怖故事。

首先让我给你一点背景。

我在做一个项目，客户要求创建一个包含某个过程图像的报告。我的技术负责人让我负责生成图像，当时我选择了 pyopengl(因为 python 已经被用作项目中的编程语言)来实现 2D 和 3D 图像的生成，主要是因为已经为 Threejs 中的正交相机计算了值，我将在 pyopengl 相机中使用这些值。

捕捉图像的过程如下，首先创建一个 pygame 窗口，然后在该窗口上绘制，然后制作一个快照。

到目前为止，在我的本地计算机上运行良好，但在服务器上尝试时却不工作(正如我所怀疑的那样，因为服务器没有一个图形界面，允许您创建一个窗口来捕获并保存该窗口)。

噩梦从这里开始，我们寻找了一个图形库，允许无头渲染或屏幕外渲染大约 2 周。首先，我们尝试了(我和 Daniel，在项目中合作的实习生)open3d python 包，这是一个彻底的灾难，因为它不允许像任何图形库一样正常设置相机(或者可能是我的错，我没有找到方法)，我们需要一个允许设置相机的查找向量、位置向量和上方向向量的相机。

然后，我不知道为什么，我决定阅读 pyopengl 文档，并说它允许使用 osmesa 库进行无头渲染(虽然它不是很明确)，等等，这是一个 facepalm 时刻结合一个 blow mind 时刻。

读完这篇文章后，我想丹尼尔和我花了一个小时来实现这个想法并使之适应我们的代码。

因此，我将以这个结尾:*阅读您已经在使用的技术的文档。*