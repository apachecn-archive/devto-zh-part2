# 带 Vue 和 GSAP - WotW 的动画卡片滑块

> 原文：<https://dev.to/ederchrono/animated-card-slider-with-vue--gsap---wotw-45bn>

这是本周系列的**小部件的第三部分。**

今天我将向你展示使用 Vue 从头开始制作一个风格化的**卡片滑块**的过程。

这个小工具的灵感来自于 [this](https://uimovement.com/ui/5332/ticket-booking/) ，看起来像这样:

[![slider](img/cd7414eaf7eeb64d92bbf7fe12a456cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---TvCKdIT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mrv7rrg33h7hl0h6ltgp.gif)

### 制剂

类似于[上一个小部件](https://dev.to/ederchrono/making-a-submit-button-with-loader---wotw-2ma)，今天的小部件我们将使用 [vue.js](https://vuejs.org/) 进行交互，使用 [tweenlite](https://greensock.com/tweenlite) 进行动画。

### HTML 结构

基本上滑块的元素是**卡片**和**信息容器**，我将开始添加它们和一些类，以便能够在下一步对它们进行样式化:

```
<div id="slider" class="slider">
  <div class="slider-cards">
    <div class="slider-card"></div>
    <div class="slider-card"></div>
    <div class="slider-card"></div>
  </div>
  <div class="slider-info">
    <h1>Title</h1>
    <p>description</p>
    <button>Action</button>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 造型！

现在它看起来一点也不像最终产品。首先，我将使用以下规则模拟移动视口:

```
.slider {
  overflow: hidden;
  background-color: #1F1140;
  width: 360px;
  height: 640px;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于卡片，我将在容器中使用一个边距来使第一张卡片居中，然后卡片将通过右边距彼此分开。我们还需要 cards 容器是相对的，并且在`slider-info` div 的顶部有一个 z-index。

卡片应该`inline`放在一起，这样它们就可以放在一起，但是为了方便起见，容器应该足够宽。在这种情况下，每张卡片大约 300 像素宽，因此容器将是 900 像素宽，因为我们有 3 张卡片(如果我们有更多的卡片，我们将需要计算所需的总宽度)。

最后，我们将添加一个方框阴影，给人以卡片漂浮的印象。

```
.slider-cards {
  position: relative;
  width: 900px;
  margin: 20px 50px;  
  z-index: 1;
}
.slider-card {
  display: inline-block;
  background-color: grey;
  overflow: hidden;
  width: 260px;
  height: 360px;
  margin-right: 30px;
  border-radius: 12px;
  box-shadow:0px 60px 20px -20px rgba(0, 0, 0, 0.3)
} 
```

Enter fullscreen mode Exit fullscreen mode

[![halfway](img/acebf096ed89c278f43bb0f31773cb38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YcuaI1p3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eb91zo2auxjb47ct89cn.png) 我们越来越近了

现在轮到`slider-info`改头换面了。我们将添加一个背景颜色，尺寸和空白的信息。

重要的是，它与卡片容器重叠，为了做到这一点，`margin-top`将是负的，为了补偿我们添加了一些`padding-top`。

我们需要确保`overflow`属性是*隐藏的*，以使底部的按钮和信息容器有相同的圆角。之后就是用下面的方式设计标题、描述和按钮:

```
.slider-info {
  position: relative;
  overflow: hidden;
  background-color: white;
  margin-top: -200px;
  margin-left: 30px;
  padding: 200px 20px 0;
  width: 260px;
  height: 200px;
  text-align: center;
  border-radius: 8px;
}
.slider-info h1 {
  font-family: Arial Black, Gadget, sans-serif;
  line-height: 25px;
  font-size: 23px;
}
.slider-info p {
  font-family: Arial, Helvetica, sans-serif;
}
.slider-button {
  position: absolute;
  width: 100%;
  height: 50px;
  bottom: 0;
  left: 0;
  border: none;
  color: white;
  background-color: #E71284;
  font-size: 18px;
  font-family: Arial, Helvetica, sans-serif;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![style done](img/623d021a25d74be6142093f9bb262825.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gwBTVyM0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lp48mdalohqksk75j6et.png) 
好多了。

### 用数据填充

我们已经准备好开始使用 Vue，让我们创建一个实例，并从[电影数据库](https://www.themoviedb.org/) :
设置一些数据

```
new Vue({
  el: '#slider',
  data: {
    slides: [
      {
        title: 'Ready Player One',
        description: 'When the creator of a popular video game system dies, a virtual contest is created to compete for his fortune.',
        image: 'https://image.tmdb.org/t/p/w300_and_h450_bestv2/pU1ULUq8D3iRxl1fdX2lZIzdHuI.jpg'
      },
      {
        title: 'Avengers: Infinity War',
        description: 'As the Avengers and their allies have continued to protect the world from threats too large for any...',
        image: 'https://image.tmdb.org/t/p/w300_and_h450_bestv2/7WsyChQLEftFiDOVTGkv3hFpyyt.jpg'
      },
      {
        title: 'Coco',
        description: 'Despite his family’s baffling generations-old ban on music, Miguel dreams of becoming an accomplished musician...',
        image: 'https://image.tmdb.org/t/p/w300_and_h450_bestv2/eKi8dIrr8voobbaGzDpe8w0PVbC.jpg'
      }
    ]
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在为了能够显示数据，我们需要定义默认选择的电影。这可以通过我们数据中的另一个变量`selectedIndex`和一个计算属性来实现，该属性可以根据所选的索引为我们提供幻灯片中的数据:

```
 data: {
    // ... slide data
    selectedIndex: 0
  },
  computed: {
    selectedSlide () {
      return this.slides[this.selectedIndex]
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的模板中，我们将使用`v-for`绑定卡片，并将信息绑定到相应的数据:

```
<div id="slider" class="slider">
  <div class="slider-cards">
    <div 
         v-for="(slide, index) in slides" 
         :key="index"
         class="slider-card">
      <img :src="slide.image" :alt="slide.title">
    </div>
  </div>
  <div class="slider-info">
    <h1>{{selectedSlide.title}}</h1>
    <p>{{selectedSlide.description}}</p>
    <button class="slider-button">BOOK</button>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![almost finished](img/5d2bc90ffa35c1bc7cb8875aa2d1414b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKQ09st_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t6aga9fq6qop3i4b32t.png) 
这个看起来差不多完成了，至少在美学上是这样，但我们仍然需要...

### 相互作用

如果我们分解滑块的交互，当我们按下卡片，移动卡片和放开卡片时，它们基本上是 3。为了跟踪这些动作，我们需要将`@mouseDown`、`@mouseUp`和`@mouseMove`绑定到 Vue 实例中的方法。也是为了防止图像传到*鬼*他们应该拥有的属性`draggable=false`。

```
<div id="slider" class="slider" @mouseMove="mouseMoving">
  <div class="slider-cards">
    <div @mouseDown="startDrag"
         @mouseUp="stopDrag"
         v-for="(slide, index) in slides" 
         :key="index"
         class="slider-card">
      <img :src="slide.image" :alt="slide.title" draggable="false">
    </div>
  </div>
  <!-- slider info and the rest --> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在 Vue 端创建这些方法，我们还将在数据对象中添加几个变量:

```
 data: {
    // ... other variables
    dragging: false,
    initialMouseX: 0,
    initialCardsX: 0,
    cardsX: 0
  },
  methods: {
    startDrag (e) {

    },
    stopDrag () {

    },
    mouseMoving (e) {

    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这三个方法都接收一个事件(在这种情况下我们称之为`e`)，但是我们只需要在`startDrag`和`mouseMoving`方法中使用它。
在接下来的代码片段中，我将一步一步地分解填充这 3 个方法的过程，因此我将忽略其余的代码。

首先我们需要根据鼠标动作
将`dragging`设置为*真*或*假*

```
startDrag (e) {
  this.dragging = true
},
stopDrag () {
  this.dragging = false
},
mouseMoving (e) {

} 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，现在我们希望只有在拖动卡片时才能移动它们，所以在`mouseMoving`方法中我们将添加这个条件:

```
startDrag (e) {
  this.dragging = true
},
stopDrag () {
  this.dragging = false
},
mouseMoving (e) {
  if(this.dragging) {

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在事情变得有趣了，当我们开始拖动时，我们需要跟踪卡片和鼠标的位置，属性`pageX`将告诉我们鼠标的位置，数据中的`cardsX`将跟踪卡片的容器位置:

```
startDrag (e) {
  this.dragging = true
  this.initialMouseX = e.pageX
  this.initialCardsX = this.cardsX
},
stopDrag () {
  this.dragging = false
},
mouseMoving (e) {
  if(this.dragging) {

  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在存储了卡片和鼠标的初始 X 之后，我们可以通过计算鼠标位置差来推断卡片容器的目标位置，此时`mouseMoving`方法执行如下:

```
startDrag (e) {
  this.dragging = true
  this.initialMouseX = e.pageX
  this.initialCardsX = this.cardsX
},
stopDrag () {
  this.dragging = false
},
mouseMoving (e) {
  if(this.dragging) {
    const dragAmount = e.pageX - this.initialMouseX
    const targetX = this.initialCardsX + dragAmount
    this.cardsX = targetX
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件几乎可以移动了，我们只需要找到一种方法将卡片的容器绑定到`cardsX`属性，这可以通过将该属性添加到 HTML:
来完成

```
...
<div class="slider-cards" :style="`transform: translate3d(${cardsX}px,0,0)`">
... 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问“为什么你使用 translate3d 而不是普通的 2d translate？”，原因是`translate3d`是*的硬件加速*，而且大部分时候都有更好的性能。你可以在[这个网站](https://jsperf.com/translate3d-vs-xy)自行查询。

幻灯片现在正在移动，但是有一个小问题，当我们放开它们时，它们会停留在我们放下它们的地方，而且电影信息不会改变。我们实际上需要的是让他们找到最近的幻灯片并将其居中。

为了找到最近的幻灯片，我们只需要将当前位置除以卡片的宽度，然后将结果四舍五入。然后用 TweenLite 我们将动画卡片到相应的位置:

```
stopDrag () {
  this.dragging = false

  const cardWidth = 290
  const nearestSlide = -Math.round(this.cardsX / cardWidth)
  this.selectedIndex = Math.min(Math.max(0, nearestSlide), this.slides.length -1)
  TweenLite.to(this, 0.3, {cardsX: -this.selectedIndex * cardWidth})
} 
```

Enter fullscreen mode Exit fullscreen mode

为了更好地理解这个公式，这张 gif 展示了`cardsX`值如何与`nearestSlide`相关联。
T3![nearestSlide](img/ec9926132e35b05d14ba5edb1a2dc4b6.png)T5】

而现在最后的结果！

[https://codepen.io/ederdiaz/embed/LmPwez?height=600&default-tab=result&embed-version=2](https://codepen.io/ederdiaz/embed/LmPwez?height=600&default-tab=result&embed-version=2)

现在它只能在桌面设备上运行，但是这个问题可能会用`vue-touch`解决，你可以在[这篇文章](https://alligator.io/vuejs/vue-touch-events/)中了解更多

这就是本周第三个**小部件**的内容。

如果你没有检查过上一个，[这里是](https://dev.to/ederchrono/making-a-submit-button-with-loader---wotw-2ma)。

另外，如果你想看下周的某个小部件，可以在评论区发表。