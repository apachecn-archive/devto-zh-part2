# 利用 RichMenu 改进 UX 线机器人

> 原文：<https://dev.to/kenakamu/improve-line-bot-ux-by-using-richmenu--2jo5>

你有没有想过如何使用你刚开始使用的机器人？或者，机器人在开始时给你一堆选项，但你必须不断请求“帮助”来提醒自己机器人能做什么？

LINE Bot 有解决方案，菜单丰富。更多细节见[概述](https://developers.line.me/en/docs/messaging-api/using-rich-menus/)。

好消息是，作为开发人员，您可以通过 API 使用它！

*   您可以创建多达 10 个丰富的菜单。
*   您可以在丰富的菜单中指定多达 20 个可点击区域
*   您可以为任何用户设置任何 richmenu。

通过使丰富的菜单可编程，您可以根据您的上下文、时间等交换丰富的菜单。是不是很刺激！？

# 丰富菜单管理器

首先，您需要创建丰富的菜单。您可以使用 API 来创建它们，但是我开发了一个 GUI 工具来简化它。

## 先决条件

*   Node.js:使用版本 8.7.0 进行测试
*   浏览器:使用 Chrome 进行测试

## 安装

1.运行以下命令。对于 macOS，必要时使用 sudo。

```
npm install –g line-richmenus-manager
or
sudo npm install –g line-richmenus-manager 
```

Enter fullscreen mode Exit fullscreen mode

2.运行以下命令启动该工具。

```
line-richmenus-manager –port:3000 
```

Enter fullscreen mode Exit fullscreen mode

如果省略端口号，默认情况下它使用 3000。

3.访问 [http://localhost:3000](http://localhost:3000) 。

4.指定通道访问令牌，然后单击确定。

[![image](img/65daf2f0d5c15e48fcab7a43854a961d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eN_D3qtM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb67.png)

## 创建丰富的菜单

1.单击顶部导航栏上的[+]图标。

2.输入姓名和聊天栏文本。

[![image](img/9b6dd89c718391e5fec40e6810f578f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jntAk_Os--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb68.png)

3.单击“选择文件”按钮并选择 PNG/JPEG 文件。图像尺寸必须为 2500×1686 或 2500×843。

[![image](img/98179270a674901d0fd0f5b52c6d6c77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sYYiMMpm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb69.png)

4.用鼠标画矩形。您可以随后更改 x/y/宽度/高度值，以进行像素级修改。

[![image](img/446b6c3a0e23382094391ca3510be0a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fN9w2-rh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb70.png)

5.选择操作类型和值，然后单击添加区域。

[![image](img/7e1f9f7c4505eab073c1b39a5af2a15b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WshdMFal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb71.png)

6.您可以通过数字轻松识别区域和相应的操作。

[![image](img/de2724cdfc57169f209fd960b2649137.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LWCsTlO2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb72.png)

7.如果你犯了错误，你可以删除行动，点击垃圾桶图标，或改变它从列表中。

8.完成添加区域后，单击创建 RICHMENU。

[![image](img/8fdefb7ef034246f6babd9bff08a3d30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hB0j9S6M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb73.png)

## 查看详情

在主屏幕中，您可以看到所有创建的丰富菜单和每个菜单的详细信息。

1.从左侧窗格的列表中选择一个丰富的菜单。

[![image](img/30d203f5bfda5883c70b5dccaa4be50c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cNzfBtkA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb74.png)

2.详细信息显示在右窗格中。

[![image](img/4d9a94feec60b6cd1f6edd097028be77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7hAyLhLv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb75.png)

3.单击删除丰富菜单进行删除。

4.您也可以将丰富的菜单链接到用户。单击链接或取消链接，然后输入用户 Id。

[![image](img/26da0f462eb99d02e0e26119fa903854.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HoSfMoRF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb76.png)

## 搜索链接到用户的丰富菜单

如果您想检查哪个丰富的菜单链接到一个用户，使用搜索。

1.单击导航栏上的搜索图标。

2.输入用户 Id 并按回车键。

[![image](img/c036bdfa02cff59cc896955af4689772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---TVy-hEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb77.png)

## 更新通道令牌

如果您更新频道令牌，或者在不同的 bot 中管理丰富的菜单，您可以更新频道令牌

1.单击导航栏上的齿轮图标。

2.输入频道令牌，然后单击确定。

一旦您将丰富的菜单链接到用户，您就可以通过物理设备或模拟器立即看到它。

# 用模拟器检查

你可以在这里找到模拟器[的细节。](https://dev.to/kenakamu/boost-line-bot-development-productivity-by-using-line-client-simulator--12o2)

## 模拟丰富菜单

1.运行模拟器并点击丰富的菜单图标。

[![image](img/c33f2b4aad88358e47bc922f63410411.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JhDqFL3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb78.png)

2.轻按丰富菜单的任何区域，查看其工作方式。

[![image](img/35651527de6be27be8856a319c94b924.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n9sxUpYs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb79.png)

3.当您点击设置回发操作的区域时，会显示以下屏幕截图。

[![image](img/a368dd2d0957693154b52ba4be54c7a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HQ9s91Xr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msdnshared.blob.core.windows.net/media/2018/01/image_thumb80.png)

# 总结

使用丰富的菜单大大提升了 UX！RichMenus Manager 将帮助您管理它们。

肯恩(男名)