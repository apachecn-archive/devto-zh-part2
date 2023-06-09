# 用 NodeMCU Amica (ESP8266)测量土壤湿度

> 原文：<https://dev.to/wiaio/measuring-soil-moisture-with-the-nodemcu-amica-esp8266-53p2>

[![alt text](img/4ddf85726d74952e4f3e7365a5313f4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U8y_yp1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-05-24/1527154845-205657-tech3182-2-sdr2kznlof.jpeg)

在本教程中，我们将使用 NodeMCU Amica(基于 ESP8266 的开发板)和 Funduino 湿度传感器来测量土壤湿度，并构建一个 Wia 流，在您的植物需要浇水时通知您。

### **开始之前**

要设置 NodeMCU Amica，您需要遵循我们的教程[开始使用 ESP8266](https://developers.wia.io/v1.0/docs/espressif-esp8266) 。它详细介绍了如何设置正确的环境来完成本教程的其余部分。

### **连接硬件**

使用跳线将湿度传感器连接到电路板，如下所示:
[![alt text](img/2dbfb6a881c58e8f1a41839235b7edb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RxJ3AEsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/82b3be5-Screenshot_from_2018-05-15_15-01-06.png)

### **安装所需的库**

在 Arduino IDE 中，转到`Sketch > Include Libraries > Manage Libraries`。
通过在模态的搜索栏中搜索它们的名称来安装以下每个库。框的右下角会出现一个按钮，允许您安装库。

*   ArduinoJson
*   ESP8266WiFi
*   ArduinoHttpClient 如果搜索结果中没有显示库，您可能需要更新 Arduino IDE 版本。你可以在这里下载最新版本[。](https://www.arduino.cc/en/main/software)

### **代码**

在 Arduino IDE 中，复制并粘贴以下代码:

```
#include <ArduinoJson.h>
#include <ESP8266WiFi.h>
#include <ArduinoHttpClient.h>
#include <Arduino.h>

const char* ssid     = "your-ssid"; // Your WiFi ssid
const char* password = "your-password"; //Your Wifi password

// Get this secret key from the wia dashboard. It should start with `d_sk`
const char* device_secret_key = "your-device-secret-key";

// Wia API parameters
char server[] = "api.wia.io";
char path[] = "/v1/events";
int port = 80;

WiFiClient client;
int status = WL_IDLE_STATUS;

StaticJsonBuffer<200> jsonBuffer;
HttpClient httpClient = HttpClient(client, server, port);
JsonObject& root = jsonBuffer.createObject();

const int ANALOG_PIN = 0;

void setup() {

  // initialize serial communications and wait for port to open:
  Serial.begin(115200);
  while (!Serial) {
    ; // wait for serial port to connect. Needed for native USB port only
  }
  WiFi.begin(ssid, password);
  Serial.print("Attempting to connect to SSID: ");
  Serial.print(ssid);
  // attempt to connect to WiFi network:
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    // Connect to WPA/WPA2 network. Change this line if using open or WEP  network:
    // wait 5 seconds for connection:
    delay(5000);
  }

  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());
  Serial.println("connecting...");
}

// Thing function runs continiously
void loop() {

  root["name"] = "moisture";
  root["data"] =  analogRead (ANALOG_PIN);

  // if you get a connection, report back via serial:
  if (client.connect(server, port)) {

    sendToWia(root);

  } else {
    // if you didn't get a connection to the server:
    Serial.println("connection failed");
  }
  delay(1000*15*60); // Wait for 15 minutes to post again
}

// Adds the correct headers for HTTP and sends Data to Wia
void sendToWia(JsonObject& data) {
  size_t len = data.measureLength();
  size_t size = len + 1;
  char json[size];
  httpClient.beginRequest();
  httpClient.post(path);
  httpClient.sendHeader("Content-Type", "application/json");
  httpClient.sendHeader("Content-Length", data.measureLength());
  httpClient.sendHeader("Authorization", "Bearer " + String(device_secret_key));
  httpClient.beginBody();
  data.printTo(httpClient);
  Serial.print("Posting ");
  data.printTo(Serial);
  Serial.println(" to Wia");
  httpClient.endRequest();
} 
```

Enter fullscreen mode Exit fullscreen mode

替换下列变量的值(将正确的值放在代码中变量名右边的引号之间):

*   `your-ssid` -使用您的 WiFi 网络名称
*   `your-password` -使用您的 WiFi 网络密码
*   `your-device-secret-key`使用 Wia 仪表板中的设备密钥(以 d_sk 开头的那个)

上面的代码每隔 15 分钟向 Wia 发布一个事件，其中的`name`值为`moisture`(稍后在构建您的流时，您将需要用到它)，而`data`值等于湿度传感器的读数。

确保选择了正确的电路板-转到`tools > board`并选择`NodeMCU 1.0 (ESP-12E Module)`。
确保选择了正确的端口- `tools > port`。

如果这些步骤中的任何一个都不适合您，请参考我们的教程[开始使用 ESP8266](https://developers.wia.io/v1.0/docs/espressif-esp8266) 。

点击菜单中的`Sketch > Upload`。

转到 Wia 仪表板，通过设备调试器查看事件。

### **当你的植物需要浇水时通知你**

接下来，我们将设置一个流程，当水分含量低于某个阈值时，该流程会向您的手机发送通知。Wia 应用程序需要向您的手机发送推送通知，它可以安装在[此处](https://play.google.com/store/apps/details?id=io.wia.wia)用于 Android。

转到您的 Wia 仪表板，点击左侧菜单中的`Flows`。使用您喜欢的任何名称创建一个新流程。

在 Flow Studio 中，从触发器部分拖动一个`Event`节点，然后:

*   输入`moisture`作为`Event Name`
*   添加与您之前添加到代码中的设备相匹配的设备![alt text](img/0b58ae6f21d4b07c6869a2435e6e0892.png)

从逻辑部分拖动一个`function`节点，复制并粘贴以下代码:

```
// Check moisture levels
if (input.body.data && input.body.data < 500) {
  // Plant is thirsty
  output.process = true;
} else {
  // Plant is ok
  output.process = false;
} 
```

Enter fullscreen mode Exit fullscreen mode

土壤湿度传感器的输出是一个介于 0 和 1023 之间的数字，0 表示完全干燥，1023 表示完全湿润。

上面的代码告诉 Wia 只处理小于 500 的输入(即植物不需要浇水)。

最后，添加一个`notification`节点，并在文本字段中输入`The plant needs watering`，这样 Wia 就可以在您的植物需要浇水时向您发送通知。

[![alt text](img/ad5cf9903cc970223395450617d205b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qXItk_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-16/1523879728-577477-notification-node.png)

### **更进一步**

这只是让这个项目更上一层楼的一种方法:除了发送通知之外，我们还将水分数据输入 Wia 仪表板上的两个小部件。

首先，从逻辑部分添加另一个`function`节点，并将其连接到同一个`event`。在这个节点中，粘贴下面的代码:

```
// Check moisture levels
if (input.body.data && input.body.data < 500) {
  // Plant is thirsty
    output.body.data = "Thirsty";
} else {
  // Plant is ok
    output.body.data = "Ok";
} 
```

Enter fullscreen mode Exit fullscreen mode

如果植物需要浇水，这个代码输出‘口渴’,否则输出‘好’。我们现在将把这个`function`节点的输出连接到一个新的`event`动作节点。从“操作”部分(不是“触发器”)拖动一个按钮。这将创建一个新事件。给事件起一个名字(比如`status`，并将`function`节点连接到它。

[![alt text](img/1b85a599e1c1e8ad51fac96dbb779fd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVRnKlch--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/63ceff4-Screenshot_from_2018-05-15_15-17-15.png)

接下来，前往你的设备的“概述”标签。单击“添加小部件”。给小部件一个名称(“Status”)，选择小部件类型“text”，并输入您在流程中创建的事件的名称(`status`)。

[![alt text](img/d93f984af6dccb0a0858d047dd6bcff0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fsLQ4UN2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/1dada30-Screenshot_from_2018-05-15_15-22-17.png)

创建另一个类型为“text”的小部件，给它命名(“Moisture”)，并输入原始事件触发器的名称(`moisture`)。

[![alt text](img/e640afb81724c1ffac337e4031c528b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M2NPXrdV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/623e1d4-Screenshot_from_2018-05-15_15-24-46.png)

现在，当您的活动发布后，设备概览页面上的小部件将实时更新！

[![alt text](img/03a6c27648538971abea5ef600b40208.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FIJaGyih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/00a4538-Screenshot_from_2018-05-15_15-28-40.png)