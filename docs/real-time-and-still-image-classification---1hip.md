# 实时和静止图像分类

> 原文：<https://dev.to/rosejcday/real-time-and-still-image-classification---1hip>

在过去的几个月里，我一直在利用英特尔 Movidius 神经计算棒(NCS)进行一些快速原型开发，利用深度神经网络在静态图像和实时视频流中进行人物检测。为了使用这个设备，我用 Ubuntu (64 位)16.04 桌面和 USB 摄像头设置了一个英特尔 Nuc。因此，我想分享我在这个过程中学到的一些东西...

NCS 由英特尔 m ovidius Myriad 2 视觉处理单元(VPU)提供支持，包括 12 个 VLIW 矢量处理器阵列，称为 SHAVE 处理器，通过并行运行网络的各个部分来加速神经网络。一旦连接到主机，神经计算 API (NCAPI)用于初始化和打开 NCS 设备，将固件加载到设备，并接受神经网络图文件和指令来执行推理。下面详细介绍了这些步骤:

#### NCS 设备设置

(1)连接到主机

(2)初始化并打开 NCS 设备
这组代码是枚举设备的地方，如果没有找到设备，Python 代码将退出。这就是为什么在第一步中，设备必须连接。一旦连接并找到，该设备可以给出返回的用于打开的代码的句柄。

```
# Look for enumerated NCS device(s); quit program if none found.
devices = mvnc.EnumerateDevices()
if len( devices ) == 0:
    print( "No devices found" )
    quit()

# Get a handle to the first enumerated device and open it
    device = mvnc.Device( devices[0] )
    device.OpenDevice()

return device 
```

Enter fullscreen mode Exit fullscreen mode

(3)首次启动时将固件加载到设备中

(4)接受神经网络图形文件
这一步是将图形文件从 DNN 模型加载到 NCS 设备中。这个例子中使用的图形是../caffe/SSD_MobileNet/graph '以 75%的置信度检测使用 15 级的人。该图在被返回之前被加载到缓冲器中，然后被加载到 NCS 中。

```
# Load a graph file onto the NCS device
# Parameters: self and enumerated device 
# Return: graph file for NCS 
def load_graph(self, device):

    # Read the graph file into a buffer
    with open( self.ARGS.graph, mode='rb' ) as f:
        blob = f.read()

        # Load the graph buffer into the NCS
        graph = device.AllocateGraph( blob )

return graph 
```

Enter fullscreen mode Exit fullscreen mode

现在图已经在 NCS 中了，可以实现在图上执行的指令了。

#### 执行推论的指令

(5)预处理图像
在进行推理之前，首先对图像进行预处理。这分三步完成:调整图像大小，将图像 RGB 转换为 OpenCV 的 BGR，使用均值减法和缩放将数据居中。一旦完成，图像可以被返回并用于推断。

(6)推断
一旦完成上述所有步骤，就可以放置重叠的结合框，并且如果图像属于特定类别(15:人)，就可以打印检测类别和分数。

#### 使实时图像处理适应静止图像

在完成这个整体过程之后，首先是实时图像处理，我重新评估了代码，以确定需要做出哪些更改来评估我正在处理的项目的静态图像。我注意到的第一件事是把原来的一个论点从视频改成了图像:

```
# Video 
parser.add_argument( '-v', '--video', type=int,
    default=0,
    help="Index of your computer's V4L2 video device. \ ex. 0 for /dev/video0" )

# Image 
parser.add_argument( '-i', '--image', type=str,
    default='img/*.jpg',
    help="Image path" ) 
```

Enter fullscreen mode Exit fullscreen mode

这一改变允许从图像文件夹中读入图像。这样做之后，需要对代码进行一些更改，以适应从相机流到静态图像的转换。其中一个主要部分是研究 NCS 设备如何接收图像。我利用 glob 从 images 路径读入图像，并遍历该文件夹中的所有图像。这种迭代允许用上面的代码处理和推断每个图像。

```
images = glob.glob('img/*.jpg')
for image in images:
    frame = cv2.imread(image) 
    img = cam.pre_process_image(frame)
    cam.infer_image(graph, img, frame) 
```

Enter fullscreen mode Exit fullscreen mode

学习如何在实时流和静态图像之间转换是一次有用的学习经历，这有助于我正在进行的一个项目，在这个项目中，我需要根据图像是否包含人来清理一个大型图像数据集。

### 参考文献

[英特尔针对 Python](https://software.intel.com/en-us/articles/intel-optimized-packages-for-the-intel-distribution-for-python)
[的英特尔分发优化包【OpenVino](https://software.intel.com/en-us/openvino-toolkit/deep-learning-cv)
[英特尔 Movidius NCS](https://movidius.github.io/ncsdk/ncs.html)
[实时人物检测 Repo](https://github.com/rosejcday/camera_person_detect)
[Caffe MobileNet-SSD](https://github.com/chuanqi305/MobileNet-SSD)
封面图片来源于[壁纸洞穴](https://wallpapercave.com/wp/6NKuNA3.jpg)