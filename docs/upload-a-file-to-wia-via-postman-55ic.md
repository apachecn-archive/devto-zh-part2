# 通过邮递员将文件上传到 Wia

> 原文：<https://dev.to/wiaio/upload-a-file-to-wia-via-postman-55ic>

在本教程中，您将学习如何通过 postman 将文件上传到 Wia。Postman 是唯一完整的 API 开发环境，面向 API 开发者，被全球超过 500 万开发者和 10 万家公司使用。Postman 通过在工作流程的每个阶段支持开发人员，使使用 API 变得更快更容易，可用于 Mac OS X、Windows 和 Linux。有了 postman，你可以很容易地测试任何基于照片的流程。

如果您还没有，您需要使用 Wia 设置设备，并为设备获取设备密钥，此处的[是向您展示如何完成的教程](https://developers.wia.io/docs/getting-started-add-a-device)

#### **下载邮差**

要下载 postman，请点击[此处](https://www.getpostman.com/apps)并根据您选择的平台进行操作。

#### **上传你的文件**

*   打开邮差 app，输入网址:`https://api.wia.io/v1/events`
*   点击`POST`作为 http 请求的类型
*   在`Authorization`选项卡中，点击类型下拉并将其设置为`Bearer Token`
*   在屏幕右侧添加您之前获得的设备密钥

[![alt text](img/6e9f38bc5759340fb9c9d3e7f71c3f70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---OxEu7H5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-16/1523883667-756684-psotman.png)

现在在`body`选项卡中:

*   单击表单数据单选按钮
*   添加一个名称键并输入您的活动名称(我上传照片时会调用我的`photo`)
*   添加一个文件键，并将鼠标悬停在键框的末尾以选择文件而不是文本。
*   一个`Choose Files`选项卡将被添加到相邻的值部分，在这里您可以选择您的文件![alt text](img/4c56dfb9ebf0e24b3edd461f62d341df.png)

点击蓝色的`Send`按钮，进入 wia 调试器，查看输出
[![alt text](img/ae10338ac47afd7723ed0130f792cf0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---zyXUyBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-16/1523886025-990124-debuggerphote.png)