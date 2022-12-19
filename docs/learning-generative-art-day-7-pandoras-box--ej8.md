# 学习生成艺术；第七天“潘多拉的盒子”🗃

> 原文：<https://dev.to/ohbarye/learning-generative-art-day-7-pandoras-box--ej8>

延续[我学习生成艺术的第一步](https://dev.to/ohbarye/my-first-step-in-learning-generative-art-3674)。我终于完成了我每天张贴艺术品的七天挑战！

## 《潘多拉的盒子》

[![pandora_black](../Images/54fe04ab6747ce3a3e0338da55329341.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F9brNF05--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39673282-45fec654-5174-11e8-9bff-4bddc22faa07.png)

[![pandora_black](../Images/52812af0a6fd46838c79cefbd259c453.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HvndlzhP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/39673286-49c46a0a-5174-11e8-89f5-5538b981d270.gif)

[![pandora_white](../Images/a99563cfb250418ee5c95d2cdb0caa5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DkE4Buaj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39673283-4622d724-5174-11e8-9f3f-8193340b5a0e.png)

[![pandora_white](../Images/43363c432c04a6f8d20710b5eaa9fc67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UHii8ayP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/39673287-49f28fe8-5174-11e8-8651-f73c0e2ca98a.gif)

我尝试过只用圆形和三角形来表达潘多拉的盒子。

在发表这篇文章时，我应该承认我确实受到了 [evanyou.me](http://evanyou.me/) 的设计和代码的启发。它首先让我想起了闪电；然后我想出了用闪电画潘多拉盒子的主意。

顺便问一下，你是否想知道为什么画的“盒子”不是字面上的盒子，而是一种圆？那我们再来核对一下流言。

> 在最初的故事中提到的容器实际上是一个大的储物罐，但这个词后来被误译为“盒子”。
> [https://en.wikipedia.org/wiki/Pandora%27s_box](https://en.wikipedia.org/wiki/Pandora%27s_box)

没错，那本来就不是一个“盒子”。所以我把它画成一个罐子的形状。好吧，我承认它可能离罐子还很远...😇

```
// Sorry for the quite ugly code...

var f = 60,
  r = 0,
  u = Math.PI * 2,
  v = Math.cos,
  q;

function setup() {
  createCanvas(1000, 400);
  frameRate(10)

  // To capture static screenshot
  // noLoop();
  // for(var i=0; i < 10; i++) { draw() } 
}

function draw() {
  // background(225, 200); // Try this for white background pattern
  background(25, 200);
  drawLightnings();
  drawCircles();
}

function drawLightnings() {
  // stroke(0, 100); // Try this to emphasize lightnings
  noStroke()
  for(var i=0; i < 10; i++) {
    q = [ {x: f, y: height * 0.7 + f}, {x: random(f-10, f+10), y: height * 0.7 - f} ]
    while(q[1].x < width + f) drawTriangle(q[0], q[1])
  }
}

function drawTriangle(i, j, direction){
  r -= u / -50;
  c = (v(r)*127+128<<16 | v(r+u/3)*127+128<<8 | v(r+u/3*2)*127+128).toString(16);

  fill(color(
    parseInt(c.substring(0, 2), 16),
    parseInt(c.substring(2, 4), 16),
    parseInt(c.substring(4, 6), 16),
    200));

  beginShape();
  vertex(i.x, i.y);
  vertex(j.x, j.y);
  var k = j.x + (Math.random()*2-0.25)*f;
  var n = y(j.y);
  vertex(k, n);
  endShape(CLOSE);

  q[0] = q[1];
  q[1] = { x: k, y: n };
}

function y(p){
  var t = p + (Math.random() * 2 - 1.1) * f;
  return (t > height || t < 0) ? y(p) : t;
}

function drawCircles() {
  stroke(255, 200);

  var radius = 10;
  for(var i=0; i < 100; i++) {
    fill(color(random(100, 255), random(100, 255), random(255), 100));
    ellipse(random(f-radius, f+radius), random(height - f -radius, height - f +radius, ), random(50));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode