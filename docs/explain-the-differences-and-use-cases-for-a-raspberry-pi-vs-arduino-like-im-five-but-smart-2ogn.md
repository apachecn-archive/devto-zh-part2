# 解释 Raspberry Pi 和 Arduino 的区别和使用案例，就像我五岁一样(但是很聪明)

> 原文：<https://dev.to/rickssss/explain-the-differences-and-use-cases-for-a-raspberry-pi-vs-arduino-like-im-five-but-smart-2ogn>

我刚刚得到了 Sparkfun 的 Raspberry Pi 3 入门套件和一些额外的基本组件和一个有趣的组件(一个扬声器)。我曾经做过一些 DIY 电子产品的东西，但我当时并不了解电容(平心而论，我的高中老师也不了解)或二极管。

现在我痴迷于智能家居物联网/家庭(以及其他一切)自动化，我最终决定涉足硬件，这样我就可以制作自己的设备。我确信在三大商家的网站上有很多有趣的传感器和组件，所以我打算尝试更多的 DIY 电子产品。所以，我意识到如果我打算继续保持这个爱好，什么是一个重要的问题:

# 主问！

**树莓派和 Arduino 有什么区别？**无论是在*技术细节*还是*用例*或者社区关注的方面。

### 加分题

#### 奖金 1

我依稀记得的 DIY 论坛上的“AVR”和“ATMega”的东西呢？我有种感觉，从高层到低层有一个层次，pi > arduino > AVR/Atmega。

#### 奖金 2

Particle/Blynk/control everything 有什么区别，他们的竞争对手是什么？(即粒子 vs 全息图是我能找到的少数几个“竞争者”之一，因为我不知道粒子是什么——一个协议，一个框架，...？

#### 奖励 3

如果我对构建智能家居/自动化项目感兴趣，我希望将这些项目与 Alexa、Siri / iOS / iPhone、macOS、Google Cast 智能电视、Philips Hue、IFTTT/Stringify/Workflow 等、Wemo 插座开关、HomeKit/HomePod(即将推出)、Android 平板电脑、我打算购买的 Smartthings Hub、NFC/RFID 技术、API 和消息队列、我将制作的其他 DIY 项目以及其他物联网消费设备或“中枢”设备连接起来，我应该朝哪个方向前进？

#### 奖金 4

有没有类似 Arduino 的板可以让我在原始 MIPS 汇编中编码？回到学校会很有趣。

背景信息:我是一名软件工程师，初级全栈，但也喜欢 FP、Haskell 和编译器。我可以写 Python/node . js/Lua/Processing/scripts，不管这些项目需要什么，并且知道 HTTP 和 RabbitMQ，学习 gRPC。因此，虽然我问的是更高层次的抽象，但我在高度定制的 mac 和智能家居设置上做的许多自动化都是在 vps 上运行的节点脚本(目前)。