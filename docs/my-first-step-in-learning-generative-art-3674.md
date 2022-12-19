# 我学习生成艺术的第一步

> 原文：<https://dev.to/ohbarye/my-first-step-in-learning-generative-art-3674>

即使我绝对是一个生成艺术的新手(我今天才开始！)，我发现它相当迷人。所以我在这里讲述我的激情。

## 什么是生成艺术？

让我们看看维基百科的引文。

> 生成艺术是指通过使用一个自主系统而全部或部分被创造出来的艺术。
> 
> “生成艺术”通常被用来指算法艺术(由算法决定的计算机生成的艺术作品)。
> [https://en.wikipedia.org/wiki/Generative_art](https://en.wikipedia.org/wiki/Generative_art)

当我了解生成艺术时，我觉得这可能是一个用我的编程技巧来完善我贫乏的审美感的机会。

如果你想现在就尝试，有一个交互式在线编辑器。访问[https://alpha.editor.p5js.org/](https://alpha.editor.p5js.org/)，然后复制&粘贴我的代码到页面上的编辑器中，看看会发生什么。

* * *

## 艺术品

称它们为“艺术品”很尴尬...但是我来介绍一下他们，说说我经历过的事情。

### 【干扰物体】

这是我的第一个生成艺术，没有数学的味道吗？

[![image](../Images/3b84d6c89e2e03c29b05c6e06876ff4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NsKTwl5p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39439337-37e7728c-4ce2-11e8-88aa-fd29456b2343.png)

虽然可能会有一些不熟悉的函数，但是创建这个图片的是熟悉的 JavaScript 代码，它使用了 [p5.js](https://p5js.org/) 。

```
function setup() {
  createCanvas(400, 400);
  background(80)
  noStroke();
  frameRate(6);
}

function draw() {
  for (var i = 0; i < 400; i++) {
    var r = random(0,200);
    stroke(r * 1);
    line(0, i, r, r);
    line(400, i, 50 + r, 100+r);
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

### “黑暗中的光明之路”

[![light](../Images/f4915fe87018c691b14249d62628a66d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--joBV99uw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/39439343-3c6cdf5e-4ce2-11e8-9196-1b0df5dbf894.gif)T3】

```
function setup() {
  createCanvas(720, 400);
  background(80)
  noStroke();
}

function draw() {
  fill(color(random(20),random(200),random(200), 1));   
  bezier(random(500, 550), random(500, 550), 0, 0, 40, 40, 50, 90);
  bezier(random(100, 200), random(100, 350), 0, 0, 40, 40, 50, 90);
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

好吧，我知道我上面的作品不是什么了不起的东西。但我会继续训练，继续发表，让自己成为一个擅长生成艺术的人。

此外，如果 dev.to 能找到更多有编程背景的艺术家，我会很高兴！