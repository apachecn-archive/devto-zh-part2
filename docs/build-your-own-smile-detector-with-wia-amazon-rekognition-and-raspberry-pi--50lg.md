# 使用 Wia、Amazon Rekognition 和 Raspberry Pi 构建您自己的微笑检测器

> 原文：<https://dev.to/wiaio/build-your-own-smile-detector-with-wia-amazon-rekognition-and-raspberry-pi--50lg>

[![alt text](../Images/bb3949116feae2539934c2fefaeb18d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CIvnUi31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-15/1523818717-566790-recognition-animation-gif-speed-00054.png)

大家好，
在本教程中，我们将构建一个面部识别相机，它可以检测某人是否在微笑。

以下是你需要的:

*   树莓 Pi(任何型号 3，3+，零等)。
*   树莓 Pi 相机模组(我们推荐[这个](https://www.raspberrypi.org/products/camera-module-v2/)的)。
*   Micro SD 卡(我们建议至少 8Gb)。
*   微型 USB 转 USB 线。

我们已经有很多深入的指导，告诉你如何从头开始设置你的树莓 Pi。你可以在这里查看那些[。](https://developers.wia.io/docs/raspberry-pi)

### **设置相机**

一旦你做好了一切准备，就该开始在你的树莓皮上设置摄像头了。当您通过 SSH 登录到您的 Raspberry Pi 时，运行命令`sudo raspi-config`。按下向下箭头键，直到到达`5 Interfacing Options`并按下回车键。

[![alt text](../Images/585b30975f4076c51671f0d10c7c2d13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VMPNgjbn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523527924-901868-screen-shot-2018-04-12-at-103641.png)

确保选择了`P1 Camera`并按下回车键。

[![alt text](../Images/1e93921397978f8beee6b9652ee7b64c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9MX-dgkU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523527954-3232-screen-shot-2018-04-12-at-103653.png)

当询问`Would you like the camera interface to be enabled?`时，选择`<Yes>`。

[![alt text](../Images/5a4d8311579c9ace9957e8218f9f57c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YnYFVJof--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523527974-618439-screen-shot-2018-04-12-at-103742.png)

要退出，按下向下箭头键，直到您到达`<Finish>`并按下 Enter。

### **安装 Node.js**

依次运行以下 5 个命令:

```
cd ~
wget https://nodejs.org/dist/v4.3.2/node-v4.3.2-linux-$(uname -m).tar.gz 
tar -xvf node-v4.3.2-linux-$(uname -m).tar.gz
cd node-v4.3.2-linux-$(uname -m)
sudo cp -R * /usr/local/ 
```

Enter fullscreen mode Exit fullscreen mode

要检查 Node.js 是否安装正确，运行`node -v`。它应该返回`v4.3.2`。

### **设置项目**

通过运行命令`cd ~`转到您的主目录。通过运行`mkdir wia-pi-camera`创建一个新目录。通过运行命令`cd wia-pi-camera`进入这个目录。

通过运行`npm init`初始化项目。在每个提示下按 Enter 键。通过运行以下命令安装`wia`和`raspicam`库:

```
npm install --save wia
npm install --save raspicam 
```

Enter fullscreen mode Exit fullscreen mode

### **编写代码**

现在，我们将编写代码来捕获照片并将其发送给 Wia。

通过运行命令`touch run-camera.js`创建一个新文件。运行`nano run-camera.js`打开文本编辑器。

从下面复制并粘贴代码，用您设备的密钥替换`your-device-secret-key`。如果您还没有设置，您可以在 Wia 仪表板[这里](https://dashboard.wia.io/spaces)设置。

```
'use strict';

var wia = require('wia')('your-device-secret-key');
var fs = require('fs');
var RaspiCam = require("raspicam");

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

// Take a photo
camera.start(); 
```

Enter fullscreen mode Exit fullscreen mode

按`CTRL+O`(那是字母 o，不是数字)保存代码，然后按`CTRL+X`退出 Nano。回到您的终端，通过运行命令`node run-camera.js`运行代码。

[![alt text](../Images/b22a44b8fa34da63a6f3223c0f83f765.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T66B08Wq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523528587-325138-screen-shot-2018-04-12-at-112247.png)

在 Wia 仪表板的调试器中，您现在应该看到事件出现。

[![alt text](../Images/9c2000bbe8e8459b8cb3e9826bdcb646.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cOnW97wb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523528622-426028-screen-shot-2018-04-12-at-112310.png)

### **造流**

在 Wia 仪表板中，我们现在将构建检测笑脸的流程。在左侧菜单中，点击“流量”，然后创建一个名为“检测笑脸”的流量。

我们将执行以下 4 个步骤:

*   添加一个触发节点
*   添加“检测面孔”节点
*   添加运行功能节点
*   添加电子邮件节点

### **添加一个触发节点**

从触发器部分拖过事件节点。将鼠标悬停在节点上，然后单击齿轮图标以打开设置。输入`photo`作为事件名称，并点击更新。现在添加您希望此触发器应用到的设备。

[![alt text](../Images/23c5790c99ab7ed479b8840e59e25c1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2f8JMHg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523528878-925761-screen-shot-2018-04-12-at-112646.png)

### **添加一个检测人脸节点**

拖动“服务”下的“检测面孔”节点，并将其连接到“事件”节点。

[![alt text](../Images/1e4046b068b396a17cca8ee08fa9f191.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8rGOxE09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523528950-212324-screen-shot-2018-04-12-at-112810.png)

添加一个运行功能节点
拖过逻辑下的一个功能节点，并将其连接到检测面节点。

[![alt text](../Images/950e17030ccda4e82a9d1c3634477b3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oxlr7edN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523529004-671765-screen-shot-2018-04-12-at-112947.png)

将鼠标悬停在跑步功能节点上，点击齿轮图标打开设置。

复制并粘贴下面的代码块来检测微笑。然后单击更新。

```
if (input.body.faceDetails && input.body.faceDetails.length > 0) {
    output.body.isSmiling = input.body.faceDetails[0].smile.value;
} else {
    output.body.isSmiling = false;
} 
```

Enter fullscreen mode Exit fullscreen mode

### **添加邮件节点**

从“操作”下拖动电子邮件节点，并将其连接到“运行”功能节点。

[![alt text](../Images/46048f23ab5287ed06f868c475a3f638.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qjD3DTJe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523529464-891185-screen-shot-2018-04-12-at-113550.png)

将鼠标悬停在电子邮件节点上，点击齿轮打开设置。

输入您的收件人地址、主题行和正文，复制并粘贴`Detected smile: ${input.body.isSmiling}`。单击更新进行保存。

[![alt text](../Images/dafed081f988e7a8ffc5dd23738e1a6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3tGBB3zE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523529571-397273-screen-shot-2018-04-12-at-113920.png)

这就是你的整个流程设置！

一起运行所有东西
现在回到您的 Raspberry Pi，再次运行命令`node run-camera.js`来查看您的流运行情况。检查您的电子邮件收件箱中的邮件。

(可选)尝试其他面部特征
你可以尝试很多其他特征。[点击此处](https://developers.wia.io/docs/integrations-aws-rekognition)查看完整名单。