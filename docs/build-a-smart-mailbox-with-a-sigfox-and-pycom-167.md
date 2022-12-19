# 用 Sigfox 和 Pycom 构建一个智能邮箱

> 原文：<https://dev.to/wiaio/build-a-smart-mailbox-with-a-sigfox-and-pycom-167>

[![alt text](../Images/31dc5f48cf79431e4ae8ae052cb18960.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GbOofzvj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-07-13/1531496045-733399-smart-mailbox-sigfox-pycom.png)

嗨，伙计们，

今天我将向您展示如何使用 Wia 和 Pycom SiPy 创建 Sigfox 邮件通知程序。

本教程假设您已经将 Sigfox 连接到 Wia，如果您还没有，请点击[此处](https://developers.wia.io/v1.0/docs/pycom-sipy)查找 Sigfox 初始设置和向 Wia 发布数据的教程。

# 组件

*   [Pycom SiPy](https://pycom.io/hardware/sipy-specs/)
*   [Pycom 扩展板](https://pycom.io/product/expansion-board-3-0/)
*   Sigfox 兼容天线
*   移动设备(iOS 或 Android)
*   [HC-SR04 超声波传感器](https://www.amazon.co.uk/ELEGOO-Ultrasonic-Raspberry-Datasheet-Available/dp/B01M0QL1F1/ref=sr_1_3?ie=UTF8&qid=1531485159&sr=8-3&keywords=hc-sr04)

# 设置您的电路板

*   将 SiPy 连接到扩展板上。(Pycom 徽标应朝向同一方向)
*   将 Sigfox 天线连接到 SiPy。连接是 SiPy 板左下方的小金圈。只需将天线扣到位。[upl-image-preview URL =[https://S3-eu-west-1 . amazonaws . com/wia-flarum-bucket/2018-07-13/1531486321-146149-sipy-antenna . png](https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-07-13/1531486321-146149-sipy-antenna.png)]

对于 HC-SR04 超声波传感器，您需要将以下引脚连接到 Pycom SiPy:

*   HC-SR0 | Pycom
*   Vcc -> Vin
*   三角-> G8
*   回声-> G7
*   Gnd ->

# 设置您的项目

在 Atom 中:

*   为您的项目创建新文件夹。我要给我的 sigfox 邮箱打电话
*   在[原子](https://atom.io/)中，转到`File > New Window`打开一个新窗口

*   点击`File > Add Project Folder`并导航至新创建的文件夹，添加该文件夹

*   如果 Atom 窗口底部的 [Pymakr](https://atom.io/packages/pymakr) 插件没有打开，点击右边的箭头打开它

*   选择`Settings > Project Settings`。在地址字段中，将值替换为上一步中的设备名称，例如`/dev/tty.usbmodemPy343431` (Mac OS X)、`COM3` (Windows)、`/dev/ttyACM0` (Linux)，然后保存文件

# 为我们的邮件发布 sigfox 事件

我们的应用程序需要三个文件:

*   `boot.py`设备加电时运行
*   这是我们的主代码所在的地方
*   我们获取距离和校准的函数在哪里

在 Atom 中:

*   右键单击您的项目，然后单击新建文件。输入`boot.py`作为文件名

*   将下面的代码复制并粘贴到文件

```
from machine import UART
import machine
import os

uart = UART(0, baudrate=115200)
os.dupterm(uart)

machine.main('main.py') 
```

Enter fullscreen mode Exit fullscreen mode

*   右键单击您的项目，然后单击新建文件。输入`main.py`作为文件名

*   将下面的代码复制并粘贴到文件

```
import time
import pycom
import socket
from network import Sigfox
from machine import Pin, Timer
import ultrasonic

pycom.heartbeat(False)

echo = Pin(Pin.exp_board.G7, mode=Pin.IN)
trigger = Pin(Pin.exp_board.G8, mode=Pin.OUT)
trigger(0)

# Get the chronometer object
chrono = Timer.Chrono()

# init Sigfox for RCZ1 (Europe)
sigfox = Sigfox(mode=Sigfox.SIGFOX, rcz=Sigfox.RCZ1)
# create a Sigfox socket
s = socket.socket(socket.AF_SIGFOX, socket.SOCK_RAW)
# make the socket blocking
s.setblocking(True)
# configure it as uplink only
s.setsockopt(socket.SOL_SIGFOX, socket.SO_RX, False)

calibrated_distance = ultrasonic.calibration(chrono, trigger, echo, 1)
mailed_distance = 0
while True:
    time.sleep(30)
    distance = ultrasonic.getDistance(chrono, trigger, echo)
    print("distance: {}, calibration: {}".format(distance, calibrated_distance))
    if distance < calibrated_distance:
        if distance != mailed_distance:
            s.send('') # Send 1 bit
            print("you got mail")
            mailed_distance = ultrasonic.calibration(chrono, trigger, echo) 
```

Enter fullscreen mode Exit fullscreen mode

*   右键单击您的项目，然后单击新建文件。输入`ultrasonic.py`作为文件名

*   将下面的代码复制并粘贴到文件

```
from machine import Pin, Timer
import pycom
import time
import socket

def calibration(chrono, trigger, echo, led = False):
    if led:
        pycom.rgbled(0x7f0000) # red
    prev_distance = 0
    distance = getDistance(chrono, trigger, echo)
    print("calibration distance is {}".format(distance))
    count = 0
    while True:
        prev_distance = distance
        distance = getDistance(chrono, trigger, echo)
        while prev_distance == distance:
            count+=1
            print("count: {}".format(count))
            if count > 5:
                if led:
                    pycom.rgbled(0x007f00) # green
                    time.sleep(1.5)
                    pycom.rgbled(0) # off
                return distance
            time.sleep(5)
            prev_distance = distance
            distance = getDistance(chrono, trigger, echo)
        else:
            count = 0

def getDistance(chrono, trigger, echo):
    chrono.reset()
    trigger(1)
    time.sleep_us(10)
    trigger(0)

    while echo() == 0:
        pass
    chrono.start()

    while echo() == 1:
        pass
    chrono.stop()

    distance = chrono.read_us() / 58.0
    if distance > 400:
        return -1
    else:
        return int(distance)

    time.sleep(1) 
```

Enter fullscreen mode Exit fullscreen mode

您的文件夹结构现在应该如下所示:

*   `ultrasonic.py`

*   `boot.py`

*   `main.py`

在 Atom 窗口底部的 Pymakr 插件中点击`Upload`,将代码发送到您的 Pycom 板。现在转到 Wia 仪表板，您应该看到数据出现在仪表板的调试器部分。

为了让传感器正常工作，它需要校准你的邮箱内的距离。

### 校准

要校准传感器，请将传感器放在邮箱中最合适的位置。给董事会供电；当板上的 LED 为红色时，传感器正在校准；一旦 LED 闪烁绿色，传感器已被校准。传感器需要至少 2 厘米的空间才能正常工作。

现在，当邮件被插入您的邮箱时，距离将从校准后的距离减少，代码将通过 Sigfox 向 Wia 发布一个事件。

# 发送推送通知

现在，下一步，一旦我们在 Wia 中收到 Sigfox 事件，我们将向任何连接的电话发送一个有邮件的通知。为此，我们需要建立一个流程。

走到你的 Wia 仪表盘和你的 Sigfox 设备所在的地方。从那里点击左边菜单中的流程图标，进入您的流程。

[![alt text](../Images/faed0dc3550db250545b4eb8b9087be1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3KDo0l7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526284386-778692-flow.png)

现在来创建你的心流，你可以给它起任何你喜欢的名字。一旦你创建了一个流，你应该被带到流工作室。

在 Flow studio 中:

*   将`trigger event node`从左侧拖到画布上

*   点击节点并输入`sigfoxDataUplink`作为事件名称

*   启用 Sigfox 设备作为事件源

现在，我们将为 Sigfox 事件添加一个通知，这样我们就可以收到关于入站邮件的通知。为此，您将需要 Wia 移动应用程序。你可以在 iOS [这里](https://itunes.apple.com/us/app/wia-talk-to-your-things/id1320616870?ls=1&mt=8)和安卓[这里](https://play.google.com/store/apps/details?id=io.wia.wia)下载。

[![alt text](../Images/5d5c5bdc789f8c37173da6ce593f3db0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v9ZXesjI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-07-13/1531485931-970630-mail-notifier.png)

在 Flow Studio 编辑器中:

*   将鼠标拖到通知节点上，并输入以下文本

```
You've got Mail! 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该可以在移动设备上接收 Sigfox 数据了。

[![alt text](../Images/dce72db5f781ef1ceef36d173726b714.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XzGOEEnh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-07-13/1531490491-960647-screenshot-20180713-150002-2.jpeg)