# 如何将 Sigfox Sens'It 连接到 Wia

> 原文：<https://dev.to/wiaio/how-to-connect-a-sigfox-sensit-to-wia-338b>

在本教程中，我们将把 Sigfox Sens'It 连接到 [Wia 平台](https://dashboard.wia.io/login)。

# 组件

*   Wia 账户。[您可以在这里创建一个](https://dashboard.wia.io/login)
*   西格福克斯感觉到了
*   USB 转微型 USB 电缆

# 将 Sens'It 连接到 Sigfox 应用程序

首先，给你的 Sens 'It 插上电源，确保它一直处于充电状态。然后，[跟随这个链接](https://www.sensit.io/)到 Sigfox 的 Sens 'It 平台。在右上角，点击`Launch App`。

[![alt text](../Images/4e3b13b415f83bf541c1dedc06adb1ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GGASmPz0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655169-784073-screen-shot-2018-08-07-at-41907-pm.png)

选择`Activate a Sens'It`。设备 ID 写在传感器的背面。在框中输入设备 ID。

[![alt text](../Images/5ed272762d3d2e92a803fc9c06640b58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdN7bldB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655215-294134-screen-shot-2018-08-07-at-41958-pm.png)
[![alt text](../Images/6f98723d0028d62acbda30b2d8bfb444.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--SFQipun0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655285-442484-screen-shot-2018-08-07-at-42036-pm.png)

继续并添加您的位置。按照提示完成激活传感器。

# 获取 PAC

一旦 Sens'It 被激活，[启动应用程序](https://www.sensit.io/app/sensit)。在左侧菜单中，选择设备的名称。然后，在右上角选择`Sens'It details`

在页面的右下方，点击蓝色按钮`Get my PAC`。下一个显示屏将显示您的 PAC。你以后会需要这个的。

# 将 Sens'It 连接到 Sigfox 后端

[点击此链接](https://buy.sigfox.com/activate)注册您的设备，这样您就可以在 Sigfox 后端对其进行配置。输入您的位置并点击`Next`。

系统会提示您输入您的`Device ID`和`PAC`。设备 ID 是传感器背面的数字。PAC 是我们在上一步中找到的序列。

继续执行`Account`步骤，创建一个账户。完成并确认。

现在，[跟随这个链接](https://backend.sigfox.com/auth/login)到 Sigfox 后端。输入您刚刚创建的帐户的登录详细信息。

# 向 Wia 发送事件

Wia 提供 Sigfox 集成，因此您可以将 sigfox 设备连接到 [Wia 平台](https://dashboard.wia.io/login)。

### 创建 API 键

*   在 Sigfox 后端，转到`Group > [Your group name] > API Access > New`
*   选择`DEVICES_MESSAGES [R]`

[![alt text](../Images/5caa6c6771a462febc36d085d7a844fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NqOI0F1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655451-320043-screen-shot-2018-08-07-at-42341-pm.png)

选中后，将为您创建一个 API 登录名和密码。

### 向 Wia 添加 API 键

*   转到 [Wia 仪表板](https://dashboard.wia.io/login)。
*   通过单击左上角的空间名称打开空间下拉列表，然后单击设置。选择边栏中的集成。

[![alt text](../Images/a5248b95ae4c2d4a6fe04ee6f61641b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tk8NOxt2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655515-324090-screen-shot-2018-08-07-at-42441-pm.png)

*   输入您的 API 访问登录名和密码。注意:这不是您用来登录 Sigfox 后端仪表板的，而是您刚刚创建的。
*   现在您应该看到 Sigfox 的入站密钥已经创建。

[![alt text](../Images/db419937e1870bd9c75fa0f1a403f63a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FG74c7-T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655560-510857-screen-shot-2018-08-07-at-42535-pm.png)

### 设置您的 Sigfox 回调

*   回到 [Sigfox 后端](https://backend.sigfox.com/auth/login)
*   选择`Device Type`，然后在您想要集成的设备类型列表的名称栏下
*   在左侧，选择 Callbacks(如果那里已经有任何 Wia 回调，那么删除它们)
*   单击左上角的“新建”以创建新的回调
*   选择自定义回拨

[![alt text](../Images/355f3373f4f13f488a63212328220fc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N8sOpCzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655632-231271-screen-shot-2018-08-07-at-42649-pm.png)

在每个字段中输入以下值:

*   类型:`DATA` `UPLINK`
*   频道:`URL`
*   Url 模式:`https://api.wia.io/v1/events`
*   使用 HTTP 方法:`POST`

头球

[![alt text](../Images/b1d5ce62e4a23e310ebb224085386a76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sNJ30ALY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533655659-973650-screen-shot-2018-08-07-at-42725-pm.png)

*   内容类型:`application/json`
*   正文:复制并粘贴下面的文本。

```
{
  "integrations":{
    "sigfox":{
      "id":"{device}"
    }
  },
  "name":"sigfoxDataUplink",
  "data":{
    "sigfoxData":"{data}",
    "time":"{time}",
    "duplicate":"{duplicate}",
    "snr":"{snr}",
    "station":"{station}",
    "avgSnr":"{avgSnr}",
    "lat":"{lat}",
    "lng":"{lng}",
    "rssi":"{rssi}",
    "seqNumber":"{seqNumber}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

点击`OK`保存回调。

一旦您的设备发送事件，它将自动出现在 [Wia 仪表板](https://dashboard.wia.io/login)中。

[![alt text](../Images/d12a172fdaea8a7d1babdcebd2f07293.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kNkkfNfJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-08-07/1533656741-178661-screen-shot-2018-08-07-at-44456-pm.png)