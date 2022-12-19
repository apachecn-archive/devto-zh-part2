# 如何创建纯 CSS 插图和动画-第 1 部分

> 原文：<https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-1-1j1k>

我一直对设计、插图和调色板很感兴趣，但在过去几年里，我一直专注于成为一名更好的前端开发人员，几乎没有时间练习我的创作技能。当我第一次接触 CSS 图像时，我迫不及待地想尝试一下。最后，我可以玩形状和颜色，并在编码时探索和发展我的创造力！

#### 但是首先，什么是纯 CSS 图像？

纯 CSS 图像是用 HTML 和 CSS 构建的插图。它不包括图像文件导入的使用，也不包括在插图软件中通过导出图形生成的代码。换句话说，这是一个完全用 HTML 和 CSS 在代码编辑器中手工编码的图像。

原理很简单:使用 HTML，您可以创建任意多的 div，每个 div 代表最终图像的一个组件的基本形状。使用 CSS 属性，如渐变、变换、边框半径、阴影等，您可以变换这些基本形状，并将其排列成一个漂亮的插图。

#### 为什么是 CSS 图片？

但是，你会问，CSS 图像有什么意义呢？毕竟 CSS 主要是用来设计网页样式的，而且有更适合和更有效的工具来为网页设计制作高质量和轻量级的图形(想想 SVG)。事实上，使用 CSS 进行演示不仅有一些设计限制，而且结果可能过于复杂、耗时，并面临一些主要的跨浏览器/设备问题。

所以 CSS 图片肯定是有位置和时间的。然而，创建 CSS 插图是一种很好的方式，可以更好地掌握 CSS，并了解新的工具和概念，如动画，预处理程序或更模糊的 CSS 属性。自从我开始编码 CSS 图像以来，我觉得我的 CSS 技能突飞猛进，我对以前不太了解的概念变得更加熟悉，比如各种 CSS 选择器、3D 变换和关键帧动画。

#### 是为了谁？

CSS 插图非常适合:

*   希望利用他们的设计技能来学习 html/css，或者对 html/CSS 更有信心的插画师或设计师
*   前端开发人员希望发挥他们的创造力，培养良好的设计眼光
*   任何人都想在加强 CSS 技能的同时享受一点乐趣
*   任何希望与社区联系、激励和被激励的人
*   或者任何想接受挑战的人

在这个系列中，我们将学习如何创建三个 CSS 插图，从简单到复杂。我们将学习 CSS 动画的基础，以及如何使用它们来制作我们的插图。在此过程中，我们将了解哪些概念、工具和技术可以帮助加快我们的工作流程。

**第 1 部分:使用 CSS 笑脸**
[学习基础知识和工作流程提示第 2 部分:使用 CSS 宝丽来](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-2-1ao4)
[介绍 CSS 动画第 3 部分:使用 CSS 灯塔场景的更高级技术](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-3-3e8a)

虽然本系列教程不需要任何 web 开发的高级知识，但我假设您至少熟悉 HTML 和 CSS。

在我们开始之前，有几个建议:

*   如果您还没有一个 [CodePen](https://codepen.io/) 帐户，请开立一个。它将消除必须建立一个项目的痛苦，特别是因为我们将使用 CSS 预处理程序和模板语言。
*   使用 Chrome 或 Firefox，因为其他浏览器已经被证明充满 CSS 插图。

# 基础知识

好了，说够了，让我们开始制作我们的第一张 CSS 图片吧！

这里有一个圆:

```
<div class="circle"></div> 
```

Enter fullscreen mode Exit fullscreen mode

```
html,
body {
  height: 100%;
  width: 100%;
  overflow: hidden;
  padding: 0;
  margin:0;
}
body {
  background: #FEEE9D;
}
.circle {
  position: absolute;
  width: 300px;
  height: 300px;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  border-radius: 50%;
  background-color: #FBD671;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/59a275996418a0cfd3ce838e42733e83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8hqvhaSS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vnfb7a57rwarv9ke8wp6.jpg)

目前为止没什么印象。

我们将添加一些元素，把这个圆圈变成一个笑脸。圆圈将是头部，我们可以添加眼睛和嘴:

```
<div class="head">
  <div class="face">
    <div class="mouth"></div>
    <div class="eye-group">
      <div class="eye eye-left"></div>
      <div class="eye eye-right"></div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
html,
body {
  height: 100%;
  width: 100%;
  overflow: hidden;
  padding: 0;
  margin: 0;
}
body {
  background: #FEEE9D;
}
* {
  position: absolute;
}
.head {
  width: 300px;
  height: 300px;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  border-radius: 50%;
  background-color: #FBD671;
}
.face {
  width: 150px;
  height: 170px;
  left: 75px;
  top: 75px;
}
.mouth {
  width: 100%;
  height: 75px;
  bottom: 0;
  left: 0;
  background-color: #20184E;
  border-radius: 10px 10px 150px 150px;
  border: 5px solid #20184E;
}
.eye-group {
  width: 150px;
  height: 50px;
  top: 10px;
  left: 0;
}
.eye {
  background-color: #20184E;
  width: 30px;
  height: 50px;
  border-radius: 50%;
}
.eye-left {
  left: 15px;
}
.eye-right {
  right: 15px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/d682f75a9bba63a46fc390add8c7546b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P124fz0d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f6n87mht45wxvffnffjf.jpg)

好吧，让我们看看这是怎么回事。

#### 主容器

我们有一个主容器，在这个例子中是`.head`元素，它包含所有其他的 HTML 元素。如你所料，这是我们插图的中心，也是其他所有内容定位的参考。

#### 绝对定位

所有元素都有`position: absolute`属性。这允许在顶部/右侧/底部/左侧属性的帮助下，相对于它们的父对象精确定位它们。需要注意的是，绝对定位会将元素从页面的自然流动中移除。这意味着具有该属性的元素的位置不会影响其他元素的位置，也不会受到它们的位置的影响。默认情况下，将`position: absolute`属性分配给所有元素将确保它们彼此独立。更多关于[的定位在这里](https://css-tricks.com/almanac/properties/p/position/)。

```
* {
  position:absolute;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 嵌套

按照这个逻辑，我们使用嵌套将元素组合在一起。观察`.face`元件。它不是图像的实际组成部分，只是用来将眼睛和嘴组合在一起。`.mouth`和`.eye-group`的位置(和大小，如果我们用%而不是 px)将参考`.face`。例如，如果我们决定以后要重新定位头部的面部，我们可以立即这样做，而不必调整`.mouth`和`.eyes`的属性。这是基本的 CSS 逻辑，如果你有 UI 开发经验，对你来说并不陌生。根据经验，嵌套允许更清晰的结构，因为它将属于一起的元素分组，并使插图更容易操作。

#### 堆叠

使用绝对定位允许我们对元素的堆叠顺序有更多的控制。自然，堆叠顺序遵循 HTML 元素的流程。第一个物体将在堆的底部(在最后面)，而最后一个将在顶部(离你最近)。通过 z-index 属性，我们可以改变堆中每个元素的位置，并使它们按照我们想要的方式重叠，前提是这些元素属于同一个[堆栈上下文](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)。z-index 的值越高，它在堆栈中的位置就越高。

> 堆叠上下文有点棘手，并不总是像你期望的那样运行。你可以在这里阅读更多关于是什么触发了新堆栈上下文[的创建。](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

#### 仅类

我们只使用 CSS 类来定位和样式化我们的 HTML 元素，因为我们不想被[特殊性](https://css-tricks.com/specifics-on-css-specificity/)问题所困扰。

* * *

唷，已经很多了。但是这些是 CSS 插图的非常重要的概念，一般来说。一旦你熟悉了定位和堆叠，你的 CSS 技能将会大大提高。

现在我们已经对如何定位元素有了基本的了解，让我们稍微活跃一下我们的笑脸。我想添加一些细节，如舌头，瞳孔和阴影。

阴影是直截了当的，但增加了一点美感:

```
.head {
  width: 100%;
  height: 100%;
  background-color: #FBD671;
  border-radius: 50%;
  box-shadow:inset -10px -10px 0px #EFBB42;
} 
```

Enter fullscreen mode Exit fullscreen mode

舌头是一个简单的粉红色圆圈，但我们只想展示它的一部分，以创造它在口腔内的假象。为此，我们将`.tongue`嵌套在`.mouth`中，并将`overflow: hidden`属性应用于`.mouth.`

```
<div class="head">
  <div class="face">
    <div class="mouth">
      <div class="tongue"></div>
    </div>
    <div class="eye-group">
      <div class="eye eye-left"></div>
      <div class="eye eye-right"></div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.mouth {
  width: 100%;
  height: 75px;
  background-color: #20184E;
  left: 0;
  bottom: 0;
  border-radius: 10px 10px 150px 150px;
  border: 5px solid #20184E;
  overflow:hidden;
}
.tongue {
  width: 100px;
  height: 80px;
  left: 25px;
  top: 30px;
  background-color: #F15962;
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在我们的`.eye`元素中添加一个简单的椭圆形瞳孔。

```
<div class="container">
  <div class="head">
    <div class="face">
      <div class="mouth">
        <div class="tongue"></div>
      </div>
      <div class="eye-group">
        <div class="eye eye-left">
          <div class="pupil"></div>
        </div>
        <div class="eye eye-right">
          <div class="pupil"></div>
        </div>
      </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.pupil {
  width: 10px;
  height: 15px;
  top: 5px;
  background-color: #FBD671;
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

它看起来已经好多了。

[![](../Images/71d69915a30946d15fe66c80edef84ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--175hOzPL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jg90tzx6lan2vx0wrudf.jpg)

# 更高级的概念

现在我们已经构建了我们的第一个 CSS 图像，让我们后退一步，看看如何改进我们的代码。虽然这些步骤对于构建 CSS 图像来说根本不是必需的，但我发现它们极大地改进了我的工作流程，并帮助我在总体上更好地掌握 CSS。

#### CSS 预处理器

如果你熟悉 [CSS 预处理程序](https://developer.mozilla.org/en-US/docs/Glossary/CSS_preprocessor)，你会知道有一个更好的方法来做这件事。我不会深入研究如何设置预处理器，因为这超出了本文的范围。相反，我强烈推荐你使用 [Codepen](https://codepen.io/) ，它内置了所有的预处理程序。你只需要在 CSS 设置中选择一个你想要的就可以了。

我个人喜欢使用[萨斯/SCSS](https://sass-lang.com/) ，但也可以随意使用任何可用的选项。

预处理器最著名的特性是使用变量的能力。预处理变量就像 javascript 变量一样工作。您只需声明它们并给它们赋值一次，就可以在整个代码中使用和重用它们。这是一个伟大的工具，因为它避免了重复，给你更多的灵活性。让我们在 CSS 中实现一些变量。变量的一个好用途是定义颜色。

SCSS 变量总是以$符号开始:

```
$black: #20184E; 
```

Enter fullscreen mode Exit fullscreen mode

您可以随意命名它，但它必须以$开头，并被赋予一个有效的 CSS 属性。

那你可以这样用:

```
background-color: $black; 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到这将派上用场。如果我们后来决定要改变这种特殊的颜色，我们只需要在一个地方这样做，它就会反映在代码中使用该变量的所有地方。此外，当谈到颜色时，记住您创建的变量的名称要比记住 RGB 或十六进制值容易得多。

我已经将新的颜色变量添加到代码的顶部，这样我们就可以很容易地找到它们。在全局范围内声明变量(与在选择器中声明相反)将确保我们可以在任何地方使用它们。

```
$black: #20184E;
$head-color: #FBD671;
$background: #FEEE9D;
$tongue-color: #F15962; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以改变我们的 css 属性，使它们指向变量而不是硬编码的十六进制值。

例:

```
.mouth {
  width: 100%;
  height: 75px;
  bottom: 0;
  background-color: $black;
  border: 5px solid $black;
  border-radius: 10px 10px 150px 150px;
  overflow: hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

> CSS 现在有了原生变量，使得预处理变量变得没有必要。然而，预处理器提供的不仅仅是变量，它在许多其他方面仍然非常有用。

SCSS 还附带了许多辅助功能，包括[颜色功能](http://sass-lang.com/documentation/Sass/Script/Functions.html)。这些功能非常强大，因为它们允许您非常容易地操纵颜色。

让我们来看看应用于头部的插图阴影。现在我们使用的是硬编码的十六进制值，我不得不手动查找。我们将使用变暗函数来生成这个值:

```
.head {
  border-radius: 50%;
  width: 100%;
  height: 100%;
  background-color: $head-color;
  box-shadow: inset -10px -10px 0px darken($head-color, 20%);
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，该函数将`$head-color`作为参数，然后增加 20%的暗度。

我喜欢使用颜色函数，因为它们不仅消除了借助一些设计软件来生成颜色的需要，而且有助于为您的插图创建更和谐的调色板。

SASS 的另一大特点是嵌套。嵌套允许您将选择器嵌套在父选择器中，以便创建快捷方式。它为你的 CSS 创建了一个更好的层次结构和可读性，并且避免了一遍又一遍的重复选择器。

例如

```
.pink {
  background-color: pink;
  .purple {
    background-color: purple;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

将被编译为:

```
.pink { 
  background-color: pink;
}
.pink .purple { 
  background-color: purple;
} 
```

Enter fullscreen mode Exit fullscreen mode

SASS 还提供& selector，它基本上是指父选择器。所以

```
.pink {
  background-color: pink;
  &.purple{
    background-color: purple;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将被编译为:

```
.pink { 
  background-color: pink;
}
.pink.purple { 
  background-color: purple;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用& selector 不会像上面的例子那样将具有类`.purple`的子元素作为目标。它的目标是拥有类`.pink`和`.purple`的父元素。

> 当心过多的嵌套，因为这会使你的代码过于复杂，可读性差，这与我们的目标背道而驰。一般来说，我倾向于最多 3 层嵌套。

记住这一点，让我们在代码中实现嵌套。例如:

```
.eye-group {
  top: 10px;
  left: 0;
  width: 150px;
  height: 50px;
}
.eye {
  background-color: $black;
  width: 30px;
  height: 50px;
  border-radius: 100%;
}
.eye-left {
  left: 15px;
}
.eye-right {
  right: 15px;
} 
```

Enter fullscreen mode Exit fullscreen mode

变成:

```
.eye-group {
  top:10px;
  left:0;
  width: 150px;
  height: 50px;
  .eye {
    background-color: $black;
    width: 30px;
    height: 50px;
    border-radius: 100%;
    &.eye-left {
      left:15px;
    }
    &.eye-right {
      right:15px;
    }
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

预处理程序是构建 CSS 图像的重要资产，将大大加快您的工作流程。它们还提供了更高级的选项，如函数、循环、混合等。这些将在更复杂的插图中派上用场，我们将在本系列的最后一部分看到。

#### :前:后

到目前为止，我们已经为笑脸中的每个形状创建了一个 div。虽然这完全没问题，但是有一种方法可以减少 HTML 元素的数量。这就是伪选择器`:before`和`:after`的用处。

这些[伪选择器](https://css-tricks.com/almanac/selectors/a/after-and-before/)会自动出现在您创建的任何 HTML 元素中。因此，对于 HTML 中的任何元素，都会给你两个额外的容器来添加内容。尽管它们最初并不存在于 DOM 中，但是您可以使用内容属性来定位它们，比如:

```
.some-class:before, .some-class:after { 
  content: "";
} 
```

Enter fullscreen mode Exit fullscreen mode

该属性可用于插入文本或图像等内容。在 CSS 插图中，我们不需要这些，但是我们仍然希望使用选择器，我们可以通过使用空引号来实现。虽然这看起来像是一个不必要的步骤，但这是确保这些选择器可用的属性，所以要确保它在那里。一个不忘记它并且避免重复的好方法是，默认情况下将它分配给所有的`:after`和`:before`元素:

```
*:before, *:after {
  position: absolute;
  content: '';
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所料，`:before`和`:after`依赖于它们的父元素来决定大小和位置。

让我们看看如何在我们的代码中使用这些。考虑一下`.mouth`元素。它有一个子元素:`.tongue`元素。我们将把`.tongue`选择器替换为`:after`伪选择器:

```
.mouth {
  width: 100%;
  height: 75px;
  bottom: 0;
  background-color: $black;
  border: 5px solid $black;
  border-radius: 10px 10px 150px 150px;
  overflow: hidden;
  &:after {
    background-color: $tongue-color;
    width: 100px;
    height: 80px;
    border-radius: 50%;
    left:25px;
    top:30px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样的逻辑可以应用于替换`.pupil`元素:

```
.eye-group {
  top: 10px;
  width: 150px;
  height: 50px;
  .eye {
    background-color: $black;
    width: 30px;
    height: 50px;
    border-radius: 100%;
    border: 5px solid $black;
    &:after {
      width: 10px;
      height: 15px;
      top: 5px;
      background-color: #FBD671;
      border-radius: 50%;
    }
    &.eye-left {
      left: 15px;
    }
    &.eye-right {
      right: 15px;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以去掉 html 中的`.tongue`和`.pupil`div:

```
<div class="head">
  <div class="face">
    <div class="mouth"></div>
    <div class="eye-group">
      <div class="eye eye-left"></div>
      <div class="eye eye-right"></div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

虽然这不是绝对必要的，但我发现这种做法有助于保持我的代码更整洁，并避免我的 HTML 过度拥挤。同样，只有在有意义的时候才使用伪选择器。(例如:创建一个与其父容器在视觉上相关的元素)。

#### HTML 模板语言

我们可以改进代码的另一个步骤是使用 HTML 模板语言。我喜欢用 [Pug](https://pugjs.org/api/getting-started.html) ，这也是 Codepen 编辑器内置的。

它是这样工作的:Pug 使用标记名来表示一个完整的 HTML 元素。比如`<div></div>`在 Pug 中会简单的翻译成`div`。它使用缩进来表示嵌套，重新创建 HTML 的树结构。

在 CSS 图像中，建议只使用 div 元素，并为它们分配一个类，以便能够轻松地定位它们。在 Pug 中用类表示 div 的方法如下:

普通 html:

```
<div class="container"></div> 
```

Enter fullscreen mode Exit fullscreen mode

帕格:

```
div.container 
```

Enter fullscreen mode Exit fullscreen mode

或者，因为`div`是默认的标签名，所以可以省略:

```
.container 
```

Enter fullscreen mode Exit fullscreen mode

我发现这种语法更简洁，它让我可以专注于类。因为我已经知道我所有的元素都是 div，所以我不需要任何无用的语法来污染我的代码。

让我们把 HTML 转换成 Pug:

```
.head
  .face
    .mouth
    .eye-group
      .eye.eye-left
      .eye.eye-right 
```

Enter fullscreen mode Exit fullscreen mode

干净多了。

类似于 CSS 预处理程序，你可以用 Pug 做很多事情，因为它是由 JavaScript 驱动的:mixins，includes 等等。谈到 CSS 图像，最强大的特性之一就是循环。我们稍后会看到如何使用它们。

这是 CodePen 的最后一个项目。您可以看到，我们最近的更改丝毫没有影响插图。

[https://codepen.io/agathaco/embed/PdWYgp?height=600&default-tab=result&embed-version=2](https://codepen.io/agathaco/embed/PdWYgp?height=600&default-tab=result&embed-version=2)

对，我们已经学到了很多！如何创建各种形状并将它们组装成一个有凝聚力的插图，如何使用 hacks 来模拟更复杂的形状，如何使用预处理器、模板语言和伪选择器来改进我们的工作流并清理我们的代码。在本系列的第二部分，我们将实践这些新概念并构建一个 CSS 宝丽来。然后，我们将学习如何制作动画。

[第二部分:用 CSS 宝丽来介绍 CSS 动画](https://dev.to/agathacco/how-to-create-pure-css-illustrations-and-animate-them---part-2-1ao4)