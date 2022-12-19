# 硬件+协作艺术

> 原文：<https://dev.to/terceranexus6/hardware--collaborative-art-400h>

不久前，我被要求做一个包含艺术的硬件项目给高中生看。我首先想到的是创建一个电子壁画，但我喜欢更好的协作工作，所以我受到了分布式账本系统的启发。这个想法是有一堆节点，每个节点都有空间用于绘图和电子随机配件，如 led 灯。他们也有导电材料和电线。这个想法是让学生在“*节点*中画东西，然后要求另一个“*节点*连接到他们的节点。当连接时，节点中的电子设备激活，因此他们在同一工作中合作，而不是离开他们的艺术个体。结果将是一幅充满不同绘画风格和电子设备的**大壁画**。我还买了一堆半透明纸，这样我们可以把*折纸*放在 led 灯里，让它看起来更可爱。

[![](../Images/14dc1cd08a1f5c5cd36d28895a155a02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VxAm-WQG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a2j9amrzx32qwph2t0uu.jpg)
[![](../Images/1dd1086f56a34b7ca7ac3f7921814159.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hvn6Wg4L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r0ofuinvazffo1v87qa7.jpg)
[![](../Images/da242c79781acbbf48e0f538990d37b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6NLsYU9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxe53hgv1dz0y1br7xum.jpg)

这是其中一个节点的 RGB led 引脚的基本代码，如 [adafruit](https://learn.adafruit.com/adafruit-arduino-lesson-3-rgb-leds?view=all) :
中所述

```
//rgb_led.ino

int redPin= 7;
int greenPin = 6;
int bluePin = 5;

void setup() {
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
}
void loop() {
  setColor(255, 0, 0); // Red Color
  delay(1000);
  setColor(0, 255, 0); // Green Color
  delay(1000);
  setColor(0, 0, 255); // Blue Color
  delay(1000);
  setColor(255, 255, 255); // White Color
  delay(1000);
  setColor(170, 0, 255); // Purple Color
  delay(1000);
}
void setColor(int redValue, int greenValue, int blueValue) {
  analogWrite(redPin, redValue);
  analogWrite(greenPin, greenValue);
  analogWrite(bluePin, blueValue);
} 
```

Enter fullscreen mode Exit fullscreen mode

作为一个额外的点，我还做了一些石墨导电涂料，学生们可以用它来实验绘画+发光二极管。我已经和我 12 岁的学生试过了，他非常喜欢。

这是电容的代码，我们在[类](https://github.com/terceranexus6/clases_con_rafa/blob/master/apuntes_curso_nuevo/clase_arduino_capacitivo.md) :
中制作的

```
//graphite.ino

#include <CapacitiveSensor.h>
CapacitiveSensor capSensor = CapacitiveSensor(4,2);

int threshold = 1000;
const int ledPin = 12;

void setup() {

    Serial.begin(9600);
    pinMode(ledPin, OUTPUT);

}

void loop() {

    long sensorValue = capSensor.capacitiveSensor(30);
    Serial.println(sensorValue);
    if(sensorValue > threshold) {
        digitalWrite(ledPin, HIGH);
    }
    else {
        digitalWrite(ledPin, LOW);
    }
    delay(10);

} 
```

Enter fullscreen mode Exit fullscreen mode

**为什么物理节点用于协作？**我的父亲是一名心理学家，不久前他向我解释说，触摸可以更好地理解，例如，当蹒跚学步的孩子试图在简单的拼图中放置不同的图形(三角形、圆形等)时。为此，例如，存在用于教学编程的幼儿园玩具，其仅在基本编程逻辑中工作，不涉及数学或单词，只有机器人和方向(右、左、直)指示。这个是一样的，为了更好的理解硬件和协同账本，我觉得做一个物理性能比较容易。

我的目标是上传 github 的[开放库中的每一个细节，它仍在进行中，尽管，原谅它现在的低质量。](https://github.com/terceranexus6/hardwart)