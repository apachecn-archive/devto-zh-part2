# 护栏:来自微控制器的虚拟现实

> 原文：<https://dev.to/djfalcon23/guardrails-vr-from-a-microcontroller-5b4j>

* * *

2017 年 12 月，我想到了一个想法，让一个虚拟世界源于一个微控制器，ESP8266。大约在这个时候，我的家乡巴尔的摩即将举办第一个独立网络夏令营，我想有一个项目来做并展示一下。于是，我开始头脑风暴；“对我来说，什么是酷的、独特的、有趣的？”我想知道。

[![alt text](../Images/ea7f268fd52506b9bc4951444864ee89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fZfD3IL0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://derekfields.is/Assets2/NodeMCU_Pinout.png)
*node MCU 的引脚排列图，一个基于 ESP8266 的微控制器板。*

我是一个亲力亲为的人，所以，这个项目必须要么有大量的视觉效果，要么涉及一些物理的东西，因为如果它没有这些品质，我的兴趣就会很低，我将永远无法完成。打个比方，它必须是我能快速起飞的东西！ESP8266 将是我的问题的答案的主要部分。我知道它可以提供网页，而且它是为物联网设计的，它可以通过这些网页与物理世界互动。“酷”，我记下来了！

现在，是什么让它“[……]对我来说是独特和有趣的？”，…我仍然需要回答这个问题。我环顾我的房间以获得灵感。我的眼睛锁定了几个谷歌纸板，我把它们放在我房间里许多电子产品盒子中的一个盒子里。“谷歌纸板...，VR…，ESP8266…酷，酷，偏向有趣！现在我该如何组合这些东西呢？..."我想知道。“‘A 字架’！嘣！！!"我可以用 A 型框架把这些东西粘在一起。

[![alt text](../Images/036de65a628b421da94c2dfa0e8d73d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9u1GBo0m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.drupal.org/files/project-images/download_6.png) 
*A 字架的 logo*

A-Frame 的徽标直接来自 A-Frame 的网站:

> A-Frame 是一个用于构建虚拟现实(VR)体验的网络框架。最初来自于 Mozilla T2，A-Frame 被开发成一种简单而强大的开发虚拟现实内容的方式。[作为一个独立的开源项目](https://github.com/aframevr/)，A-Frame 已经成长为[最大和最受欢迎的虚拟现实社区之一。](https://aframe.io/community/)
> A-Frame 基于 HTML 之上，入门简单。但是 A-Frame 不仅仅是 3D 场景图或标记语言；核心是一个强大的实体组件框架，为 [three.js.](https://threejs.org/)
> A-Frame 提供声明性、可扩展、可组合的结构，支持大多数 VR 头戴设备，如 Vive、Rift、Daydream、GearVR、Cardboard，甚至可以用于增强现实。虽然 A-Frame 支持整个频谱，但 A-Frame 旨在定义超越基本 360°内容的完全沉浸式互动 VR 体验，充分利用位置跟踪和控制器。

我有了主要的原料，现在我必须用它们做点什么。第一步是将 ESP8266 置于接入点和网络服务器模式。我现在可以编写包含 A 帧 VR 世界的 HTML，并将其放在微控制器上。为了做到这一切，我在 YouTube 上查找了一些教程，并查阅了一些文档。我发现的是:

[https://youtu.be/Q0eUoFIXrWE](https://youtu.be/Q0eUoFIXrWE)
*【使用 Arduino IDE 的 ESP8266 WiFi 接入点示例】作者大卫·沃茨*

* * *

*和我找到的文档:*

> **[服务器- ESP8266 Arduino Core 2.4.0 文档](http://arduino-esp8266.readthedocs.io/en/latest/esp8266wifi/server-examples.html)**
> [在 ESP8266 上设置 web a 服务器只需要很少的代码，而且出奇的简单。这要感谢…arduino-esp8266 . readthedocs . io](http://arduino-esp8266.readthedocs.io/en/latest/esp8266wifi/server-examples.html)

* * *

在视频的描述中，David Watts(视频的创建者)提供了一个链接，指向他创建的将 HTML 代码转换为字符串的工具。这为我节省了大量的时间和挫折。谢谢你，大卫！该工具还允许您指定放置变量的空间。

在这一点上，我可以说这个项目是成功的，但在目前阶段没有乐趣可言。有趣的是在那些为变量指定的空间里，…加入一点 Ajax。我的想法是将传感器连接到 ESP8266，并使用它们的数据来改变或操纵虚拟世界中的某些东西。我可以用光传感器来改变虚拟世界的照明。我可以用加速度计和指南针来摇动和旋转它。当然，反过来也是对的，ESP8266 没有震撼地球的力量，但通过一些其他组件，如伺服系统，也许它可以震撼一块岩石。

###### 这个项目在我们如何互动和使用虚拟空间方面有很大的潜力。它让我兴奋地探索更多！也许我会写第二部分。