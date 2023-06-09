# 用画布像素化人物，绘制生成艺术！

> 原文：<https://dev.to/ohbarye/pixelate-charactors-with-canvas-and-draw-generative-art-4eb6>

[正如我在](https://dev.to/ohbarye/what-i-felt-through-7-days-of-learning-generative-art-1fka)之前发布的，我正在和 [p5.js](https://p5js.org/) 学习生成艺术。

在这篇文章中，我介绍了一个工具，“字符串像素化”，这是我通过学习过程中。

*   GitHub:[https://github.com/ohbarye/string-pixelater](https://github.com/ohbarye/string-pixelater)
*   NPM:[https://www.npmjs.com/package/string-pixelater](https://www.npmjs.com/package/string-pixelater)

## 什么是字符串像素化器

[![image](img/a23cbe571902557fdb700c4bacff09a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q_K0fcLO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39958038-d70fbda2-5637-11e8-8b03-4b2d7bb21bf4.png)

这是一个简单的工具来像素化任何字符。`pixelate`的意思是“将一个字符转换成一个二维数组”，这样我们就可以出于多种目的来处理表格数据。我实际上是为绘画生成艺术创作这个和 [p5.js](https://p5js.org/)

让我们深入了解它是如何工作的...但是到目前为止它只有一个 API！

```
> StringPixelater.pixelate('hello', {fontSize: 24})

[
  [0,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,1,1,0,0,1,1,1,1,1,0,0,0,0,0,0,0,0,1,1,1,1,1,1,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,1,1,1,1,1,1,0,0,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,1,1,1,1,1,1,1,1,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,1,1,1,1,1,1,1,1,1,0,0,0,0],
  [0,0,1,1,1,1,1,0,0,1,1,1,1,0,0,0,0,1,1,1,0,0,0,1,1,1,1,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,1,1,1,1,0,0,0,1,1,1,1,0,0,0],
  [0,0,1,1,1,1,0,0,0,0,1,1,1,0,0,0,1,1,1,0,0,0,0,0,0,1,1,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,1,1,1,0,0,0,0,0,1,1,1,1,0,0],
  [0,0,1,1,1,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,1,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,1,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,0,0,0,0,0,0,0,0,1,0,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,1,1,1,0,0,0,1,1,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,0,1,1,1,0,0,0,0,1,1,1,1,0,0,0,1,1,0,0,0,1,1,1,0,0,0,1,1,1,1,0,0,0,1,1,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,1,1,1,1,1,1,1,1,1,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,1,1,1,1,1,1,0,0,0,0,0,0,1,1,0,0,0,1,1,1,0,0,0,0,0,1,1,1,1,1,1,1,0,0,0,0,0]
] 
```

Enter fullscreen mode Exit fullscreen mode

你能在上面隐约看到“你好”吗？

## 它是如何工作的？

简而言之，它使用`<canvas>`元素作为临时的*画布*来渲染角色。然后，它提取并解析光栅化的图像数据。它的基本代码如下。

```
const canvas = <HTMLCanvasElement> document.createElement('canvas');
const context = <CanvasRenderingContext2D> this.canvas.getContext('2d');
context.fillText("hello", 0, 0);

let table = new Array(canvas.height);

const imageData = context.getImageData(0, 0, canvas.width, canvas.height);

for (let row = 0; row < canvas.height; row++){
  table[row] = new Array(canvas.width);

  for (let col = 0; col < canvas.width; col++){
    const alpha = imageData.data[(canvas.width * row + col) * 4 + 3];
    if (alpha >= 64) {
      table[row][col] = 1;
    } else {
      table[row][col] = 0;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

只需用一个你想要像素化的字符串安装并调用`StringPixelater.pixelate`方法。

```
$ npm install --save string-pixelater
or
$ yarn add string-pixelater 
```

Enter fullscreen mode Exit fullscreen mode

你可以作为 ES 模块使用或通过`<script>`标签
加载

```
import StringPixelater from 'string-pixelater';

const table = StringPixelater.pixelate('Hello, world'); 
```

Enter fullscreen mode Exit fullscreen mode

```
<script type="text/javascript" src="path/to/dist/js/string-pixelater.js"></script>
<script type="text/javascript">
  var table = StringPixelater.pixelate('Hello, world');
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### `transpose`

如果你像我一样用`p5.js`，`transpose`选项相当方便，因为它有倒轴，

```
> StringPixelater.pixelate('B', {fontSize: 24, transpose: false})
[
  [0,0,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,1,1,1,1,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,0,0,0,0,0,0,0,0,0,1,1,1,0,0],
  [0,0,1,1,1,0,0,0,0,0,0,1,1,1,1,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,1,1,1,0,0,0],
  [0,0,1,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0]
]

> StringPixelater.pixelate('B', {fontSize: 24, transpose: true})
[
  [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
  [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,1,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,1,1],
  [1,1,1,0,0,0,0,1,1,1,0,0,0,0,0,0,1,1,1],
  [1,1,1,1,0,0,1,1,1,1,1,0,0,0,0,0,1,1,1],
  [0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0],
  [0,0,1,1,1,1,1,1,0,0,1,1,1,1,1,1,1,1,0],
  [0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,0,0,0],
  [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
] 
```

Enter fullscreen mode Exit fullscreen mode

当用简单直观的循环渲染时，后者看起来是正确的。

```
// Note: This is just pseudo code!
function draw() {
  const imageData = StringPixelater.pixelate('B', {fontSize: 24, transpose: true})
  const pixelSize = 10;

  imageData.forEach(function(row, i) {
    row.forEach(function(cell, j) {
      if (cell === 1) {
        ellipse(i * pixelSize, j * pixelSize, pixelSize, pixelSize);
      }
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

结果如下所示:

[![2018-05-12 22 49 04](img/5a92a2d993fb240ada765c3c15e7b05b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fMe-q97R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39957847-bd96294a-5634-11e8-93be-9efaac493833.png)

## 用线像素化器的艺术品

就凭`StringPixelater.pixelate('hello')`:

[![2018-05-12 22 49 04](img/7b2e979ec90e3d1d04240dcb7c6f3b91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d-Kmkxdu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39958008-34941f78-5637-11e8-8ec9-271d87e58f76.png)

我们可以得到像素化的表情符号，如`StringPixelater.pixelate('🐈')`。

[![2018-05-12 22 52 36](img/4a02bbde3b9ed74d66fc7e9438d3a35e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--54BMxG8_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/39958009-34b9721e-5637-11e8-80c2-42b5e6661e23.png)

* * *

如果有人通过这篇文章对生成艺术或 [string-pixelater](https://github.com/ohbarye/string-pixelater) 感兴趣，我会很高兴。:)