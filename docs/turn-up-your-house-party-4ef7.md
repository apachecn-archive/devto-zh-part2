# 打开你的家庭聚会！

> 原文：<https://dev.to/gareebcoder/turn-up-your-house-party-4ef7>

[![“Crowd with raised arms in a colorful club in silhouette” by Matty Adame on Unsplash](../Images/7cac848a10f2792e68a4237d80c81584.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bygw8g0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qfsyrqil3133mpj04jte.jpg)

## 你是喜欢聚会的书呆子吗？

好吧，现在是你把你的技能用在工作上的时候了。
用**低音反应 led 条形灯**开启你的家庭派对。为了让你有点兴奋，这里有一个演示..
[https://www.youtube.com/embed/zgcKU52koAg](https://www.youtube.com/embed/zgcKU52koAg)

完成这项工作的程序既简单又便宜，所以，我们开始吧。

#### 你需要什么？

1.  Arduino Uno R3: [在此购买](https://www.amazon.in/Uno-ATmega328P-Compatible-ATMEGA16U2-Arduino/dp/B015C7SC5U/ref=sr_1_4?s=industrial&ie=UTF8&qid=1534869919&sr=1-4&keywords=arduino+uno+r3)
2.  声音传感器:[在这里购买](https://www.amazon.in/Core-Technologies-Sound-Sensor-Module/dp/B01HHYMK7C?tag=googinhydr18418-21&tag=googinkenshoo-21&ascsubtag=657bf44d-1ee0-4eb7-8e33-61abc0ebfe6a)
3.  Led 灯条 12V(含交流电源适配器):[在此购买](https://www.amazon.in/gp/product/B016SMPFI0/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
4.  跳线:[在此购买](https://www.amazon.in/Jumper-Wires-Male-female-Pieces/dp/B00ZYFX6A2/ref=sr_1_3?ie=UTF8&qid=1534876767&sr=8-3&keywords=Jumper+cables)
5.  IRF540 N 沟道功率 MOSFET: [在此购买](https://www.amazon.in/vasp-IRF540-N-Channel-Power-MOSFET/dp/B015WZF0VG/ref=sr_1_1?ie=UTF8&qid=1534876856&sr=8-1&keywords=irf540n)你也可以购买其他 MOSFET，但要大的(TO-220)。
6.  面包板:[在这里购买](https://www.amazon.in/Generic-Elementz-Solderless-Piecesb-Circuit/dp/B00MC1CCZQ/ref=sr_1_3?ie=UTF8&qid=1534876993&sr=8-3&keywords=breadboards)
7.  带超低音扬声器的扬声器:[在此购买](https://www.amazon.in/A110-2-1-Channel-Multimedia-Speakers/dp/B00E2OY9OS/ref=sr_1_9?s=electronics&ie=UTF8&qid=1534882915&sr=1-9&keywords=fenda+speakers)

没有必要坚持我提到的链接，做你口袋里允许的事情。

**注意**:声音传感器拾取低音，因此必须靠近超重低音扬声器。**尽可能靠近**。

#### 电路

[![“Crowd with raised arms in a colorful club in silhouette” by Matty Adame on Unsplash](../Images/b200cb6f5355bf6d8484fefa10a629e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qjf_OQXl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qjdquj9e5ewpxh0cj33z.gif)

现在是时候启动你的项目了！下图显示了所有的连接。为了简单起见，这些是直接连接，实际项目中的导线数量可能会有所不同，因为您将使用试验板，并从字面上连接导线。

使用您的 9 V 墙壁适配器(随 LED 灯条一起提供)为 Arduino 供电(将其插入 Arduino 左下方的黑色组件)

#### Arduino 代码

我假设你知道 Arduino 的基础知识。即使你不想也没问题。这里可以参考初学者指南[。](http://forefront.io/a/beginners-guide-to-arduino/)

如果你还在纠结 Arduino，只需要将声音传感器的 AO 引脚连接到 mosfet 的 Gate 引脚即可。如果没有，那就说代码吧。

```
 #include <SoftwareSerial.h>
#define SS_output 7
#define LEDstrip 3
#define LEDstrip1 5
#define LEDstrip2 6 void setup()
{
  pinMode(SS_output, INPUT); //AO of the Sound Sensor connected to Analog Pin 7
  pinMode(LEDstrip, OUTPUT);
  pinMode(LEDstrip1, OUTPUT);
  pinMode(LEDstrip2, OUTPUT);
}
//This loop gets the sound sensor readings and forwards them to thed LED strip
void loop()
{
  int soundstate = analogRead(SS_output);
    analogWrite(LEDstrip, soundstate);
    analogWrite(LEDstrip1, soundstate);
    analogWrite(LEDstrip2, soundstate);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就把你带到了终点。选首歌放松一下。

如果你喜欢这个，哪天请我喝杯啤酒吧！