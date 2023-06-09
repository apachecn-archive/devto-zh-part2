# 使用 Python 设置 Pycom FiPy 并将事件发布到 Wia

> 原文：<https://dev.to/wiaio/setup-a-pycom-fipy-and-publish-an-event-to-wia-using-python-23nn>

[![Pycom FiPy](img/324336d43fd032aa3d46d1710e2a4f80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nm9IgCO_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/e8473b6-fipyTop_copy.png)

### **设置您的开发环境**

我们将使用 Atom 作为我们的开发环境。你可以从[这里](https://atom.io/)下载最新版本。

一旦你设置好了，安装 [Pymakr](https://atom.io/packages/pymakr) 插件。按照[本教程](https://docs.pycom.io/chapter/gettingstarted/installation/pymakr.html)中的步骤进行设置。

### **连接板卡**

有两种方法可以将您的主板连接到您的计算机，通过 USB 或串行连接。

#### (选项 1)通过 USB

你可以通过扩展板上的 USB 端口、Pysense 或 Pytrack 板(更多信息请点击[这里](https://docs.pycom.io/chapter/gettingstarted/))来实现。

#### (选项 2)通过串行(USB 转 TTL)

要通过 USB 转 TTL 电缆与电路板通信，请连接以下引脚:

FiPyUSB TTL
GND<->GND
TX0<->RX
RX0<->TX
3.3V<->VCC

[![Pycom WiPy Pinout Diagram](img/8eb16d48f26d717a94b7e8b4cc3d6591.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lfOuGU8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/e157bd7-wipy_v01_0_pinout_rc1.png)

### **获取设备名称**

将设备连接到电脑后，使用以下步骤之一获取设备的名称:

#### Linux 和 Mac OS X

*   从[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)下载并安装 FTDI 驱动。为您的操作系统和体系结构选择合适的版本。
*   打开终端窗口并运行命令`ls /dev/tty*`
*   查找名称以`/dev/tty`开头的设备，例如 MAC 上的`/dev/tty.usbmodemPy343431`或 Linux 上的`/dev/ttyUSB0` / `dev/ttyACM0`。

**注意:**对于 Linux，您可能需要运行下面的两个命令。一旦你完成了，重启你的电脑。这将添加允许您将草图上传到板上的权限。
T0
T1】

#### 窗口

*   从这里下载并安装 FTDI 驱动程序。为您的操作系统和体系结构选择合适的版本。
*   打开 Windows 开始菜单，搜索`Device Manager`
*   Pycom 设备的 COM 端口将被列为`USB Serial Device`或类似的名称
    *   记下 COM 端口(例如 COM4)

#### 使用 Windows 7？

Pytrack 和 Pysense 开箱即可用于 Windows 8/10/+、Mac OS 以及 Linux。如果使用 Windows 7，需要安装支持主板的驱动程序。你可以在这里找到它们。

### **设置您的项目**

*   为您的项目创建新文件夹。姑且称之为`wia-publish-pycom-event`。记住你把它放在哪里了。
*   在 Atom 中，转到`File > New Window`打开一个新窗口。
*   点击`File > Add Project Folder`并导航至之前创建的文件夹，添加新创建的文件夹。

[![alt text](img/dc71f63943ebd292c49c2338337fc7a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5ecNF9yF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/451f586-Screen_Shot_2018-03-27_at_16.48.09.png)

#### 看不到 Atom 中的树形视图窗格？

在 Atom 的顶部菜单中，点击`View > Toggle Tree View`。这应该使它出现。

*   如果 Pymakr 插件没有在 Atom 窗口的底部打开，单击右边的箭头打开它。
*   选择`Settings > Project Settings`。在`address`字段中，用上述步骤中的设备名称替换该值，例如`/dev/tty.usbmodemPy343431` (Linux/Mac OS X)、`COM4` (Windows)，然后保存文件。

### **添加请求库**

*   右键单击 Atom 中的文件夹名称，然后单击 Add Folder。输入`lib`作为文件夹名称。
*   右键点击`lib`文件夹，点击新建文件。输入`urequests.py`作为文件名。
*   点击该文件，然后将此处[的代码复制粘贴到该文件中，然后保存。](https://raw.githubusercontent.com/wiaio/wia-board-examples/master/Pycom/LoPy/PublishEventWifi/lib/urequests.py)

### **发布事件**

在 Atom 中，右键单击您的项目，然后单击 New File。输入`boot.py`作为文件名。
将下面的代码复制并粘贴到文件中。查看 GitHub 上的这里的[。](https://github.com/wiaio/wia-board-examples/tree/master/Pycom/LoPy/PublishEventWifi)

**boot . py**T2】

```
from machine import UART
import machine
import os

uart = UART(0, baudrate=115200)
os.dupterm(uart)

machine.main('main.py') 
```

Enter fullscreen mode Exit fullscreen mode

*   右键单击您的项目，然后单击新建文件。输入 main.py 作为文件名。
*   将下面的代码复制并粘贴到文件中。查看 GitHub 上的这里的[。](https://github.com/wiaio/wia-board-examples/tree/master/Pycom/LoPy/PublishEventWifi)

**main . py**T2】

```
from network import WLAN
import urequests as requests
import machine
import time
import pycom
import gc
import micropython

# Garbage collection to save memory
gc.enable()

# Your WiFi network credentials
WIFI_SSID = 'your-wifi-ssid'
WIFI_KEY = 'your-wifi-key'

# Get this from the Wia dashboard
DEVICE_SECRET_KEY = 'your-device-secret-key'

# Delay between each event
DELAY = 3

wlan = WLAN(mode=WLAN.STA)
nets = wlan.scan()

# Connect to the WiFi network
for net in nets:
    if net.ssid == WIFI_SSID:
        print('Network found!')
        wlan.connect(net.ssid, auth=(net.sec, WIFI_KEY), timeout=5000)
        print('Connecting...')
        while not wlan.isconnected():
             machine.idle() # save power while waiting
        print('WLAN connection succeeded!')
        break

# Post an Event to the Wia cloud
def post_event(json):
    try:
        if json is not None:
            req = requests.post(url=url, headers=headers, json=json_data)
            if req.status_code is not 200:
                machine.reset()
            else:
                print("posting", json, "to Wia")
            return req.json()
        else:
            pass
    except:
        pass

# Data
temperature = 24.6
name = "temperature"
json_data = {"name": name, "data": temperature}

# Run this loop continuously
while True:
    gc.collect()
    #micropython.mem_info()
    post_event(json_data)
    if not wlan.isconnected():
        wlan.connect(net.ssid, auth=(net.sec, WIFI_KEY), timeout=5000)
        print("reconnecting...")
        while not wlan.isconnected():
             machine.idle()
    time.sleep(DELAY) 
```

Enter fullscreen mode Exit fullscreen mode

替换下列变量的下列值:

*   `WIFI_SSID`使用您的 WiFi 网络名称。
*   `WIFI_KEY`用你的 WiFi 网络密码。
*   `DEVICE_SECRET_KEY`使用您的设备密钥。您可以在您的设备配置页面中找到它(见下面的截图)。

[![Wia dashboard device configuration page](img/6075fbc97e628ccff6f5defba5f6e8f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4tXxk5JJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/4e21733-Screen_Shot_2018-03-27_at_16.56.11.png)

您的文件夹结构现在应该如下所示:

*   `lib`
    *   `urequests.py`
*   `boot.py`
*   `main.py`

[![Project folder structure](img/55c6b1f9309b23c9f6f20aec0f501219.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JNBa19IK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/fdcc56d-Screen_Shot_2018-03-27_at_16.53.15.png)

在 Atom 窗口底部的 Pymakr 插件中点击`Upload`,将代码发送到您的 Pycom 板。

[![Publish an event code running on Pycom board](img/b429f6e84ca4b04d99507751fcd7e5e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LBdTCZi_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/4c15ed2-Screenshot_from_2018-03-26_15-37-01.png)

**注意:如果你得到“上传项目(主文件夹)...错误**

*   尝试更新固件。对于开发板，点击[此处](https://docs.pycom.io/chapter/gettingstarted/installation/firmwaretool.html#first)。
*   有关 Pysense/Pytrack 板的其他固件，请单击[此处](https://docs.pycom.io/chapter/gettingstarted/installation/firmwaretool.html#first)。

现在转到 Wia 仪表板中的设备，您应该看到数据出现在调试器中。

[![Wia Device Debugger](img/0a8597b6b9981c3c552442fcdba46325.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fJNmUoYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/4fed030-Debugger_Wia.png)