# 学习生成艺术；第六天“表面波”🏄

> 原文：<https://dev.to/ohbarye/learning-generative-art-day-6-surface-wave--8hc>

延续[我学习生成艺术的第一步](https://dev.to/ohbarye/my-first-step-in-learning-generative-art-3674)。我明天要完成这个系列，所以这是倒数第二个。

## 【表面波】

[![2018-05-05 21 58 21](img/e8edca9b8bd3283156dbf899b3f07ead.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9E3CcZmt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39663562-65bc2758-50b0-11e8-9823-015ac231c1c0.png)

[![2018-05-05 21 57 17](img/d65856d4fb9dabb44b8a444261a772de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UNnIGA80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39663561-65969862-50b0-11e8-8e0f-9a85c14b4a27.png)

[![surface_blue](img/e0d283869446121ba1fc068f4f6d7fc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j2bQnSIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/39663565-6942f44c-50b0-11e8-82e6-d71a5306e1d9.gif)

[![surface_orange](img/42b41242fc2eb806012bc3a154c057a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OhC1arRz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/39663566-696e22de-50b0-11e8-9cec-ccbc440e3b42.gif)

我尝试过用 2D 噪声来表达表面波。它看起来像一个波，也在地球表面。

我可以通过观看一个波来平静下来，不管这个波是电的还是自然的。你呢？

```
var yoff = 0.0;
var backgroudColor;

function setup() {
  createCanvas(1400, 400);
  backgroudColor = 255;
  // backgroudColor = 22;
}

function draw() {
  background(backgroudColor, 2);

  fill(backgroudColor, 2);
  stroke(color(random(22),random(100, 255),random(255), 200));
  // stroke(color(random(222, 255),random(100, 155),random(10), 200));
  strokeWeight(random(4))
  beginShape();

  var xoff = 0;

  for (var x = 0; x <= width; x += 10) {
    var y = map(noise(xoff, yoff), 0, 1, 100, height);
    vertex(x, y);
    xoff += 0.05;
  }

  yoff += 0.01;
  vertex(width, height);
  vertex(0, height);
  endShape(CLOSE);
} 
```

Enter fullscreen mode Exit fullscreen mode