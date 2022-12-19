# 带有 javascript 的动画精灵表

> 原文：<https://dev.to/martyhimmel/animating-sprite-sheets-with-javascript-ag3>

让我们看看如何使用 JavaScript 在 HTML5 画布上制作一个 sprite 表单的动画。

### 一点设置

首先，让我们创建画布元素。

```
<canvas width="300" height="200"></canvas> 
```

Enter fullscreen mode Exit fullscreen mode

添加一个边框(这样我们可以看到我们的可用区域)。

```
canvas {
  border: 1px solid black;
} 
```

Enter fullscreen mode Exit fullscreen mode

并加载精灵工作表([https://opengameart.org/content/green-cap-character-16x18](https://opengameart.org/content/green-cap-character-16x18))。当我们这样做的时候，让我们进入画布和它的 2D 背景。

```
let img = new Image();
img.src = 'https://opengameart.org/sites/default/files/Green-Cap-Character-16x18.png';
img.onload = function() {
  init();
};

let canvas = document.querySelector('canvas');
let ctx = canvas.getContext('2d');

function init() {
  // future animation code goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

在加载图像后，通过`img.onload`调用`init`函数。这是为了确保在我们尝试使用它之前加载图像。所有的动画代码都将放入`init`函数中。对于本教程来说，这是可行的。如果我们要处理多个图像，我们可能希望在对它们做任何事情之前，使用承诺来等待所有图像都被加载。

### 精神层面

现在我们已经设置好了，让我们看一下图像。

[![Character sprite sheet](../Images/ee21d1b5dd26cb98d3d4bad0c77e9a8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gNVrp7Tw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://opengameart.org/sites/default/files/Green-Cap-Character-16x18.png)

每行代表一个动画周期。第一(顶)行是向下行走的角色，第二行是向上行走，第三行是向左行走，第四(底)行是向右行走。从技术上来说，左栏是一个站立的(没有动画)，而中间和右边的栏是动画帧。不过，我认为我们可以用这三个来制作更流畅的行走动画。😊

### 上下文的`drawImage`方法

在我们制作图像动画之前，让我们看看`drawImage` context 方法，因为这是我们用来自动分割 sprite 工作表并将其应用到画布上的方法。

[MDN docs - drawImage](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/drawImage)

哇，这个方法有很多参数！尤其是第三种形式，也是我们将要使用的形式。别担心，事情没有看上去那么糟。它有一个逻辑分组。

```
drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight) 
```

Enter fullscreen mode Exit fullscreen mode

`image`参数是源图像。接下来的四个(`sx`、`sy`、`sWidth`和`sHeight`)与源图像 sprite 工作表相关。最后四个(`dx`、`dy`、`dWidth`和`dHeight`)与目的地相关——画布。

“x”和“y”参数(`sx`、`sy`、`dx`、`dy`)分别与子画面(源)和画布(目标)的起始位置相关。它本质上是一个网格，左上角从(0，0)开始，向右下方正向移动。换句话说，(50，30)向右 50 像素，向下 30 像素。

“宽度”和“高度”参数(`sWidth`、`sHeight`、`dWidth`和`dHeight`)指的是子画面和画布的宽度和高度，从它们各自的“x”和“y”位置开始。让我们把它分成一个部分，比如说源图像。如果源参数(`sx`、`sy`、`sWidth`、`sHeight`)为(10，15，20，30)，则起始位置(在网格坐标中)将为(10，15)并延伸至(30，45)。然后终点坐标计算为(`sx` + `sWidth`，`sy` + `sHeight`)。

### 绘制第一帧

现在我们已经讨论了`drawImage`方法，让我们实际看看它的运行情况。

我们的 sprite 工作表的字符帧大小被方便地标记在文件名(`16x18`)中，这样就给出了我们的宽度和高度属性。第一帧将从(0，0)开始，到(16，18)结束。让我们把它画到画布上。我们将从画布上的(0，0)开始绘制这个帧，并保持比例。

```
function init() {
  ctx.drawImage(img, 0, 0, 16, 18, 0, 0, 16, 18);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/yEGZpd?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/yEGZpd?height=600&default-tab=result&embed-version=2)

我们有了第一个相框！虽然有点小。让我们把它放大一点，以便更容易看到。

将以上内容改为:

```
const scale = 2;
function init() {
  ctx.drawImage(img, 0, 0, 16, 18, 0, 0, 16 * scale, 18 * scale);
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到画布上绘制的图像在水平和垂直方向上的大小都增加了一倍。通过改变`dWidth`和`dHeight`的值，我们可以在画布上放大或缩小原始图像。不过，在这样做的时候要小心，因为你在处理像素，它会很快开始变得模糊。尝试改变`scale`值，看看输出是如何变化的。

### 下一帧

要绘制第二个帧，我们唯一需要做的事情是更改源集合的一些值。具体来说，`sx`和`sy`。每个框架的宽度和高度都是相同的，所以我们永远不需要改变这些值。实际上，让我们把这些值提取出来，创建几个缩放值，然后在当前帧的右边画下两帧。

```
const scale = 2;
const width = 16;
const height = 18;
const scaledWidth = scale * width;
const scaledHeight = scale * height;

function init() {
  ctx.drawImage(img, 0, 0, width, height, 0, 0, scaledWidth, scaledHeight);
  ctx.drawImage(img, width, 0, width, height, scaledWidth, 0, scaledWidth, scaledHeight);
  ctx.drawImage(img, width * 2, 0, width, height, scaledWidth * 2, 0, scaledWidth, scaledHeight);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是它现在的样子:

[https://codepen.io/martyhimmel/embed/yEGZRB?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/yEGZRB?height=600&default-tab=result&embed-version=2)

现在我们有了 sprite 表的整个顶行，但是在三个独立的框架中。如果您查看一下`ctx.drawImage`调用，现在只有 4 个值发生变化- `sx`、`sy`、`dx`和`dy`。

让我们稍微简化一下。现在，让我们开始使用 sprite 表中的帧数，而不是处理像素。

将所有的`ctx.drawImage`呼叫替换为:

```
function drawFrame(frameX, frameY, canvasX, canvasY) {
  ctx.drawImage(img,
                frameX * width, frameY * height, width, height,
                canvasX, canvasY, scaledWidth, scaledHeight);
}

function init() {
  drawFrame(0, 0, 0, 0);
  drawFrame(1, 0, scaledWidth, 0);
  drawFrame(0, 0, scaledWidth * 2, 0);
  drawFrame(2, 0, scaledWidth * 3, 0);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`drawFrame`函数处理 sprite 工作表数学，所以我们只需要传入帧数(从 0 开始，像一个数组，所以“x”帧是 0、1 和 2)。

画布的“x”和“y”值仍然采用像素值，因此我们可以更好地控制字符的位置。在函数内移动`scaledWidth`乘数(即`scaledWidth * canvasX`)意味着每次移动/改变整个缩放字符宽度。比方说，如果角色每帧移动 4 或 5 个像素，那么行走动画就不会这样。所以我们让它保持原样。

在那个`drawFrame`呼叫列表中还有一行。这是为了展示我们的动画周期将会是什么样子，而不仅仅是绘制 sprite 表的前三帧。不是动画循环重复“左一步，右一步”，而是会重复“立，左，立，右”——是稍微好一点的动画循环。两者都可以——许多 80 年代的游戏使用两步动画。

这是我们目前的情况:

[https://codepen.io/martyhimmel/embed/RJEvvy?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/RJEvvy?height=600&default-tab=result&embed-version=2)

### 让我们来动画这个角色吧！

现在我们已经准备好为我们的角色制作动画了！让我们看看 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)中的`requestAnimationFrame`。

这就是我们将用来创建我们的循环。我们也可以使用`setInterval`，但是`requestAnimationFrame`已经有了一些很好的优化，比如以每秒 60 帧的速度运行(或者尽可能的接近)以及当浏览器/标签失去焦点时停止动画循环。

本质上，`requestAnimationFrame`是一个递归函数——为了创建我们的动画循环，我们将从作为参数传递的函数中再次调用`requestAnimationFrame`。大概是这样:

```
window.requestAnimationFrame(step);

function step() {
  // do something
  window.requestAnimationFrame(step);
} 
```

Enter fullscreen mode Exit fullscreen mode

在`walk`函数开始循环之前的唯一一个调用，然后它在内部被连续调用。

在开始使用它之前，我们需要了解和使用另一种上下文方法- `clearRect` ( [MDN docs](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/clearRect) )。当在画布上绘图时，如果我们一直在同一个位置调用`drawFrame`,它会一直在已经存在的内容上绘图。为了简单起见，我们将在每次绘制之间清除整个画布，而不仅仅是我们要绘制的区域。

因此，我们的绘制循环将类似于 clear，绘制第一帧，clear，绘制第二帧，依此类推。

换句话说:

```
ctx.clearRect(0, 0, canvas.width, canvas.height);
drawFrame(0, 0, 0, 0);
// repeat for each frame 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们来制作这个角色的动画吧！让我们为循环(0，1，0，2)创建一个数组，并记录我们在循环中的位置。然后我们将创建我们的`step`函数，它将作为主动画循环。

step 函数清除画布，绘制框架，在循环中前进(或重置)我们的位置，然后通过`requestAnimationFrame`调用它自己。

```
const cycleLoop = [0, 1, 0, 2];
let currentLoopIndex = 0;

function step() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawFrame(cycleLoop[currentLoopIndex], 0, 0, 0);
  currentLoopIndex++;
  if (currentLoopIndex >= cycleLoop.length) {
    currentLoopIndex = 0;
  }
  window.requestAnimationFrame(step);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让动画开始，让我们更新`init`函数。

```
function init() {
  window.requestAnimationFrame(step);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/mKvXxB?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/mKvXxB?height=600&default-tab=result&embed-version=2)

那个角色发展得很快！😂

### 那里慢点！

看来我们的性格有点失控了。如果浏览器允许，字符将以每秒 60 帧的速度绘制，或者尽可能接近。让我们对其进行限制，使其每 15 帧步进一次。我们需要记录我们在哪一帧上。然后，在`step`函数中，我们将在每次调用时推进计数器，但只在 15 帧过去后绘制。一旦 15 帧过去，重置计数器，并绘制帧。

```
const cycleLoop = [0, 1, 0, 2];
let currentLoopIndex = 0;
let frameCount = 0;

function step() {
  frameCount++;
  if (frameCount < 15) {
    window.requestAnimationFrame(step);
    return;
  }
  frameCount = 0;
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawFrame(cycleLoop[currentLoopIndex], 0, 0, 0);
  currentLoopIndex++;
  if (currentLoopIndex >= cycleLoop.length) {
    currentLoopIndex = 0;
  }
  window.requestAnimationFrame(step);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/bKzvqP?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/bKzvqP?height=600&default-tab=result&embed-version=2)

好多了！

### 另一个方向

到目前为止，我们只处理了向下的方向。我们稍微修改一下动画，让角色在每个方向做一个完整的 4 步循环怎么样？

记住,“下”帧在我们代码的第 0 行(sprite 工作表的第一行),上是第 1 行，左是第 2 行，右是第 3 行(sprite 工作表的底行)。对于每一行，循环保持为 0，1，0，2。因为我们已经在处理周期变化，我们唯一需要改变的是行号，这是`drawFrame`函数的第二个参数。

我们将添加一个变量来跟踪我们当前的方向。为了简单起见，我们将按照 sprite 工作表的顺序(下、上、左、右)进行，因此它是连续的(0、1、2、3、重复)。

当周期重置时，我们将转向下一个方向。一旦我们走完了每一个方向，我们将重新开始。因此，我们更新后的`step`函数和相关变量如下所示:

```
const cycleLoop = [0, 1, 0, 2];
let currentLoopIndex = 0;
let frameCount = 0;
let currentDirection = 0;

function step() {
  frameCount++;
  if (frameCount < 15) {
    window.requestAnimationFrame(step);
    return;
  }
  frameCount = 0;
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawFrame(cycleLoop[currentLoopIndex], currentDirection, 0, 0);
  currentLoopIndex++;
  if (currentLoopIndex >= cycleLoop.length) {
    currentLoopIndex = 0;
    currentDirection++; // Next row/direction in the sprite sheet
  }
  // Reset to the "down" direction once we've run through them all
  if (currentDirection >= 4) {
    currentDirection = 0;
  }
  window.requestAnimationFrame(step);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/VdgXXB?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/VdgXXB?height=600&default-tab=result&embed-version=2)

我们做到了！我们的角色在所有四个方向行走，全部由一个图像驱动。