# 使用您的 SODAQ SARA N211 & NB-IoT 向 Wia 发布活动

> 原文：<https://dev.to/wiaio/publish-an-event-to-wia-using-your-sodaq-sara-n211--nb-iot-79e>

[![alt text](../Images/13f9cdc7f633580f4abc5bcd9eef8fc6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9ds-t4t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackster.imgix.net/uploads/attachments/488416/nb-iot-press-release_TyfUxnrY3N.png%3Fauto%3Dcompress%252Cformat%26w%3D900%26h%3D675%26fit%3Dmin)

在本教程中，我们将介绍设置 NB-IoT 并向 Wia 发布事件的初始步骤。

成分

*   索达克萨拉 N211
*   [M0 克鲁杜伊诺](https://www.elecrow.com/crowduino-m0-sd-p-1649.html)
*   [NB-IoT 兼容天线](https://shop.sodaq.com/en/gprs-pcb-antenna.html)
*   NB-IoT 兼容 SIM 卡(请向您的网络运营商咨询)

### **安装 Python 和 Pyserial**

如果你还没有，你需要在你的电脑上安装 Python 来完成这个教程。

*   [Python](https://www.python.org/downloads/)
*   2.7.x 版
*   遵循适用于您的操作系统的下载说明

**注意** : Python 环境变量
在使用 Windows 时，你需要将 Python 添加到你的路径中，以便在你的操作系统中的任何地方使用它。更多关于[这里](https://docs.python.org/2/using/windows.html#excursus-setting-environment-variables)。

现在安装`pyserial`库:

*   对于 Windows，打开 PowerShell(或命令提示符)并运行命令`pip install pyserial`
*   对于 Mac/Linux 打开终端并运行命令`pip install pyserial`

**注意**:运行命令的问题？
在极少数情况下，如果`pip install pyserial`不起作用，请尝试以下方法:`python -m pip install pyserial`

### **下载代码**

*   对于 Windows，在您选择的位置打开 PowerShell，并在 PowerShell 终端中运行 git clone `git@github.com:wiaio/nb-iot-utilities.git`
*   对于 Mac/Linux，在您选择的位置打开一个终端，并在终端中运行`git clone git@github.com:wiaio/nb-iot-utilities.git`

或者，您可以在此下载 zip 文件[。](https://github.com/wiaio/nb-iot-utilities)

*   提取你选择的位置的压缩文件

### **查找设备的串口**

#### *Linux 和 Mac OS X*

*   从[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)下载并安装 USB 转 UART 桥驱动程序。为您的操作系统和体系结构选择合适的版本
*   打开终端窗口并运行命令`ls /dev/tty*`
*   查找名称以`/dev/tty`开头的设备，例如 MAC 上的`/dev/tty.usbmodemPy343431`或 Linux 上的`/dev/ttyUSB0/dev/ttyACM0`

**注意**:对于 Linux，您可能需要运行下面两个命令。一旦你完成了，重启你的电脑。这将添加允许您将草图上传到板上的权限。
T0
T1】

### **视窗**

*   从[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)下载并安装 USB 转 UART 桥驱动程序。为您的操作系统和体系结构选择合适的版本
*   打开 Windows 开始菜单，搜索`Device Manager`
*   Pycom 设备的 COM 端口将被列为`USB Serial Device`或类似的名称
    *   记下 COM 端口(例如 COM4)

### **上传设置代码到你的 Arduino**

为了通过 Arduino 与 NB-IoT shield 进行通信，您需要使用代码来刷新 Arduino，以便为 shield 供电并为指令提供隧道。

*   安装 Arduino IDE。你可以在这里下载适用于 Mac OS X、Windows 和 Linux 的软件

在 Arduino IDE 中:

*   进入菜单:`Tools > Board > Boards Manager`
*   搜索`Arduino SAMD`。找到后，点击`Install`

[![alt text](../Images/dc8fed25f00323f4c2506397d801eea4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VvHhUE1N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/ba31e52-aruinodsambboard.png)

在 Arduino IDE 中:

*   转到`Tools > Board`选择 Arduino M0 板类型
*   选择显示`Arduino M0`的端口

```
initial_config.ino
 #include "Arduino.h"

#if defined(ARDUINO_AVR_LEONARDO)
#define USB Serial
#define UBLOX Serial1

#elif defined(ARDUINO_SODAQ_EXPLORER)
#define USB SerialUSB
#define UBLOX Serial

#elif defined(ARDUINO_SAM_ZERO)
#define USB SerialUSB
#define UBLOX Serial1

#else
#error "Please select a Sodaq ExpLoRer, Arduino Leonardo or Arduino m0."
#endif

// Pin to turn on/off the nb-iot module
#define powerPin 7
unsigned long baud = 9600;  //start at 9600 allow the USB port to change the Baudrate

void setup()
{
  // Turn the nb-iot module on
  pinMode(powerPin, OUTPUT);
  digitalWrite(powerPin, HIGH);

  // Start communication
  USB.begin(baud);
  UBLOX.begin(baud);
}

// Forward every message to the other serial
void loop()
{
  while (USB.available())
  {
    uint8_t c = USB.read();
    UBLOX.write(c);
  }

  while (UBLOX.available())
  {
    USB.write(UBLOX.read());
  }

  // check if the USB virtual serial wants a new baud rate
  if (USB.baud() != baud) {
    baud = USB.baud();
       UBLOX.begin(baud);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   现在将 [initial_config.ino](https://github.com/wiaio/nb-iot-utilities/blob/master/intial_config.ino) 代码复制并粘贴到 Arduino IDE 中
*   点击`Upload`闪屏现在你应该准备好和你的盾牌交流了。

### **更新 nb-iot.py**

在我们与设备交互之前，有几个变量需要更新文件`nb-iot.py`。
在你最喜欢的文本编辑器中，打开`nb-iot.py`并遵循以下步骤:

*   找到并替换`serial_name`为之前在教程中获得的端口
*   在 Wia 仪表板中找到并替换`accessToken`为您设备的密钥
*   找到并替换`apn`与您的运营商 APN(默认:沃达丰爱尔兰)
*   找到并使用您的运营商 ID 替换`network_operator`(默认:沃达丰爱尔兰)

**注意** : Wia CoAP API 端点细节
`52.17.209.228`是 Wia CoAP API 的 IP 地址，`5683`是端口。

[![alt text](../Images/a9cc48f5be8595722146eb0febf02728.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8lCFrTo---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/bed4da1-serial_accesstoken.png)

### **与设备接口**

*   对于 Windows，在 PowerShell 中，在 PowerShell 终端中运行`python nb-iot.py` -a
*   对于 Mac/Linux，打开一个终端并在终端中运行`python nb-iot.py -a`,该命令通过在设备上运行最简单的命令(AT)来检查设备是否正在使用我们的代码。

有关可用命令的完整列表:

*   对于 Windows，在 PowerShell 中，在 PowerShell 终端中运行 python nb-iot.py -h
*   对于 Mac/Linux，打开一个终端并在终端中运行 python nb-iot.py -h

### **向 Wia 发布事件**

要使用 nb-iot 设备将事件发布到 Wia，请运行以下命令:

*   对于 Windows，在 PowerShell 中，运行命令 python ` `nb-iot.py -aknt`
*   对于 Mac/Linux 打开终端并运行命令`python nb-iot.py -aknt`

该脚本执行以下操作:

*   检查代码是否可以与设备接口
*   重置设备
*   将设备连接到网络
*   打开一个套接字并通过 UDP 向 Wia 发送消息
*   输出应该如下图所示:

[![alt text](../Images/8cf37f7daf4ea02bcffcc4b1214748c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3bL5KgIm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/73aa36b-publishing_an_event_to_wia.png)

### **常见错误**

某些设备的固件可能较旧，与您尝试连接的网络不兼容。您可以按照此处的说明[更新您的设备固件。](http://support.sodaq.com/sodaq-one/firmware-upgrade/)

如果您仍然无法连接，请给我们留言。