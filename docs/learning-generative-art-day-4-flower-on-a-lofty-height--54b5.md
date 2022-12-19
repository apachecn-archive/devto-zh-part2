# 学习生成艺术；第四天“高处之花”🌻

> 原文：<https://dev.to/ohbarye/learning-generative-art-day-4-flower-on-a-lofty-height--54b5>

延续[我学习生成艺术的第一步](https://dev.to/ohbarye/my-first-step-in-learning-generative-art-3674)。

## “花在高处”

[![blue](../Images/ac7efa7ee71b599234e4f3d964958b95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJr84RUH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j3tjpk231kdsk0icf1yk.png)
[![yellow](../Images/1cfc7edeb389bbb8d50092961864b272.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--NmHv5X34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4twrtrj47b3jtyl33i0b.png)

我画了一幅画，表现一朵高高在上的花；我想知道是否有一种花在一天之内会变色。

```
function setup() {
  createCanvas(800, 700);
  // background(48);
  petals();
  stalk();
}

function petals() {
  // noStroke();

  for (var i = 0; i < 200; i++) {
    var r = random(600);
    // fill(color(random(200, 255), random(200, 255), random(255), 78)) // yellow
    fill(color(random(255), random(200, 210), random(200, 255), 78)) // blue

    beginShape();
    curveVertex(0, 0);
    curveVertex(400, 350);
    curveVertex(random(400), random(600));
    curveVertex(random(400), random(400));
    endShape();
  }
}

function stalk() {
  for (var i = 0; i < 30; i++) {
    fill(color(144, random(200, 255), 144, 60));
    beginShape();
    curveVertex(400, i);
    curveVertex(400, 350);
    curveVertex(800, 700);
    curveVertex(random(500), random(700));
    endShape();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 到

`curveVertex`功能可以绘制复杂曲线。我还是很难掌握。然而，这样的曲线是绘制自然界中任何东西所必需的。

* * *

我开始在一个很酷的托管网络服务上发布我的作品，也开始了 OpenProcessing。