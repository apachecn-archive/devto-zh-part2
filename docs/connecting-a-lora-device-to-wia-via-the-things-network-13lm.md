# 通过物联网将 LoRa 设备连接到 Wia

> 原文：<https://dev.to/wiaio/connecting-a-lora-device-to-wia-via-the-things-network-13lm>

[![alt text](img/8d94cb95142ed7f3923031b10fb2ca89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GuEs53nn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530016343-835236-blog-02.png)

在本教程中，我们将使用两个 [Pycom LoPy 的](https://docs.pycom.io/chapter/datasheets/development/lopy.html)，将第一个设置为 LoRaWAN 网关，并将其注册为与[物联网](https://www.thethingsnetwork.org)的网关。然后在第二次 LoPy 中，我们设置它与我们的 LoRaWAN 网关对话，并让[物联网](https://www.thethingsnetwork.org)将事件推送到 Wia。

# 组件

*   2 x [Pycom LoPy](https://pycom.io/product/lopy4/)
*   2 x [LoRa 兼容天线](https://pycom.io/product/lora-antenna-kit/)

# 先决条件

如果您对 Pycom LoPy 和 Wia 完全不熟悉，您应该尝试我们的入门教程，了解如何通过 WiFi 连接 LoPy 并在此向 Wia [发布事件。](https://developers.wia.io/v1.0/docs/pycom-lopy)

# 洛拉万纳米网关的基本设置

我们将使用 Pycom 的 LoRaWAN Nano [网关](https://docs.pycom.io/chapter/tutorials/lora/lorawan-otaa.html)示例作为我们的基本 LoRa 网关。

*   在 Atom 中创建一个新文件夹，命名为`lorawan-nano-gateway`这是我们的 Nano Gateway 的主文件夹
*   转到 Pycom 库示例中的`lorawan-nano-gateway`文件夹，这个文件可以在[这里找到](https://github.com/pycom/pycom-libraries/tree/master/examples/lorawan-nano-gateway)
*   将`main.py`、`config.py`和`nanogateway.py`复制到我们在 Atom 的主文件夹中
*   将下面的代码复制到名为`boot.py`的文件中

代码在 Pycom 板启动时运行，并在板连接到电源时自动启动 Nano 网关。

```
from machine import UART
import machine
import os

uart = UART(0, baudrate=115200)
os.dupterm(uart)

machine.main('main.py') 
```

Enter fullscreen mode Exit fullscreen mode

现在您的`LoRaWAN Nano Gateway`的文件夹结构应该如下所示:

*   main.py
*   boot.py
*   config.py
*   nanogateway.py

[![alt text](img/fad4d7faf025b5dc90a659c1962a5cb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9FO3gPaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530009786-273166-selection-166.png)

在 Atom 窗口底部的 Pymakr 插件中点击`Upload`,将代码发送到您的 Pycom 板。

[![alt text](img/71ba4f05a0464d0a00d83dc820df19c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j7s-nfzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530009143-504206-nanogatewaypushack.png)

*   网关 ID 将在以后向物联网注册网关时使用

### 向物联网注册(TTN)

要设置物联网网关，您需要向他们注册/创建一个帐户。他们注册页面的链接是[这里](https://account.thethingsnetwork.org/register)。注册后，您将被带到 TTN 控制台，在那里注册网关和应用程序，监控设备活动并设置上行链路以将数据推送到其他服务。

### 向物联网注册网关(TTN)

在 TTN 控制台中，有两个选项，应用程序和网关。选择网关，然后单击注册网关。这将允许新网关的设置和注册。

[![alt text](img/84a8651ce9d1929d531a4ad61e394612.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F9wGrvgI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530003082-936235-ttn-register-gateway.png)

*   必须勾选`I'm using the legacy packet forwarder`的勾选框
*   `Gateway EUI`是我们之前运行 Nano Gateway 时的数据
*   `Description`可以是您希望网关用于的用途
*   `Frequency Plan`代表您所在的地区，即您所在的地理区域
*   应该选择的地方也应该离你的区域最近
*   输入完所有字段后，点击`Register Gateway`

在注册你的网关时，你应该准备好你的网关与 TTN 通信所需的一切。

[![alt text](img/a29c62410bc385d4bceee9393ceb74c5.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Fl7LGWw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530003100-353257-gateway-overview.png)

### 使用物联网配置您的 LoRaWAN nano 网关(TTN)

现在，您的网关已经完全注册到 TTN，您应该在 atom 终端中看到推和拉确认。

[![alt text](img/e8c373b95d813a4384d7cbfaba0c59a9.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--pBtlRZFn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530003137-632843-nanogatwayatompushpull.png)

# 设置物联网应用(TTN)

应用程序用于对具有相似功能的设备集合进行分组。
在 TTN 控制台中，点击`applications`选项卡，然后点击`add application`您的应用程序将需要:

*   一个你自己随机生成的独一无二的`Application ID`
*   你的申请
*   对于`Handler Registration`，添加最适合您所在地理区域的处理程序

点击`Add application`向 TTN 添加应用程序。

# 在物联网中向您的应用添加设备(TTN)

在 TTN 控制台中，单击“Applications”选项卡，然后单击我们之前创建的应用程序。

在“应用概述”选项卡中:

*   点击设备部分的`register device`
*   添加唯一的`Device ID`(设备 ID 只能由小写字母数字字符、不连续的-和 _ 组成，并且不能以-或 _ 开头或结尾)
*   点击铅笔图标为`Device EUI`
*   点击`Register`按钮添加设备

[![alt text](img/93647a4cecff2515c73c0a3e58ef0205.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nt4r5voE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530003189-741093-register-device.png)

# 激活 Wia 中的物联网(TTN)集成

在您的 Wia 仪表板中，单击您想要添加的共享空间，然后单击左侧菜单栏上的设置图标，以查看您的共享空间的设置页面。

[![alt text](img/2ee837f9aa6a98dc06a1911d30240fa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9QEuJLcm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530004257-935013-space-settings.png) 
现在点击`Integrations`选项卡查看可用集成列表:

*   找到`The Things Network`集成并点击添加它
*   将为生成一个集成密钥，并应以`ik_`开始

[![alt text](img/4f0d572344ad628e3879af0647520c21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dWKMmA17--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530004533-550880-integration-added.png) 
回到您的 TTN 控制台，在您刚刚创建的应用程序中，单击集成选项卡:

*   点击`Get started by creating a new one`查看可用的集成
*   点击`HTTP Integration`
*   添加随机`Process ID`
*   单击您的应用程序中先前生成的`Access Key`
*   输入`https://integrations.wia.io/api/ttn/uplink`作为 url
*   选择`POST`作为 HTTP 方法
*   添加`Bearer ik_your_integration_key`，用您的 Wia 空间中生成的集成替换`ik_your_integration_key`
*   `Custom Header Name`和`Custom Header Value`不需要添加任何内容
*   点击“保存”, TTN 会将所有活动数据从您的 LoRa 设备推送到 Wia

[![alt text](img/7ad11a266c81d0325d460e32de775fe1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ykHC7Ip8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530007857-617583-integration.png)

现在，设置好所有需要的东西，将我们的 LoRa 设备连接到物联网，并将我们的活动发布到 Wia。现在我们需要设置一个 LoRa 客户端节点。我们需要第二次机会来完成这项任务。

# 空中激活 LoRa 节点的设置

在 Atom 中，创建一个新文件夹，并将其命名为`otaa_lora`。在该文件夹中添加来自`LoRaWAN nano Gateway`的配置文件，并将其复制到文件夹中。

创建一个 main.py 文件，并将下面的代码复制到其中。

```
""" OTAA Node example compatible with the LoPy Nano Gateway """

from network import LoRa
import socket
import binascii
import struct
import time
import config
import random

# Initialize LoRa in LORAWAN mode.
# Please pick the region that matches where you are using the device:
# Asia = LoRa.AS923
# Australia = LoRa.AU915
# Europe = LoRa.EU868
# United States = LoRa.US915
lora = LoRa(mode=LoRa.LORAWAN, region=LoRa.EU868)

# create an OTA authentication params
dev_eui = binascii.unhexlify('your-ttn-dev-eui')
app_eui = binascii.unhexlify('your-ttn-app-eui')
app_key = binascii.unhexlify('your-ttn-app-key')

# set the 3 default channels to the same frequency (must be before sending the OTAA join request)
lora.add_channel(0, frequency=config.LORA_FREQUENCY, dr_min=0, dr_max=5)
lora.add_channel(1, frequency=config.LORA_FREQUENCY, dr_min=0, dr_max=5)
lora.add_channel(2, frequency=config.LORA_FREQUENCY, dr_min=0, dr_max=5)

# join a network using OTAA
lora.join(activation=LoRa.OTAA, auth=(dev_eui, app_eui, app_key), timeout=0, dr=config.LORA_NODE_DR)

# wait until the module has joined the network
while not lora.has_joined():
    time.sleep(2.5)
    print('Not joined yet...')

# remove all the non-default channels
for i in range(3, 16):
    lora.remove_channel(i)

# create a LoRa socket
s = socket.socket(socket.AF_LORA, socket.SOCK_RAW)

# set the LoRaWAN data rate
s.setsockopt(socket.SOL_LORA, socket.SO_DR, config.LORA_NODE_DR)

# make the socket blocking
s.setblocking(False)

time.sleep(5.0)

temperature = random.randint(0, 40)

for i in range (200):
    pkt = b'{}'.format(temperature)
    print('Sending:', pkt)
    s.send(pkt)
    time.sleep(4)
    rx, port = s.recvfrom(256)
    if rx:
        print('Received: {}, on port: {}'.format(rx, port))
    time.sleep(20) 
```

Enter fullscreen mode Exit fullscreen mode

您需要将之前从 TTN 获得的以下密钥添加到教程代码中:

*   设备 eui(设备 EUI)
*   app_eui(应用 eui)
*   app_key(访问键)

应将`LORAWAN mode`设置为与您的区域和您为`LoRaWAN nano Gateway`设置的区域相匹配的区域。

将 boot.py 文件添加到中，并将下面的代码复制到文件中:

```
from machine import UART
import machine
import os

uart = UART(0, baudrate=115200)
os.dupterm(uart)

machine.main('main.py') 
```

Enter fullscreen mode Exit fullscreen mode

现在您的文件夹:`otaa_lora`应该包含:

*   main.py
*   boot.py
*   config.py

注:如果您的`LoRaWAN nano Gateway`和`otaa LoRa Node`
都在运行 USB 端口，您需要为其中一个项目设置项目级设置，并将`address`更改为第二个设备连接的端口。点击 Pymakr 插件上的`Settings`，点击项目设置添加一个`pymakr.conf`。这是您为第二台设备添加端口的地方。

在 Atom 窗口底部的 Pymakr 插件中点击`Upload`,将代码发送到您的 Pycom 板。

[![alt text](img/953e9516fd288266f747b01296747a82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C8qtPsb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530005624-627540-client-data-push.png)

在您的 Wia 仪表板中，点击您的 LoRa 设备并切换到`Debugger`选项卡，以实时查看您的 LoRa 设备发布的事件。要查看全部内容，请点击`Events`选项卡。

[![alt text](img/b12d437758e3eacb11b855c58a992020.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uowEeeS3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530010124-591193-image-2.png)

# 在 Wia 中处理您的 LoRa 数据

现在，下一步，我们必须解析从物联网收到的数据，并利用它做一些有用的事情。为此，我们需要建立一个流程。

前往您的 Wia 仪表板，在放置您的 LoRa 设备的地方。从那里点击左边菜单中的流程图标，进入您的流程。

现在来创建你的心流，你可以给它起任何你喜欢的名字。一旦你创建了一个流，你应该被带到流工作室。

在 Flow studio 中:

*   将触发器`Event`节点从左侧拖到画布上

*   点击节点并输入`ttnUplink`作为事件名称

*   启用您的 LoRa 设备作为事件源

[upl-image-preview URL =[https://S3-eu-west-1 . amazonaws . com/wia-flarum-bucket/2018-06-26/1530006282-733766-lora-flow-event . png](https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-26/1530006282-733766-lora-flow-event.png)]

现在从逻辑部分拖动一个功能节点(我们将在这里解析数据)

点击功能节点，添加以下代码:

```
function parseLoRaEvent(loraData) {
  return Buffer.from(loraData, 'base64');
}

if (input.body) {
  output.body.data = parseLoRaEvent(input.body.data.payload_raw);
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将 LoRa 数据从`base64`格式解析为 ascii。

开始发布数据，您的流应该会触发。

就是这样！