# 我是如何制作一个笑脸切换控件的——WotW

> 原文：<https://dev.to/ederchrono/how-i-made-a-smiley-toggle-controlwotw-5612>

这是名为**本周小部件**系列的第一篇文章。

顾名思义，我将每 7 天创建一个主要基于 [UI 动作](https://uimovement.com/)的[运球](https://dribbble.com/)的部件。

将一个 **gif** 转换成一个**全功能 HTML 控件**并不像听起来那么简单，所以我也会写下这个过程，并解释解决棘手部分的原因。

事不宜迟，本周小部件是这个**切换控件**:

[![toggle control](../Images/bf078fdc040dd7087685a4940efaced5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--usaYkwpC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bli196srpvgqhdtw5qvs.gif)

### 准备元素

首先，我们需要识别部件的各个部分，乍一看，我们需要一个容器来容纳控件、背景、球和球内的各个面。

几乎所有的事情都可以用 HTML + CSS 完成。面部可以用一些 CSS 魔法来完成，但我认为启动一个应用程序并为它们创建 SVG 节点会更容易。我只是截屏重画了一下:

[![drawing svg](../Images/d4080dd7ee74fa35923a6292eb81d66d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JgA8HIfU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/0%2AGFYGKnXfdUYaIQKz.png)

我们现在有一切可以开始编码。

### HTML 结构

基本结构开始是这样的:

```
<div id="widget" class="center">
  <div class="toggle-container">
    <!-- background -->
    <div class="toggle-bg"></div>

    <div class="ball-face">
      <div class="faces-container">
        <div class="faces-together">
          <!-- happy face svg -->
          
          <!-- sleepy face svg -->
              
        </div>
      </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后我开始给它们添加一些样式，我为背景和球面使用了边界半径，渐变和阴影帮助给球体增加体积。我还必须设置`-webkit-tap-highlight-color`来移除当你点击移动时出现的高亮显示。然后，经过一些试验错误，我定位的面孔 SVG。

```
.center {
  margin-left: calc(50% - 75px);
  margin-top: 50px;
}
.toggle-container {
  position: relative;
  cursor: pointer;
  margin: 20px 0;
  outline:0;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
.toggle-bg {
  background: #D4CFD0;
  width: 150px;
  height: 70px;
  border-radius: 50px;
}
.ball-face {
  position: absolute;
  overflow: hidden;
  left: 7px;
  top: 7px;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: linear-gradient(45deg, #c6c6c6 0%,#ffffff 60%);
  box-shadow: -1px 3px 8px 0px rgba(0,0,0,0.2);
}
.faces-container {
  position: relative;
}
.faces-together {
  position: absolute;  
  left: 0;
}
.happy-face {
  position: absolute;
  top: 18px;  
  left: -120px;
  width: 25px;
}
.sleepy-face {
  position: absolute;
  top: 27px;  
  left: 10px;
  width: 25px;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 交互逻辑

为了让控件工作，我们需要一些 JS 代码，所以我导入了 Vue.js 来快速集成功能。

类似这样的事情足以开始:

```
var widget = new Vue({
  el: '#widget',
  data: {
    active: false
  },
  methods: {
    toggle () {
      this.active = !this.active
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要将 toggle 方法绑定到 HTML 中的 toggle 控件，如下所示:

```
<div id="widget" class="center"> 
  <div @click="toggle" class="toggle-container">]
    // ... rest of html 
```

Enter fullscreen mode Exit fullscreen mode

目前，不会有任何视觉上的变化，但如果我们检查组件，它已经在工作，每次点击切换活动属性。所以接下来要做的是根据属性改变外观。

### 将 CSS 类绑定到 Vue 属性

首先，我需要创建 CSS 规则，当开关处于“活动”状态时应用这些规则，所以我开始创建像
这样的类

```
.ball-face.active {
  left: 87px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将立即移动到球表面的右侧，所以我们需要在我们已经创建的内部进行转换。球面类:

```
.ball-face {
  ... other rules
  transition: left .4s ease-in-out;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将在 0.4 秒内以“进-出”类型的加速度变化(减缓)来激活属性“左”。如果你想知道更多关于 CSS 中的过渡和什么是放松，你可以查看[这一页](https://www.w3schools.com/css/css3_transitions.asp)。

之后，当 Vue 属性为真时，我们需要以某种方式将“active”类附加到 HTML 中，为此，我们需要像这样绑定该类:

```
<div :class="{'active': active}" class="ball-face"> 
```

Enter fullscreen mode Exit fullscreen mode

注意`:class`中的冒号，它有助于绑定 Vue 属性来显示或隐藏‘active’类。该类将追加到已经定义的类“ball-face”中。

现在，我试图对其余的属性做同样的事情。我遇到的唯一问题是，当试图为容器背景制作背景颜色动画时，看起来 CSS 过渡还不支持该属性。我不得不使用两种背景来解决这个问题，一种是灰色的，另一种是彩色的。我只需要制作不透明度的动画，而不是过渡颜色。

对于脸，我也设置了左边属性的动画，然后在球中设置溢出:隐藏；来掩盖内容。这是它不带遮罩时的样子:

[![how animation looks](../Images/39a95456fcca9404a59b43432b8767ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JzTgl8Hk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1440/0%2AL9tXAE9yjPojhgkI.gif)

## 可达性

我们的组件具有复选框的功能，所以根据 [MDN webdocs](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_checkbox_role) 如果我们想让它可访问，我们需要添加几个属性。它还提到了“空格”按钮应该切换控件，我认为“回车”键也应该切换它，所以我用了这些绑定:

```
<label for="toggleControl">Click the sleepy face!</label>
<div @click="toggle"
   @keyup.space="toggle"
   @keyup.enter="toggle"
   :aria-checked="isChecked" 
   class="toggle-container" 
   role="checkbox" 
   tabindex="0" id="toggleControl">
     ... rest of html 
```

Enter fullscreen mode Exit fullscreen mode

而现在最后的结果！

[https://codepen.io/ederdiaz/embed/JLbKRa?height=600&default-tab=result&embed-version=2](https://codepen.io/ederdiaz/embed/JLbKRa?height=600&default-tab=result&embed-version=2)

这就是本周第一个**小工具**的内容，如果你想看下周的某个小工具，请在评论区发表。

下周见！

*最初发表于* [*埃德尔迪亚兹*](http://ederdiaz.com/blog/2018/04/04/smiley-toggle-wotw/) *。*