# 设置 Espressif ESP8266 并将事件发布到 Wia

> 原文：<https://dev.to/wiaio/setup-an-espressif-esp8266-and-publish-an-event-to-wia-52dh>

### **连接到板卡**

`Note:If your board has a micro USB port (as with the NodeMCU breakout boards), you can skip this step, and go to the "Setup Your Environment" section below.`

通过 USB 转 TTL 电缆/适配器将您的主板连接到笔记本电脑/台式机。这是显示不同连接的引脚排列。

[![alt text](../Images/b561af6abd7a657fb8c165a142450a4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oilgUtzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/675b4ca-Wia_Blog_Illustration_1600x1052_03B_1.png) 
*ESP8266 引脚排列*

ESP 和 USB TTL 之间的连接如下:

*   ESP8266 USB TTL
*   gnd 国民生产总值
*   TX RX
*   VCC 3.3V(+)
*   CH_PD 3.3V(+)
*   GPIO0 GND(仅在闪烁电路板时)

### **设置您的环境**

*   安装 Arduino IDE(集成开发环境)。你可以在这里下载适用于 Mac OS X、Windows 和 Linux 的软件。

详细说明如下:

*   [为 Windows 安装 Arduino IDE](https://www.arduino.cc/en/main/software)
*   [安装用于 Mac 的 Arduino IDE](https://www.arduino.cc/en/Guide/MacOSX)
*   [安装用于 Linux 的 Arduino IDE](https://www.arduino.cc/en/Guide/Linux)

`Note: New to the Arduino IDE?
If you are completely unfamiliar with the Arduino IDE, watch this video [here](https://www.youtube.com/watch?v=nbD_V4QtNvY) to give you a better understanding of how it works.`

*   启动 Arduino 应用程序并打开`Preferences`
*   在附加的董事会经理 URL 中输入`http://arduino.esp8266.com/stable/package_esp8266com_index.json`。如果需要多个，可以用逗号分隔

[![alt text](../Images/75df8a965e2826b9d4f099dc43864e28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IGLxiIF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/90719ce-Screen_Shot_2018-01-04_at_12.03.11.png) 
*安装 ESP8266 Arduino 包*

*   转到`Tools > Board > Boards Manager`
*   搜索`esp8266`。找到后，点击`Install`

[![alt text](../Images/fcf47b00db7c7e96758f10d5802eed0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CYkR5B9U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/5293598-Screen_Shot_2018-01-04_at_12.04.59.png)

*   前往`Tools > Board`选择您的 ESP8266 板类型，然后选择您的类型。对于这个例子，你可以使用`NodeMCU 1.0 (ESP-12E Module)`
*   检查`Upload Speed`是否设置为`115200`
*   为板选择正确的端口

#### **注:选择端口时**

对于 ESP 8266，端口名称往往有不常见的名称，例如:Mac OS 上的
:Windows 上的`/dev/tty.usb.modem8232`或`/dev/cu.SLAB_USB_toUART`
:Linux 上的`Com4`或`Com3`
`/dev/ttyUSB0`或/ `dev/ttyACM0`

[![alt text](../Images/7275e88a561b89672fcab0ee494c6b3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JzqNFJKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/e0d9624-Screen_Shot_2018-01-04_at_12.06.55.png)

#### **USB 转 UART 所需驱动**

如果你的端口没有出现，你需要安装 USB 到 UART 桥 VCP 驱动程序。为此，请转到[这里](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)，然后下载并安装适用于您的操作系统的驱动程序。安装驱动程序后，重新启动 Arduino IDE。

##### **Mac 用户注意事项**

您可能需要安装两次驱动程序才能工作，因为第一次安装只会删除现有的驱动程序。

##### **只充 USB 线**

端口没有显示的另一个原因可能是 USB 电缆-确保您使用的电缆不是充电专用电缆，并且没有故障。

### **创建草图**

#### 用 NodeMCU Amica 测量土壤水分

如果你来自我们的“测量土壤湿度”教程，在这里继续学习教程。否则，请继续下面的教程。

*   点击`File > New`创建一个新草图
*   复制并粘贴下面的发布事件示例代码。你也可以在 GitHub [这里](https://github.com/wiaio/wia-board-examples/tree/master/Arduino/ESP8266)查看。在那里你将直接看到如何发布事件和位置

`*PublishEvent.ino*`

```
 /**
 * PublishEvent.ino
 *
 *  Created on: 09.01.2017
 *
 */

#include <Arduino.h>

#include <ESP8266WiFi.h>
#include <ESP8266WiFiMulti.h>

#include <ESP8266HTTPClient.h>

#define USE_SERIAL Serial

ESP8266WiFiMulti WiFiMulti;

const char* ssid     = "your-ssid";
const char* password = "your-password";

// get this from the wia dashboard. it should start with `d_sk`
const char* device_secret_key = "your-device-secret-key";

void setup() {
    USE_SERIAL.begin(115200);
    //USE_SERIAL.setDebugOutput(true);

    WiFi.mode(WIFI_STA);
    WiFiMulti.addAP(ssid, password);
}

void loop() {
    // wait for WiFi connection
    if((WiFiMulti.run() == WL_CONNECTED)) {
        HTTPClient http;

        USE_SERIAL.print("[HTTP] begin...\n");

        // configure wia rest api
        http.begin("http://api.wia.io/v1/events");

        USE_SERIAL.print("[HTTP] POST...\n");

        // set authorization token
        http.addHeader("Authorization", "Bearer " + String(device_secret_key));

        // set content-type to json
        http.addHeader("Content-Type", "application/json");

        // start connection and send HTTP headers. replace name and data values with your own.
        int httpCode = http.POST("{\"name\":\"temperature\",\"data\":21.5}");

        // httpCode will be negative on error
        if(httpCode > 0) {
            // HTTP header has been send and Server response header has been handled
            USE_SERIAL.printf("[HTTP] POST... code: %d\n", httpCode);

            // file found at server
            if(httpCode == HTTP_CODE_OK) {
                String payload = http.getString();
                USE_SERIAL.println(payload);
            }
        } else {
            USE_SERIAL.printf("[HTTP] POST... failed, error: %s\n", http.errorToString(httpCode).c_str());
        }

        http.end();
    }

    delay(10000);
} 
```

Enter fullscreen mode Exit fullscreen mode

替换以下变量的值:

*   `ssid` -使用您的 WiFi 网络名称。
*   `password` -用你的 WiFi 网络密码。
*   `device_secret_key`使用 Wia 仪表板(以 d_sk 开头的仪表板)中的设备密钥。
*   转到`Sketch > Upload`将其发送到您的 ESP8266。现在转到 Wia 仪表板中的设备，您应该看到数据出现在调试器中。

[![alt text](../Images/0a8597b6b9981c3c552442fcdba46325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fJNmUoYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/4fed030-Debugger_Wia.png) 
*Wia 设备调试器*

### **常见错误**

如果您遇到类似下图所示的错误，请检查您的板卡和端口是否在`Tools`菜单中正确设置。

[![alt text](../Images/e91827b3b81d29fed3eabd151cd53267.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xc8i314r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/9d72425-image_16.png) 
*板卡或端口故障*