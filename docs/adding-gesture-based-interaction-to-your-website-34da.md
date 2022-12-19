# 在你的网站上添加基于手势的交互

> 原文：<https://dev.to/genejams/adding-gesture-based-interaction-to-your-website-34da>

<small>约翰·施诺布里奇在 Unsplash 上的封面照片</small>

 <small>虽然这篇文章中的主题可能仍然是真实的，但是这些内容引用了一个旧版本的 *zuix.js* 库。最新文档见 [zuix.js](https://zuixjs.org) 网站。</small> 

* * *

桌面和移动开发的一个常见问题是，除了“click”事件，鼠标事件在移动设备上不起作用，因为它们是基于触摸的设备，所以必须使用不同的事件集。

**常见鼠标事件**

*   老鼠洞
*   鼠标移动
*   老鼠！老鼠

**常见触摸事件**

*   touchstart
*   触摸移动
*   触摸端

因此，要在一个既能在桌面上运行又能在移动设备上运行的网站中实现自定义交互，就需要同时处理鼠标和触摸事件。

如果我们考虑到鼠标和触摸屏不是数字世界中唯一可用的交互方式，情况会变得更糟。

鼠标、触摸屏、你的手、你的眼睛或者你的整个身体都可以被认为是可能的交互媒介。

这就是对基于手势的模型的抽象可以派上用场的地方，它创建了一种与用户界面交互的通用方式，而不管使用的是什么设备。

## 手势助手

为了给网页添加手势交互，我们将使用 [**手势助手**](https://zuixjs.github.io/zkit/content/controllers/gesture-helper/) 组件。这可以用在任何 HTML 元素上来检测它上面的手势。

**识别手势**

*   触控
*   轻敲，水龙头
*   平底锅
*   偷窃
*   释放；排放；发布

由于这个组件是使用 [**zuix.js**](https://zuixjs.org) 实现的，首先我们需要包含库:

```
<script src="https://cdn.jsdelivr.net/npm/zuix-dist@1.0.0/js/zuix.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以通过只使用 HTML ( `data-ui-load`属性)或 JavaScript 在元素上加载*手势助手*组件。因为无论如何都需要 JavaScript 来处理事件，所以让我们使用`zuix.load(..)`方法来加载组件:

**HTML 元素**

```
<div data-ui-field="surface"></div> 
```

Enter fullscreen mode Exit fullscreen mode

**JavaScript**

```
let surface = zuix.field('surface');
zuix.load('@lib/controllers/gesture_helper', {
  view: surface, // <- detect gestures over the given element
  on: {
    'gesture:touch': function(e, tp) {
      // handle gesture ...
    },
    'gesture:tap': function(e, tp) {
      // handle gesture ...
    },
    'gesture:pan': function(e, tp) {
      // handle gesture ...
    },
    'gesture:swipe': function(e, tp) {
      // handle gesture ...
    },
    'gesture:release': function(e, tp) {
      // handle gesture ...
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

有了这段代码，我们就可以处理元素上的手势事件了。
每个事件将传递给处理函数(回调函数)一个`tp`(触摸指针)对象，该对象包含一些可用于动画显示目标元素的数据:

```
tp = {
  // time frame
  startTime,
  endTime,
  // initial touch position
  startX,
  startY,
  // relative movement
  shiftX,
  shiftY,
  // actual direction, speed, position
  direction, // 'left' | 'right' | 'up' | 'down'
  velocity, // pixels per milliseconds
  x: x,
  y: y,
  // guessed scrolling direction
  scrollIntent(),  // false | 'horizontal' | 'vertical'
  // original event + cancel method
  event,
  cancel()
}; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在下面的代码栏中看到完整的示例:

[https://codepen.io/genielabs/embed/GXrRWv?height=600&default-tab=result&embed-version=2](https://codepen.io/genielabs/embed/GXrRWv?height=600&default-tab=result&embed-version=2)

在本例中，有两个正在加载的`gesture_helper`实例。

第一个加载在主界面上，用于检测*平移*手势。平移手势是指在表面上触摸并拖动指针。

第二个加载在球对象上，用于检测*触摸*和*轻击*手势。
当球对象被触摸时，一个`.touched`类被添加到球对象中。因此，如果`.touched`类出现在表面上，处理*平移*手势的函数将开始移动球。*轻敲*球会让它弹起。轻击手势是指触摸后立即释放。

### 结论

是的，我称这个`gesture_helper`为“组件”，但准确地说，它是一个**控制器**。

组件和控制器之间的区别在于，第一个组件还附带了用户界面( *HTML 视图*),而后者是一个只包含 JavaScript 代码的组件，可以加载到任何 HTML 元素上。

由于这个原因，控制器可以被认为是*框架不可知的*(尽管有人可能不同意这个定义)。
例如，你可以在一个 *React* 或 *Vue* 组件或一个基于引导的网站上使用一个 *zuix.js* 控制器。

zuix.js 控制器的另一个特性是你可以在同一个元素上装载任意多的控制器。因此，你不仅可以添加`gesture_helper`，还可以添加一个控制器来实现一些基于物理的反应或任何其他交互逻辑(例如，甚至像输入字段验证这样简单的事情)。
你可以将元素融入生活，而不会干扰用户界面。

转到第一个帖子:

[![genejams](../Images/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 基于组件的 web 与 zuix.js

### { Gene } Jul 18 ' 183min read

#showdev #webdev #javascript #zuixjs](/genejams/component-based-web-with-zuixjs-3eha)