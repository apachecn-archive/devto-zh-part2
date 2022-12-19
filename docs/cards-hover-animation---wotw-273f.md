# 卡悬停动画- WotW

> 原文：<https://dev.to/ederchrono/cards-hover-animation---wotw-273f>

欢迎来到“每周小部件”系列，在这里我拍摄了令人敬畏的 UI/UX 组件的 gif 或视频，并用代码将它们赋予生命。

几周以来，我一直很忙，在 [Wizeline Academy](https://academy.wizeline.com/) 准备和讲授 Vue.js 课程，但我带着另一个互动组件回来了。

今天我们将制作一个卡片列表，当悬停(或在移动设备中点击)时会有动画效果，灵感来自于 [Kreativa 工作室](https://dribbble.com/KreativaStudio)制作的[运球](https://dribbble.com/shots/4579038-Foodiefit-Interaction-studio-included)，看起来像这样:

[![wotw-7](../Images/ac324534f8fdda975ff3437bfbb19c1d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OSzUDnw2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2s3z34vqtb4er8d31cw4.gif)

### 制剂

对于今天的小部件，我们将使用 [Vue.js](https://vuejs.org/) ，对于一些特定的动画，我们将使用 [TweenMax](https://greensock.com/tweenmax) 。

如果你想继续，你可以派生这个已经有依赖关系的 [codepen 模板](https://codepen.io/ederdiaz/pen/bMLbNp)。

### 初始标记

对于这个组件，我将从制作一张卡开始。它将被分为两部分:图像和带有文本元素的页脚。另外，我将为每个元素设置一个特定的类，这样我们可以稍后对其进行样式化。

```
<div id="app">
  <div class="card-row">
    <div class="card">

      <img class="card-image" src="https://placeimg.com/640/480/nature">

      <div class="card-footer">
        <p class="card-text">RECIPE</p>
        <h3 class="card-title">Title</h3>
        <p class="card-text">by 
          <span class="card-author">Author</span>
        </p>
      </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们应该只有一个大的图像和一些文字如下。

### 造型

在做任何事情之前，我们需要让卡片看起来像参考，以便处理任何需要的动画。
首先，我将匹配主体背景颜色，然后在`card-row`中，我将利用`flex`、`justify-content`属性和`align-items`使内容居中，在本例中是卡片。

```
body {
  background-color: #E1E7E7;
}

.card-row {
  display: flex;
  justify-content: center;
  align-items: center;  
  min-width: 780px;
  width: 100%;
  height: 500px;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们设计卡片。`position`将被设置为`relative`,以便能够定位页脚元素并将其粘贴到底部。此外，我们将`overflow`属性设置为`hidden`，以便能够调整背景图像的大小，而不会超出卡片的范围。

```
.card {
  position: relative;
  background-color: #FFFFFF;
  height: 370px;
  width: 240px;
  margin: 10px;
  overflow: hidden;
  box-shadow: 0px 2px 4px 0px rgba(0,0,0,0.5);
} 
```

Enter fullscreen mode Exit fullscreen mode

对于图像，我将`position`设置为`absoulte`，将`left`和`right`属性设置为`-9999px`，即使我们对图像的宽度进行动画处理，这个技巧也应该使图像水平居中。

```
.card-image {
  /* center horizontally overflown image */
  position: absolute;
  left: -9999px;
  right: -9999px;
  margin: auto;

  height: 220px;
  min-width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，我们需要样式页脚和它的内容。为此，`position: absolute`和`bottom:0`应该将容器保持在底部。剩下的只是让元素看起来有点像引用的规则。

```
.card-footer {
  position: absolute;
  bottom: 0;
  height: 130px;
  padding: 10px 15px;
  font-family: Helvetica;
}
.card-text {
  font-size: 14px;
  color: rgba(0, 0, 0, 0.7);
}
.card-title {
  font-family: Serif;
}
.card-author {
  font-size: 14px;
  color: #BAB096;
} 
```

Enter fullscreen mode Exit fullscreen mode

在那之后我们应该有这样的东西:
[![card-styles](../Images/a03b87f5f81df87c9430a9fac0cd50c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RTJ1hG3w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8rqvl6q0o4fcjjc2nbf.png)

### 行为举止

在接下来的步骤中，我们将需要不止一张卡，所以让我们声明至少三张卡的数据。

```
const cards = [
  {title: 'Gooey PBJ Brownies', author: 'John Walibur', image: 'https://placeimg.com/640/480/nature'},
  {title: 'Crisp Spanish Tortilla Matzo Brei', author: 'Colman Andrews', image: 'https://placeimg.com/640/480/animals'},
  {title: 'Grilled Shrimp with Lemon and Garlic', author: 'Celeste Mills', image: 'https://placeimg.com/640/480/arch'}
] 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们创建 Vue 实例，并将其绑定到 cards 数据，并声明一个变量来跟踪当前的 cards:

```
new Vue({
  el: '#app',
  data: {
    cards: cards,
    selectedCard: -1
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在在我们的模板中，我们可以用这样的`v-for`指令来呈现这些卡片:

```
<div id="app">
  <div class="card-row">
    <div v-for="(card, index) in cards" 
      :key="index"
      :ref="`card_${index}`"
      @mouseover="hoverCard(index)"
      @mouseout="hoverCard(-1)"
         class="card">

      <img class="card-image" :src="card.image">

      <div class="card-footer">
        <p class="card-text">RECIPE</p>
        <h3 class="card-title">{{card.title}}</h3>
        <p class="card-text">by 
          <span class="card-author">{{card.author}}</span>
        </p>
      </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![three cards](../Images/4d36ae1f63f9bdd224418d1fb9404691.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RC6AzsIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qbcxtn8skt9jdp6c6s54.png)

你可能已经注意到我绑定了几个事件`@mouseover`和`mouseout`。两者都触发了一个叫做`hoverCard`的方法，我们还没有声明它，所以让我们开始吧。

```
// ... vue instance
  methods: {
    hoverCard(selectedIndex) {
      this.selectedCard = selectedIndex
    },
    isSelected (cardIndex) {
      return this.selectedCard === cardIndex
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 制作悬停效果

我们将利用`selectedCard`属性和`isSelected`方法为我们要制作动画的元素设置一个类，但是首先让我们为整张卡片添加一些简单的 CSS 过渡。

```
.card {
  /* the other rules */
  transition: height 0.3s, box-shadow 0.3s;
}
.card:hover {
  height: 410px;
  box-shadow: 20px 20px 40px 0px rgba(0,0,0,0.5);
} 
```

Enter fullscreen mode Exit fullscreen mode

通过在`.card`中添加过渡线，我们表明`height`和`box-shadow`应该被动画化，然后当卡片被悬停时，它的高度将随着阴影增加，使它看起来越来越接近我们。

现在我们将使用`isSelected`方法将`.selected`卡片设置为图像。在这种情况下，我没有使用`:hover`,因为用户可以将鼠标悬停在文本上，我们仍然希望图像动画能够运行，而不仅仅是在图像悬停时。

```
<!-- card markup -->
    <img class="card-image" 
        :class="{'selected': isSelected(index)}"
        :src="card.image"> 
```

Enter fullscreen mode Exit fullscreen mode

就像我们对卡片所做的那样，让我们添加一个过渡和一个`.selected`状态:

```
 .card-image {
  /* the other rules */
  transition: height 0.3s, opacity 0.3s;
}
.card-image.selected {
  height: 410px;
  opacity: 0.3;
} 
```

Enter fullscreen mode Exit fullscreen mode

当悬停时，图像会改变其大小，我们只需要设置高度，因为宽度会自动调整以保持纵横比。

[![text not readable](../Images/849a6e29385de4175807511117f335a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dy-K8hkL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/np70esehlbdn0onix00v.png)

现在图像扩大了，感觉作者文字太亮，看不清楚。我将重复最后的步骤，使卡片悬停时看起来更暗:

```
<!-- card -->
  <!-- card-footer -->
    <span 
        class="card-author" 
        :class="{'selected': isSelected(index)}">
            {{card.author}}
    </span> 
```

Enter fullscreen mode Exit fullscreen mode

```
.card-author {
  /* the other rules */
  transition: color 0.3s;
}
.card-author.selected {
  color: #6a6456;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将其他卡片分开

我们就快到了，但是还缺少一些东西。在参考文献中，当卡片被悬停时，其他卡片会远离它，就像排斥效应一样。仅仅使用 CSS 无法轻松实现这种效果，我们需要通过计算每张卡片应该移动的位置来制作动画，然后使用`TweenMax`制作动画。

我将创建一个名为`animateCards`的新方法，它将在`selectedCard`改变时被触发。
然后对于每张卡片，我们需要知道它们应该朝哪个方向移动。

*   首先，当没有选择卡片时，一切都应该保持不变`direction = 0`。
*   当一张牌在所选牌之前时，它应该向左移动`direction = -1`。
*   如果卡片在所选卡片之后，它必须向右移动`direction = 1`。
*   最后，如果卡片是选中的那张，它不应该移动`direction = 0`。

```
// methods
    hoverCard(selectedIndex) {
      this.selectedCard = selectedIndex
      this.animateCards()
    },
    animateCards () {
      this.cards.forEach((card, index) => {
        const direction = this.calculateCardDirection(index, this.selectedCard)
        TweenMax.to(
          this.$refs[`card_${index}`], 
          0.3, 
          {x: direction * 50}
        )
      })
    },
    calculateCardDirection (cardIndex, selectedIndex) {
      if(selectedIndex === -1) {
        return 0
      }

      const diff = cardIndex - selectedIndex
      return diff === 0 ? 0 : diff/Math.abs(diff)
    }, 
```

Enter fullscreen mode Exit fullscreen mode

计算方向后，在`this.$refs`的帮助下，我们可以选择每张牌，并使 TweenMax 改变它的位置。

而现在最后的结果！

[https://codepen.io/ederdiaz/embed/NzbKeo?height=600&default-tab=result&embed-version=2](https://codepen.io/ederdiaz/embed/NzbKeo?height=600&default-tab=result&embed-version=2)

这就是本周的**小部件。**

如果你想知道更多，你可以去看看其他的 WotW:

*   [滚动卡片列表](https://dev.to/ederchrono/making-a-scrolling-card-list---wotw-57ml)
*   [动画导航](https://dev.to/ederchrono/making-an-animated-nav-component---wotw-46ho)
*   [流体布局](https://dev.to/ederchrono/how-i-made-a-fluid-layout-component---wotw-2ko0)

另外，如果你想看下周的某个小部件，可以在评论区发表。