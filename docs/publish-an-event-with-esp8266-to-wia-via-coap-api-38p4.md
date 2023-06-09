# 通过 CoAP API 向 Wia 发布带有 ESP8266 的事件

> 原文：<https://dev.to/wiaio/publish-an-event-with-esp8266-to-wia-via-coap-api-38p4>

在本教程中，我们将设置一个 ESP8266 板，并通过我们的 CoAP API 向 Wia 发布一个事件。

### **连接板**

注:如果您的主板有一个微型 USB 端口，您可以跳过这一步。

通过 USB 转 TTL 电缆/适配器将您的主板连接到笔记本电脑/台式机。这是显示不同连接的引脚排列。

[![alt text](img/b561af6abd7a657fb8c165a142450a4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oilgUtzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/675b4ca-Wia_Blog_Illustration_1600x1052_03B_1.png)

ESP 和 USB TTL 之间的连接如下:

*   ESP8266 USB TTL
*   gnd 国民生产总值
*   TX RX
*   VCC 3.3V(+)
*   CH_PD 3.3V(+)
*   GPIO0 GND(仅在闪烁电路板时)

### **设置您的环境**

*   安装 Arduino IDE。你可以在这里下载适用于 Mac OS X、Windows 和 Linux 的软件
*   启动 Arduino 应用程序并打开`Preferences`
*   在附加的董事会经理 URL 中输入`http://arduino.esp8266.com/stable/package_esp8266com_index.json`。如果需要多个，可以用逗号分隔

[![alt text](img/75df8a965e2826b9d4f099dc43864e28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IGLxiIF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/90719ce-Screen_Shot_2018-01-04_at_12.03.11.png)

*   转到`Tools > Board > Boards Manager`
*   搜索`esp8266`。找到后，点击`Install`

[![alt text](img/fcf47b00db7c7e96758f10d5802eed0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CYkR5B9U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/5293598-Screen_Shot_2018-01-04_at_12.04.59.png)

前往`Tools > Board`选择您的 ESP8266 板类型，然后选择您的类型。对于这个例子，我使用的是`NodeMCU 1.0 (ESP-12E Module)`。

您可以使用以下步骤之一获取设备端口的名称:

#### **Linux 和 Mac OS X**

*   从[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)下载并安装 FTDI 驱动。为您的操作系统和体系结构选择合适的版本
*   打开终端窗口并运行命令`ls /dev/tty*`
*   查找名称以`/dev/tty`开头的设备，例如 MAC 上的`/dev/tty.usbmodemPy343431`或 Linux 上的`/dev/ttyUSB0/dev/ttyACM0`

注意:
对于 Linux，您可能需要运行下面的两个命令。一旦你完成了，重启你的电脑。这将添加权限，允许您将草图上传到板上。

`sudo usermod -a -G tty ${USER}`
T1】

#### **视窗**

*   从[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)下载并安装 FTDI 驱动。为您的操作系统和体系结构选择合适的版本
*   打开 Windows 开始菜单，搜索设备管理器
*   ESP8266 设备的 COM 端口将被列为 USB 串行设备或类似设备
    *   记下 COM 端口(例如 COM4)

[![alt text](img/7275e88a561b89672fcab0ee494c6b3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JzqNFJKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/e0d9624-Screen_Shot_2018-01-04_at_12.06.55.png)

### **安装所需的库**

在 Arduino IDE 中，转到`Sketch > Include Libraries > Manage Libraries`。通过在模块的搜索栏中搜索库名来安装以下每个库。框的右下角会出现一个按钮，允许您安装库。

*   ESP8266WiFi
*   ArduinoJson

[![alt text](img/93ae3450343f6354756bff9f870e8dd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fmxVUuIg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-08/1525790522-719168-libraryesp.png)

#### **添加 COAP 库**

要添加 CoAP 客户端库，您必须手动将其添加到 Arduino 根文件夹中的`libraries`文件夹。

*   点击下载压缩文件
*   在操作系统中找到`Arduino`文件夹的位置
*   在`Arduino`文件夹中，点击进入`libraries`子文件夹
*   提取 zip 文件
*   重新启动 Arduino IDE

### **在 Wia 中添加一个设备**

如果您还没有，您需要在您的 Wia 帐户中创建一个设备。教程可以在这里找到[。](https://developers.wia.io/v1.0/docs/getting-started-add-a-device)

### **创建草图**

在 Arduino IDE 中:

*   点击`File > New`创建一个新草图
*   复制并粘贴下面的示例代码通过 CoAP 发布事件
*   回调包含一个占位符部分，用于编写代码，以便在命令被触发时做一些有用的事情

```
#include <ESP8266WiFi.h>
#include <ArduinoJson.h>
#include "coap_client.h"

//instance for coapclient
coapClient coap;

//WiFi connection info
const char* ssid = "your-wifi-ssid";
const char* password = "your-wifi-password";
String DEVICE_SECRET_KEY  = "your-device_secret_key";

//ip address and default port of coap server in which your interested in
IPAddress ip(52, 17, 209, 228);
int port = 5683;
char* path = "events";

StaticJsonBuffer<200> jsonBuffer;
JsonObject& root = jsonBuffer.createObject();

// coap client response callback
void callback_response(coapPacket &packet, IPAddress ip, int port) {
  char p[packet.payloadlen + 1];
  memcpy(p, packet.payload, packet.payloadlen);
  p[packet.payloadlen] = NULL;

  //response from coap server
  if (packet.type == 3 && packet.code == 0) {
    Serial.println("ping ok");
  }

  Serial.println(p);
}

void setup() {
  Serial.begin(115200);

  WiFi.begin(ssid, password);
  Serial.println(" ");

  // Connection info to WiFi network
  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    yield();
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected");
  // Print the IP address of client
  Serial.println(WiFi.localIP());

  // client response callback.
  // this endpoint is single callback.
  coap.response(callback_response);

  // start coap client
  coap.start();
}

void loop() {
  root["name"] = "temperature";
  root["data"] =  21.5;
  root["accessToken"] =  DEVICE_SECRET_KEY;

  String data;
  root.printTo(data);
  char dataChar[data.length() + 1];
  data.toCharArray(dataChar, data.length() + 1);
  bool state;

  //post request
  //arguments server ip address,default port,resource name, payload,payloadlength
  int msgid = coap.post(ip, port, path, dataChar, data.length());

  state = coap.loop();

  delay(5000);
} 
```

Enter fullscreen mode Exit fullscreen mode

替换下列变量的下列值:

*   `your-wifi-ssid` -使用您的 WiFi 网络名称
*   `your-wifi-password` -使用您的 WiFi 网络密码
*   `your-device_secret_key`使用 Wia 仪表板中的设备密钥(以 d_sk 开头的那个)
*   转到 Arduino IDE 菜单中的`Sketch > Upload`将其发送到您的 ESP8266
*   进入`Tools > Serial Monitor`，打开串行监视器，查看 ESP8266 的输出

现在转到 Wia 仪表板中的设备，您应该看到数据出现在调试器中。

[![alt text](img/0a8597b6b9981c3c552442fcdba46325.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fJNmUoYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/4fed030-Debugger_Wia.png)