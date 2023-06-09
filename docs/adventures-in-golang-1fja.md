# 戈朗历险记

> 原文：<https://dev.to/davidgs/adventures-in-golang-1fja>

[![29437996](img/1eb3b699af134fc8610d2f65f3de83bb.png "29437996.jpg")](https://res.cloudinary.com/practicaldev/image/fetch/s--xyLbajVe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidgs.com/wp-content/uploads/2018/08/29437996.jpg) 我不是 Golang 开发者。我们先把这个问题解决了吧。我在围棋上开发了一些东西，但我不是围棋开发者。我有点需要，但这并不重要。我决定是时候放手一搏，认真对待围棋了。说真的，通过阅读互联网你只能学到这么多。

为此，我采取了两项措施:

1.  下周我要去丹佛的戈弗肯
2.  我从 C 移植了一个库到 Go

这是我今天要在这里写的第二点，不是因为我认为我做得非常好，而是因为它可能对其他人有用，只是为了记录我所做的事情，以防我以后想引用它。

如果你感兴趣，请继续阅读！

## 背景

我一直在做一个小的物联网项目(duh ),它使用了树莓 Pi。它还使用了 Adafruit 的 Bosch BME280 分线板。如果我在 Arduino 上运行它的话，处理起来会非常容易。但我不是。是的，我知道有很多方法可以在 Raspberry Pi 上运行 Arduino 草图，但我真的不是 Arduino 草图的粉丝，所以我决定用另一种方式。

当然，传感器是 I <sup>2</sup> C，所以就有了这个。让 I <sup>2</sup> C 总线在 Raspberry Pi 上可访问很容易(只需运行 raspi-config 和 Bob's your Uncle)，但是处理 I <sup>2</sup> C 设备就有点困难了。我尝试了几个基于 C 的 I <sup>2</sup> C 库，但是大多数都给出了… *意想不到的结果。我发现最接近的是 GitHub 用户“[bit bank 2](https://github.com/bitbank2)”([https://github.com/bitbank2/bme280](https://github.com/bitbank2/bme280))写的，所以我决定用他的。我能够编译它并运行示例程序，至少得到了合理的结果。但是为了得到结果，我仍然必须从一些用户空间程序中调用它。我应该闻到老鼠洞来了，但我当然没有。*

我会把它带走的！当时听起来很合理。

## 移植去

这实际上比我想象的要容易得多。首先，有一个来自 [@rakyll](https://twitter.com/rakyll) 的非常棒的 Go I <sup>2</sup> C 库。我用它来访问 SenseAir K30 CO <sub>2</sub> 传感器，所以我想我应该从那里开始。

因为我开始使用的库可以工作，所以我认为最简单的方法就是做半直接的翻译。我会复制几行 C 代码，然后让它运行。当然，有些事情就是做不好。例如，I <sup>2</sup> C 库想要处理字节和字节片，所以我不能仅仅使用 C 库使用的 int。此外，C 库使用了大量静态全局变量，这在 go 中也不能很好地工作。于是我做了调整:

```
static int calT1,calT2,calT3;
static int calP1, calP2, calP3, calP4, calP5, calP6, calP7, calP8, calP9;
static int calH1, calH2, calH3, calH4, calH5, calH6; 
```

Enter fullscreen mode Exit fullscreen mode

变成了:

```
type BME280 struct {
 Dev *i2c.Device
 tConfig []int
 pConfig []int
 hConfig []int
} 
```

Enter fullscreen mode Exit fullscreen mode

和

```
device.tConfig = make([]int, 3)
device.pConfig = make([]int, 9)
device.hConfig = make([]int, 6) 
```

Enter fullscreen mode Exit fullscreen mode

剩下的几乎就是将 C 语言构造转换成 Golang 构造的简单翻译。

```
// Prepare temperature calibration data
calT1 = ucCal[0] + (ucCal[1] << 8);
calT2 = ucCal[2] + (ucCal[3] << 8);
if (calT2 > 32767) calT2 -= 65536; // negative value
calT3 = ucCal[4] + (ucCal[5] << 8);
if (calT3 > 32767) calT3 -= 65536; 
```

Enter fullscreen mode Exit fullscreen mode

变成了:

```
// time to set up the calibration
 device.tConfig[0] = int(ucCal[0]) + (int(ucCal[1]) << 8)
 device.tConfig[1] = int(ucCal[2]) + (int(ucCal[3]) << 8)
 if device.tConfig[1] > 32767 {
     device.tConfig[1] -= 65536
 }
 device.tConfig[2] = int(ucCal[4]) + (int(ucCal[5]) << 8)
 if device.tConfig[2] > 32767 {
     device.tConfig[2] -= 65536
 } 
```

Enter fullscreen mode Exit fullscreen mode

诸如此类。

现在任何围棋程序都可以简单地做到以下几点:

```
package main
import (
  "fmt"
  "github.com/davidgs/bme280_go"
  "time"
)
func main() {
  dev := "/dev/i2c-1"
  bme := bme280_go.BME280{}
  r := bme.BME280Init(dev)
  if r < 0 {
    fmt.Println("Error")
  }
  rets := bme.BME280ReadValues()
  f := 0.00
  f = float64(rets[0]) / 100.00
  fmt.Println("Temp: ", f)
  f = float64(rets[2]) / 1024.00
  fmt.Println("Humidity: ", f)
  bme.Dev.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

因为对`BME280ReadValues`的调用会返回一个简单的`ints`片段，依次是温度、压力和湿度。**注意**:压力计算目前是破的，不建议使用。

正如我所说的，让这一切工作起来非常容易！我现在已经为 GoLang 的 Adafruit ZBME280 分线板提供了一个功能齐全的库！

接下来是为 SenseAir K30 传感器编写一个类似的库。我已经让传感器正常工作了，我只需要把代码转换成一个库。

如果你有兴趣使用这个库，它可以在我的 GitHub 上免费获得。

《戈朗历险记》这篇文章最早出现在《T2》杂志的《大卫·g·西蒙斯》上。