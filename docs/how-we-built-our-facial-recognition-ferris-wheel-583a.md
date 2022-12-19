# 我们如何建造面部识别摩天轮

> 原文：<https://dev.to/wiaio/how-we-built-our-facial-recognition-ferris-wheel-583a>

[![''](../Images/baa8ce7a22698d8e9b72ed2cd28cf964.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vIqoifTc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/29963bd-DSC_0292.jpg)

在 2018 年最酷项目[上，我们展示了带有面部识别摩天轮的](http://coolestprojects.org/) [Wia 平台](https://www.wia.io)——如果你在微笑，轮子就会转动，如果你不微笑，它就会停止。今天我们要谈谈我们是如何构建这个项目的，也许你会受到启发去构建类似的东西！

## 我们用什么

*   乐高摩天轮套装
*   Arduino MKR 1000
*   L293D 电机驱动器
*   高扭矩 DC 电机
*   试验板
*   树莓派零度 W
*   Raspberry Pi 相机模块(我们用了[这个](https://www.raspberrypi.org/products/camera-module-v2/)

## 乐高摩天轮

对于摩天轮本身，我们决定用[这个乐高套件](https://shop.lego.com/en-IE/Ferris-Wheel-10247)。大概花了 7 个小时左右完成。该套件有一个可选的附加电机，但我们决定创建自己的解决方案。

## 驱动电机

我们使用高扭矩 DC 电机来转动车轮，L293D 电机驱动器来驱动电机。
[点击这里](https://community.wia.io/d/27-driving-a-dc-motor-with-an-arduino-and-the-l293d-motor-driver)深入阅读我们如何设置它，以及让马达转向的基本代码片段。在本文的后面，当我们添加 MQTT 功能来监听命令时，我们将构建这个代码。

[![''](../Images/2cd18fc81c5e3181e45dc88ac1685b34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7pdxtqzz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/3e68a3e-DSC_0351.jpg)
[![''](../Images/588cea49e4294e4d25920c053c1cf9a3.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--L-w5LYIh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/2f2418e-DSC_0357.jpg)

## 将电机安装到车轮上

下一步是将 DC 马达连接到摩天轮装置上，让它转动。这被证明是相当具有挑战性的，并且需要拆除结构的下部来为部件腾出空间。我们发现马达的轴几乎完全适合一个乐高积木，我们所要做的就是将马达安装在正确的位置，并确保它不会四处移动。因此，我们用多余的零件为马达做了一个小小的乐高外壳，在后面留出空间，用于连接电线和马达的端子。

[![''](../Images/5f69ee80f96cbf79fbfefd2fd1581b39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QA0Ov_LA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/249d8a6-DSC_0327.jpg)

## 监听命令

接下来，我们想要为 Arduino 创建一些[命令](https://developers.wia.io/docs/add-a-command)来监听，并相应地旋转电机。第一步是在 Wia 仪表板中创建命令。正如你所看到的，我们创建了一些手动控制滚轮的基本命令，以及一个“拍照”命令，我们稍后会讲到。

[![''](../Images/c807448d2d1ab91965faf16a9766ca18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Am5V-rsa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/325afb3-ferris-wheel-commands-2.png)

接下来，我们更新了 Arduino 代码，使用 MQTT 来监听命令。下面是代码:

```
#include <WiFi101.h>
#include <MQTT.h>

const char WIFI_SSID[] = "wifi-ssid"; // WiFI ssid 
const char WIFI_PASS[] = "wifi-password"; //WiFI password

// get this from the wia dashboard. it should start with `d_sk`
const char* device_secret_key = "device-secret-key";

//WiFiSSLClient ipCloudStack;
WiFiClient wifiClient;
MQTTClient mqttClient;

int status = WL_IDLE_STATUS;

// Wia Cloud MQTT params
char mqttCloudServer[]     = "api.wia.io";
int  mqttCloudPort         = 1883;
char mqttCloudUsername[]   = "device_secret_key"; 
char mqttCloudPassword[]   = " "; 

// Wia API parameters
char server[] = "api.wia.io";
const int mqttPort = 1883;  // Default MQTT port

const String deviceId = "device-id";   // starts with dev_, found in Wia Dashboard
const String commandName = "take-photo";  // Configured in Wia Dashboard

// Topics
String takePhotoCommandTopic = "devices/" + deviceId + "/commands/take-photo/run";
String rotateForFiveCommandTopic = "devices/" + deviceId + "/commands/rotate-for-5-seconds/run";
String startMovingCommandTopic = "devices/" + deviceId + "/commands/start-moving/run";
String stopMovingCommandTopic = "devices/" + deviceId + "/commands/stop-moving/run";

//L293D
const int motorPin1  = 7;
const int motorPin2  = 6;

void messageReceived(String &topic, String &payload) {
  Serial.println("incoming: " + topic + " - " + payload);
  if (topic.equals(rotateForFiveCommandTopic)) {
    Serial.println("Rotating for 5 seconds.....");
    // Start rotating
    digitalWrite(motorPin1, HIGH);
    digitalWrite(motorPin2, LOW);
    // wait 5 seconds for connection:
    delay(5000);
    // Stop rotating
    digitalWrite(motorPin1, LOW);
    digitalWrite(motorPin2, LOW);
  } else if (topic.equals(startMovingCommandTopic)) {
    Serial.println("Start rotating.....");
    // Start rotating
    digitalWrite(motorPin1, HIGH);
    digitalWrite(motorPin2, LOW);
  } else if (topic.equals(stopMovingCommandTopic)) {
     Serial.println("Stop rotating.....");
    // Stop rotating
    digitalWrite(motorPin1, LOW);
    digitalWrite(motorPin2, LOW);
  } else {
    Serial.println("Unhandled topic");
  }
}

void connect() {

 // check for the presence of the shield:
  if (WiFi.status() == WL_NO_SHIELD) {
    Serial.println("WiFi shield not present");
    // don't continue:
    while (true);
  }

  // attempt to connect to WiFi network:
  while ( status != WL_CONNECTED) {
    Serial.print("Attempting to connect to SSID: ");
    Serial.println(WIFI_SSID);
    // Connect to WPA/WPA2 network. Change this line if using open or WEP network:
    status = WiFi.begin(WIFI_SSID, WIFI_PASS);

    // wait 5 seconds for connection:
    delay(5000);
  }
  Serial.print("\nconnecting...");

  Serial.println("\nconnected!\n");
  Serial.print("\nIP address: ");
  Serial.println(WiFi.localIP());

  // You need to set the IP address directly.
  mqttClient.begin(mqttCloudServer, mqttCloudPort, wifiClient);

  Serial.println("start wia connect"); Serial.println();

  while (!mqttClient.connect("wiatest", mqttCloudUsername, mqttCloudPassword)) {
    Serial.print("*");
    delay(500);    
  }

  Serial.println("Connected to MQTT");

  mqttClient.onMessage(messageReceived);

  mqttClient.subscribe(takePhotoCommandTopic);
  mqttClient.subscribe(rotateForFiveCommandTopic);
  mqttClient.subscribe(startMovingCommandTopic);
  mqttClient.subscribe(stopMovingCommandTopic);
}

void setup() {
  Serial.begin(115200);

  pinMode(motorPin1, OUTPUT);
  pinMode(motorPin2, OUTPUT);

  digitalWrite(motorPin1, LOW);
  digitalWrite(motorPin2, LOW);  

  connect();
}

void loop() {
  mqttClient.loop();
  delay(1000);

  if (!wifiClient.connected()) {
    connect();
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

将代码上传到 Arduino 后，我们只需点击 Wia 仪表盘上的“运行”命令，摩天轮就会相应地转动。

## 面部识别摄像头

对于面部识别摄像头，我们使用了一个带有 Raspberry Pi 摄像头模块的 Raspberry Pi Zero W。
在这里深入阅读我们是如何设置的[。这包括使用相机模块设置 Raspberry Pi，使用 NodeJS 拍摄照片并将其作为事件发布到 Wia 平台，以及设置检查照片主题是否微笑的基本流程。](https://community.wia.io/d/3-build-your-own-smile-detector-with-wia-amazon-rekognition-and-raspberry-pi)

[![''](../Images/3787cd658f7529891f24c94bd7d968d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LEDvimEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/bc42f81-DSC_0316.jpg)

我们在这个设置的基础上做了更多的改进，增加了 Raspberry Pi 监听“拍照”命令的能力。下面是代码:

```
'use strict';

var wia = require('wia')('device-secret-key');
var fs = require('fs');
var RaspiCam = require("raspicam");

wia.events.publish({
    name: "started"
});

// Setup the camera
var camera = new RaspiCam({
  mode: 'photo',
  output: __dirname + '/photo.jpg',
  encoding: 'jpg'
});

// Listen for the "start" event triggered when the start method has been successfully initiated
camera.on("start", function(){
  console.log("Starting to take photo.");
});

// Listen for the "read" event triggered when each new photo/video is saved
camera.on("read", function(err, timestamp, filename){
  console.log("New photo created.", timestamp, filename);

  // Publish the photo to Wia
  wia.events.publish({
    name: 'photo',
    file:  fs.createReadStream(__dirname + '/' + filename)
  });
});

wia.stream.on("connect", function() {
  console.log("Connected to stream.");
  wia.events.publish({name: "connected"});
});

setTimeout(function() {
  wia.commands.subscribe({
    slug: 'take-photo'
  }, function(err, data) {
    wia.events.publish({name:"gotCommand"});

    console.log("In command callback. Taking photo.");

    // Take a photo
    camera.start();
  });
}, 12500);

wia.stream.connect(); 
```

Enter fullscreen mode Exit fullscreen mode

## 建筑流动

我们的下一步是通过创建一个[流](https://developers.wia.io/docs/getting-started-flows)，让 Raspberry Pi 和 Arduino 在 Wia 上协同工作。

在 Wia 仪表板中，我们创建了一个新的流。触发器是运行“拍照”命令时创建的`photo`事件。然后，它经过一个“检测人脸”节点，该节点的输出分为两个逻辑节点——如果对象在微笑，一个输出字符串“微笑”,如果对象没有微笑，另一个输出字符串“不微笑”。
这是“微笑”逻辑节点的代码:

```
if (input.body.faceDetails) {
  input.body.faceDetails.forEach(function(face) { 
    if (face.smile.value == true) {
        output.process = true; 
      output.body.data = "Smiling";
    } else {
        output.process = false; 
    }
  });
} else {
    output.process = false; 
} 
```

Enter fullscreen mode Exit fullscreen mode

...这是“不笑”逻辑节点的代码:

```
if (input.body.faceDetails) {
  input.body.faceDetails.forEach(function(face) { 
    if (face.smile.value == false) {
        output.process = true; 
        output.body.data = "Not smiling";
    } else {
        output.process = false; 
    }
  });
} else {
    output.process = false; 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果拍摄对象在微笑，就会运行“开始移动”命令，触发 Arduino 开始转动马达。如果拍摄对象没有笑，就会运行“停止移动”命令，停止电机转动。

[![''](../Images/1f59685d3fb50169aaed67f69a94cdb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UA28UKBy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/9246690-ferris-wheel-flow.png)

## 创建微件

您会注意到，在流程的末尾还有“创建事件”节点。这就是我们使用来自逻辑节点的`output.body.data`值来创建[小部件](https://developers.wia.io/docs/widgets)的地方！
每次拍照时，都会创建一个新事件，其数据等于“微笑”或“不微笑”。在仪表板的设备概览页面上，我们能够创建一个文本小部件，它将显示此文本，并在每次拍摄新照片时自动更新。我们还设置了方便的小部件来运行各种命令，以及显示 Raspberry Pi 拍摄的照片！

[![''](../Images/8942d9f6163f4feb62f737f3970d1f96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Ml79BEE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/16895f3-ferris-wheel-widgets.png)

[![''](../Images/8353fd92bd5af1ad8c6808f23c09f356.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n30RbJJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.readme.io/29e73b4-DSC_0288.jpg)