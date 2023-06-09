# 使用 Wia 和 Pycom 构建一个端到端的 Sigfox GPS 跟踪器

> 原文：<https://dev.to/wiaio/build-an-end-to-end-sigfox-gps-tracker-using-wia-and-pycom-2eni>

[![alt text](img/31c59cd1bd62341d9f4ad59f64c9521d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VVg7F-LT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526299660-249518-pycom-smartphone-illustration.png)

嗨，伙计们，

今天我将向您展示如何使用 Wia 和 Pycom SiPy 创建 Sigfox GPS 跟踪器。

本教程假设您已经将 Sigfox 连接到 Wia，如果您还没有，请点击[此处](https://developers.wia.io/v1.0/docs/pycom-sipy)查找 Sigfox 初始设置和向 Wia 发布数据的教程。

### **组件**

*   [Pycom SiPy](https://pycom.io/hardware/sipy-specs/)
*   [Pytrack 扩展板](https://pycom.io/product/expansion-board-3-0/)
*   Sigfox 兼容天线
*   移动设备(iOS 或 Android)

### **设置你的棋盘**

如果您还没有升级，您需要升级 Pytrack 扩展板上的固件。

你可以按照[这里]的说明去做。([https://docs . pycom . io/chapter/pytrackpysense/installation/firmware . html](https://docs.pycom.io/chapter/pytrackpysense/installation/firmware.html))

*   将 SiPy 连接到 Pytrack 板上。(SiPy 上的 RGB LED 应该在 Pytrack 板上 usb 端口的一侧)
*   将 Sigfox 天线连接到 SiPy。(连接就在 RGB LED 的左侧)

### **设置您的项目**

*   为您的项目创建新文件夹。我要把我的命名为 pycom-pytrack
*   在[原子](https://atom.io/)中，转到`File > New Window`打开一个新窗口
*   点击`File > Add Project Folder`并导航至新创建的文件夹，添加该文件夹
*   如果 Atom 窗口底部的 [Pymakr](https://atom.io/packages/pymakr) 插件没有打开，点击右边的箭头打开它
*   选择`Settings > Project Settings`。在地址字段中，将值替换为上一步中的设备名称，例如`/dev/tty.usbmodemPy343431` (Mac OS X)、`COM3` (Windows)、`/dev/ttyACM0` (Linux)，然后保存文件

### **添加所需库**

*   右键单击 Atom 中的文件夹名称，然后单击`Add Folder`。输入 lib 作为文件夹名
*   右键单击 lib 文件夹，然后单击新建文件。输入 urequests.py 作为文件名
*   点击该文件，然后将此处[的代码复制粘贴到该文件中，然后保存。](https://raw.githubusercontent.com/wiaio/wia-board-examples/master/Pycom/LoPy/PublishEventWifi/lib/urequests.py)
*   对于 Pytrack，额外的库也必须添加到 lib 文件夹，这些可以在[这里](https://github.com/pycom/pycom-libraries/tree/master/pytrack/lib)找到

### **发布位置事件**

*   我们的应用程序需要两个文件:
    *   `boot.py`设备加电时运行
    *   这是我们的主代码所在的地方
*   在 Atom 中，右键单击您的项目，然后单击`New File`。输入`boot.py`作为文件名
*   将下面的代码复制并粘贴到文件中

```
from machine import UART
from network import Sigfox
import binascii
import machine
import os

# initalise Sigfox for RCZ1 (Europe) (You may need a different RCZ Region)
sigfox = Sigfox(mode=Sigfox.SIGFOX, rcz=Sigfox.RCZ1)

# print Sigfox Device ID
print("ID: ", binascii.hexlify(sigfox.id()))

# print Sigfox PAC number
print("PAC: ", binascii.hexlify(sigfox.pac()))

uart = UART(0, baudrate=115200)
os.dupterm(uart)

machine.main('main.py') 
```

Enter fullscreen mode Exit fullscreen mode

*   右键单击您的项目，然后单击新建文件。输入`main.py`作为文件名
*   将下面的代码复制并粘贴到文件中

```
from network import Sigfox
from pytrack import Pytrack
import urequests as requests
from L76GNSS import L76GNSS
import socket
import time
import pycom
import struct

py = Pytrack()
gps = L76GNSS(py, timeout=60)

init_timer = time.time()

print("connecting to Sigfox")
# init Sigfox for RCZ1 (Europe)
sigfox = Sigfox(mode=Sigfox.SIGFOX, rcz=Sigfox.RCZ1)

# create a Sigfox socket
s = socket.socket(socket.AF_SIGFOX, socket.SOCK_RAW)
# make the socket blocking
s.setblocking(True)

s.setsockopt(socket.SOL_SIGFOX, socket.SO_RX, False)

# Post an location to the Wia cloud via Sigfox backend
def post_location(latitude, longitude):
    try:
        print(str(latitude), ":", str(longitude))
        s.send(struct.pack('f',float(latitude)) + struct.pack('f',float(longitude)))
    except:
        pass

# main loop
while True:
    final_timer = time.time()
    diff = final_timer - init_timer
    # Get coordinates from pytrack
    coord = gps.coordinates()

    # If the GPS has coordinates and 15 minites has past. Post the location data
    if not coord == (None, None) and diff < 900:
        lat, lng = coord
        post_location(lat, lng))
        init_timer = time.time() 
```

Enter fullscreen mode Exit fullscreen mode

您的文件夹结构现在应该如下所示:

`lib`

*   `urequests.py`
*   `L76GNSS.py`
*   `LIS2HH12.py`
*   `pytrack.py`
    *   `boot.py`
    *   `main.py`

在 Atom 窗口底部的 Pymakr 插件中点击`Upload`,将代码发送到您的 Pycom 板。现在转到 Wia 仪表板，您应该看到数据出现在仪表板的调试器部分。

注意:Pytrack 可能需要几分钟才能找到 GPS 信号，而且 Pytrack 不适合室内使用。

### **解析数据**

现在，下一步，我们必须解析从 Sigfox 收到的数据，并对其做一些有用的事情。为此，我们需要建立一个流程。

走到你的 Wia 仪表盘和你的 Sigfox 设备所在的地方。从那里点击左边菜单中的流程图标，进入您的流程。

[![alt text](img/faed0dc3550db250545b4eb8b9087be1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3KDo0l7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526284386-778692-flow.png)

现在来创建你的心流，你可以给它起任何你喜欢的名字。一旦你创建了一个流，你应该被带到流工作室。

在 Flow studio 中:

*   将`trigger event node`从左侧拖到画布上

*   点击节点并输入`sigfoxDataUplink`作为事件名称

*   启用 Sigfox 设备作为事件源

*   现在从逻辑部分添加一个函数节点(我们将在这里解析数据)

*   从`output`部分添加一个位置节点

点击功能节点，添加以下代码:

```
if (input.body) {
  let latLong = parseSigfoxLocation(input.body.data.sigfoxData);
  output.body.latitude = latLong.latitude;
  output.body.longitude = latLong.longitude;
}

function parseSigfoxLocation(sigfoxData) {
  let latHex = sigfoxData.slice(0, 8);
  let longHex = sigfoxData.slice(8);
  let result = {
    latitude: Buffer(latHex, 'hex').readFloatLE(0).toFixed(6),
    longitude: Buffer(longHex, 'hex').readFloatLE(0).toFixed(6)
  };

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将 Sigfox 数据从十六进制格式解析为浮点格式的经度和纬度。

*   Sigfox 数据是一个 12 字节的十六进制字符串
*   每个 GPS 坐标是 32 位或 4 字节的数据
*   它们是 2 个字符，可以放入 1 个字节的数据中，因此我们将十六进制字符串除以 8

### **发送推送通知**

目前，该流从 Sigfox 接收数据，解析它并输出一个位置。现在，我们将更进一步，获得新创建位置的通知，以便我们可以在手机上的地图上查看它。为此，您将需要 Wia 移动应用程序。你可以在 iOS [这里](https://itunes.apple.com/us/app/wia-talk-to-your-things/id1320616870?ls=1&mt=8)和安卓[这里](https://play.google.com/store/apps/details?id=io.wia.wia)下载。

[![alt text](img/7e418b00110afab6400a61a42294a07b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s2mLiswh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526284470-725433-flow-left.png)

在 Flow Studio 编辑器中:

*   现在从触发器部分拖动到位置节点上。这样，我们就可以捕获从前面的流链中创建的节点
*   将与前面相同的设备添加到位置触发节点
*   将鼠标拖到通知节点上，并输入以下文本

`GPS Coordinates: ${input.body.latitude}, ${input.body.longitude}`

[![alt text](img/83d43dc6a1b515404f05f4413ad855dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gSJeZ6DC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526299594-582357-selection-112.png)

现在，您应该可以在移动设备上接收 Sigfox 数据了。

[![alt text](img/6f3689ee74058d9489223e3b7af918d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hZcrsqNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-14/1526303292-200022-track-push.jpeg)