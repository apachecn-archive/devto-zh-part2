# 使用 ESP8266 的简介

> 原文：<https://dev.to/bocajnotnef/intro-to-working-with-with-esp8266-3bno>

原贴[此处](https://highnoiseratio.org/esp8266-intro.html)

## TL；博士；医生

我正在使用 ESP8266 构建环境传感器，这是一款超便宜的支持 wifi 的微控制器，可以运行 Micropython。本指南介绍了如何为 8266 编写驱动程序代码，以从 DHT 和土壤湿度传感器收集数据，并通过网络将其传输回服务器应用程序，该应用程序收集数据供以后使用。项目代码在处[可用。](https://github.com/bocajnotnef/microcontrollers/tree/master/greenhouse)

* * *

所以，我住在中西部，那里的冬天特别冷。我也喜欢园艺和新鲜蔬菜。这些事实没有很好地结合在一起。值得庆幸的是，像明尼苏达大学这样的地方有与深冬温室合作的推广项目，旨在在严冬种植植物。

我的(长期)目标是用微控制器建造并自动化其中一个，以监测和控制温室内的环境。因为我不想在建造温室的同时学习如何编程微控制器，所以我决定开始着手做这件事。

这篇文章将详细介绍我制作环境(温度、湿度、土壤湿度)传感器的过程，该传感器通过网络向中央服务器报告。

本指南/开发博客/教程将浓缩从 [Micropython ESP8266 教程](https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/intro.html)中获得的信息，这是一个非常好的参考，以及一些关于我正在使用的传感器的文档。

## 零件

*   1 [ESP8266 ESP12](https://www.ebay.com/itm/132532596048)
*   1 个 DHT11 传感器/ 1 个 [DHT 22 传感器](https://www.ebay.com/itm/DHT22-Digital-Temperature-Humidity-Sensor-AM2302-Module-PCB-Cable-Arduino-Q32/371765844729?hash=item568ef89af9:g:X7EAAOSwpLNYBZCJ)
*   1 [土壤湿度传感器](https://www.ebay.com/itm/Soil-Hygrometer-Detection-Module-Moisture-Sensor-for-Arduino-Smart-Robot-Car/121861281045?epid=1938324160&hash=item1c5f7f9515:g:P7kAAOSw-xVaOFvk)
*   1 个 USB 壁式电源插座+微型电缆

我假设你身边有各种跳线，还有一台笔记本电脑。

## 软件

我使用的所有工具都是基于 Python 的；所以我假设您在 pip 旁边安装了 Python 3。您将需要一些 pip 包:

```
pip install adafruit-ampy esptool 
```

Enter fullscreen mode Exit fullscreen mode

您还需要一份来自 [micropython 页面](http://micropython.org/download#esp8266)的用于 ESP8266 的最新稳定的 micropython 固件。

我使用串行工具`picocom`通过 USB 与电路板对话；你可以使用任何你喜欢的串行连接工具，只要它能工作。

## 为什么是 ESP8266？

因此，刚刚开始使用微控制器，我对它们知之甚少，但我会告诉你我对 8266 的了解。

最初在这个项目中，我打算使用一个通过 USB 连接到 raspi 的 arduino。arduino 将通过其 GPIO 端口收集传感器读数，并通过 USB 串行接口将它们报告给 raspi。pi 将解析传入的数据，对其进行 coallate 处理，并将其发送到控制服务器。这个设计有几个问题:

1.  需要 raspi 通过物理连接为 arduino 供电并与之通信。这将很难在温室周围放置许多监测传感器，因为现在我需要运行电缆。也增加了成本，因为对于每 4 个传感器 arduinos，我需要一个 raspi 来供电和控制它们。
2.  我不得不用 C 语言为 arduino 编写程序。这是我预料到的，因为 C 语言无疑是最适合微控制器的语言，但也很糟糕，因为 C 语言使用起来很麻烦，尤其是对初学者来说。

我可以通过将 wifi 芯片连接到 arduino，并通过网络传输数据来缓解问题 1。我不喜欢用 C 来做这件事，但这在技术上是可能的；快速研究将我引向了一款名为 ESP8266 的芯片，这款芯片显然非常受希望通过 wifi 使用微控制器的人的欢迎。在我第一次通过时，我忽略了 ESP8266 是一个独立的微控制器*。它不需要 arduino，并且拥有自己的 GPIO 功能。事实上，8266 在可用内存方面远远超过 arduino。*

 *因此，我改变了思路，不再构建一个通过 USB 连接到 raspi 的 arduino，而是决定使用一组 8266 通过 wifi 将数据传输回一个单一的中央 raspi。

## 入门

我从 Ebay 订购的 ESP8266s 声称与 AT firmwhere 一起发货，在那里你可以通过串行向 ESP8266 发出命令，用于连接网络、打开 TCP/IP 套接字、发送数据、接受输入数据等。对我来说不幸的是，要么他们没有包括固件，固件不是我期望的，要么我只是太无能以至于不能正确地使用它，所以我花了大约三个小时试图说服这些该死的东西做我想要的事情，然后我跑进了 Micropython 项目。

按照他们超级有用的 ESP8266 教程的指示，我在我的`/dev`中寻找这个设备；运行`ls /dev/tty*`并寻找以`USB0`或`ACM0`结尾的东西会给你指出正确的方向。

一旦我弄清楚它在哪个端口上，我就把 Micropython 固件复制到芯片上:

```
$ esptool.py --port /dev/ttyUSB0 --baud 460800 write\_flash --flash\_size=detect -fm dio 0 esp8266-20171101-v1.9.3.bin 
```

Enter fullscreen mode Exit fullscreen mode

然后我用串口连接到芯片:

```
$ picocom /dev/ttyUSB0 -b115200 
```

Enter fullscreen mode Exit fullscreen mode

我收到了 MicroPython REPL 的问候！太棒了。

## 挂接传感器

[![ESP8266 Pinout](img/9567ee8387c5dde935c1e9906854930a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oUSU24bF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/highnoiseratio.oimg/esp8266-pinout.jpg)

我的大多数传感器平台将是一个土壤湿度传感器，运行一个 LM393 比较器芯片和一个 DHT 11 或 DHT 22 传感器。

DHT 通过单线接口发射数字传感器，因此您可以将其正极引线连接到 8266 的 3.3v 电源，负极引线连接到 8266 的地，中间数据引线连接到 ESP8266 的一个数字 GPIO 引脚。

不幸的是，湿度传感器只能根据土壤的湿度给出一个 0 到 1 伏的模拟信号。使用 micropython 中的 ADC 模块，它将读取一个介于 0 和 1024 之间的值——我们稍后将回到这个问题。

与 DHT 传感器一样，ESP8266 上的正极引脚连接到 3.3v，负极引脚连接到地，芯片的模拟输出连接到 8266 的模拟输入引脚，只有一个模拟输入引脚，位于引脚排列图的左上角。在我使用的电路板上，它被标为“ADC0”或“A0”。

关于 LM393 芯片，有一点需要注意，它包含一个电位计，当模拟值超过阈值时，它会发送一个数字信号——低于阈值时为“0 ”,高于阈值时为“1”。电位计可以用螺丝刀和一些耐心来调整。对于我的用途，我更关心模拟值。

现在您会注意到，我们已经使用了 ESP8266 的三个 3.3 供电轨中的两个。也许可以将这些线路分割成多个方向，但我不是电气工程师，不知道这样做是否可行，所以要小心行事。

我还打算通过 USB 端口为 8266 供电。你的计划可能不同，但我是个简单的人。

## 编写程序

所以，作为使用 Micropython 的一个好处，我不用写 C 代码。由于我将处理网络交互和数据收集，我对此非常满意。

现在，缺点是我不能对内存进行细粒度的控制，而且 Micrpython 在设备的程序存储上占用的空间比同等的 C 二进制文件要多。我不打算从芯片中挤出每一点点性能，所以我可以牺牲一点空间和管理来支持更简单的代码。

Micropython 提供了几个现成的模块来完成我需要的传感器数据收集和网络通信。8266 的二进制代码还包含了相当容易连接到网络的功能。我建议在第一次刷新二进制文件时，通过串行链接花一些时间在 REPL 上，以便熟悉可用的功能。

### 连接网络

这可能是 ESP8266 的精华，也是你为什么要用它的原因:这该死的东西有 2.4ghz 的 wifi 功能。Micropython 意识到了这一点，并在 repl 提供的`help()`函数中包含了连接的快速说明，他们的文档也包含了这个函数:

```
def do_connect():
    import network
    sta_if = network.WLAN(network.STA_IF)
    if not sta_if.isconnected():
        print('connecting to network...')
        sta_if.active(True)
        sta_if.connect('<essid>', '<password>')
        while not sta_if.isconnected():
            pass
    print('network config:', sta_if.ifconfig()) 
```

Enter fullscreen mode Exit fullscreen mode

当从您的程序调用此功能时，它将阻止执行，直到 8266 使用指定的密码连接到 wifi 网络。

我对此做了一点修改，当它连接时，我的板上的内置 LED 会闪烁，所以我可以对设备的状态有一个大致的了解。(**怪异注:**不知什么原因，led.on()会关闭 led，led.off()会开启。不知道为什么。)您还会注意到，我将网络名称和密码提取到从配置文件加载的变量中:

```
def do_connect():
    global led_state
    sta_if = network.WLAN(network.STA_IF)
    if not sta_if.isconnected():
        print('connecting to network...')
        sta_if.active(True)
        sta_if.connect(NETNAME, NETPASS)
        while not sta_if.isconnected():
            if led_state:
                status_led.on()
                led_state = False
            else:
                status_led.off()
                led_state = True
            time.sleep(0.25)
    print('network config:', sta_if.ifconfig())
    status_led.off() 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？很好而且没有痛苦。(你不庆幸这不是 C 吗？)

### 连接到传感器

#### DHT 传感器

对，所以你连上了无线网络。您还没有与网络上的任何内容进行对话，但没关系，我们稍后会谈到这一点。

我们正在使用的 micropython 二进制文件包括一个使用 DHT 传感器的库，因为它们显然非常流行。我有一台 DHT11，稍微便宜一点，但读数误差大得令人恶心(2 摄氏度！).DHT22 稍微贵一点(DHT11 大约 2 美元/个，DHT22 大约 3.5 美元/个)，但更精确，代价是你可以从传感器读取数据的频率。我们也会谈到这一点。

所以，连接 DHT 传感器很简单。我们引入机器模块来访问硬件引脚，引入 DHT 模块来与该引脚上的任何内容进行通信:

```
d = dht.DHT11(machine.Pin(5)) 
```

Enter fullscreen mode Exit fullscreen mode

哇，那很容易。

现在，**重要提示:**你交给机器的数字。Pin 是 8266 pin 的号码**，而不是你正在使用的任何方便的板。如果您在连接传感器时遇到问题，请注意您的引线图。**

无论如何，现在我们有了这个 DHT11 对象，我们可以告诉它测量:

```
d.measure() 
```

Enter fullscreen mode Exit fullscreen mode

更新这些值:

```
d.temperature(); d.humidity(); 
```

Enter fullscreen mode Exit fullscreen mode

另一个注意，在 DHT11 上，大约每秒钟只能调用一次 measure。在 DHT22 上，每两秒钟只能读一次。我不知道这是为什么，也不知道不遵守会有什么后果。再次提醒，如果你在画线外上色，要小心。

#### 土壤湿度传感器

正如我之前提到的，土壤湿度传感器——正如我们所连接的——会反馈一个模拟信号。我们将让 ESP8266 将该信号解释为数字值。

这里，我们用这个引脚制作了一个 ADC 对象:
python

```
adc = machine.ADC(0)adc.read() 
```

Enter fullscreen mode Exit fullscreen mode

`adc.read()`将产生一个介于 0 和 1024 之间的值。显然，1024 意味着“干燥”，“0”意味着“比水潮湿”，因为我把传感器放在一杯水里，只得到 300 左右的读数。我不知道为什么会这样，但就是这样。你想用就用吧。

### 网络通讯

好了，我们已经有了数据，我们连接到了 wifi，现在我们需要从我们所在的微控制器中获取数据，并将其放到有用的地方。Micropython 包含一个`socket`模块，其行为就像[Macro]中的普通模块一样？Python。在我的代码中，我将服务器应用程序的 IP 和端口记录在一个配置文件中，因此我们打开套接字并开始向它发送数据:

```
socket_connected = False

while True:

    while not socket_connected:
        try:
            s = socket.socket()
            s.connect((SERVIP, SERVPORT))
            s.send("Initializing link...\n")
            socket_connected = True
        except OSError:
            print("Socket connection failed... waiting.")
            # pulse LED to indicate problem
            status_led.on()
            time.sleep(0.1)
            status_led.off()
            time.sleep(0.1)
            status_led.on()
            time.sleep(0.1)
            status_led.off()
            time.sleep(0.1)
            status_led.on()
            time.sleep(0.1)
            status_led.off()

    status_led.on()
    d.measure()
    msg = "Soil: " + str(adc.read()) + "; temp: " + str(d.temperature()) + "; hum: " + str(d.humidity()) + "; from " + mac + "\n"
    print(msg)
    try:
        q = s.send(msg)
    except OSError:
        socket_connected = False
    print("Sent ", q, " bytes.")
    status_led.off()
    time.sleep(2) 
```

Enter fullscreen mode Exit fullscreen mode

走过这里:

我们陷入了一个永远的循环，所以我们将收集和传输数据，直到时间的尽头。

当我们没有活动的套接字连接时，尝试创建一个。如果服务器没有监听，打开套接字的尝试将超时，我们将愤怒地闪烁 LED 并重试。

(我需要更新这段代码来检查并确保我们仍然有 wifi 链接——如果我们无论如何都没有连接，那么尝试传输数据是没有意义的。)

如果我们有一个套接字连接，我们就不会被困在这个循环中，我们会继续这个循环。

我们更新温度传感器，然后构造一个消息来传输。这个消息可以是你想要的任何形式，我很确定它会被转换成字节，所以如果你真的想要，你可以发送原始的二进制数据。现在，我只是发送字符串来简化我的数据加载。

我还附加了这块板的 mac 地址，这样当我在服务器上时，我可以识别读数来自哪个传感器。我用这一行获得了 mac 地址。我从这里偷的:

```
mac = ubinascii.hexlify(network.WLAN().config('mac'),':').decode() 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们构造了消息，我就打印它，将它转储到串行输出，这使得调试变得轻而易举，并且我将它写入套接字。

结束时的睡眠让温度传感器有时间休养生息，并给我们一个合理的数据流速率。

这样，你就有了一个可以通过网络发送数据的微控制器环境传感器！

你可以在这里查看我所有的项目代码。*