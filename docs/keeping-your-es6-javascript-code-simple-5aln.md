# 保持您的 ES6 JavaScript 代码简单

> 原文：<https://dev.to/leosl/keeping-your-es6-javascript-code-simple-5aln>

经过几年几乎只使用 Ruby on Rails 和一些 jQuery 的工作，我将注意力转移到了前端开发，发现了 JavaScript ES6 语法的美妙之处以及 React 和 Vue 等令人兴奋的现代库。我开始只用 ES6 Vanilla JS 实现新特性，并立刻爱上了`class`抽象和那些 arrow sweeties 函数。

如今，我正在生成大量的 JS 代码，但是，因为我是学徒，所以还有很大的改进空间。通过我的研究和观察，我了解到即使使用 ES6 中的语法糖，如果你不遵循 [SOLID](https://hackernoon.com/solid-principles-made-easy-67b1246bcdf) 的主要原则，你的代码很有可能变得难以阅读和维护。

为了演示我正在谈论的内容，我将带您经历上周的一次精彩的代码审查会议。我们将从一个 35 行的 JS 类开始，并以一个漂亮的 11 行代码段结束，这段代码只使用了巧妙的函数！

[![refactoring](../Images/c821c05dffb0da1ea2fda155a31bc2c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cQlQchxT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzbr5jol478yyloqfzv1.gif)

有了耐心和韧性，你将能够观察并应用这个模式到你自己的代码库中。

## 特性

我需要完成的事情非常简单:从页面中获取一些信息，并向第三方跟踪服务发送一个请求。我们正在构建一个事件跟踪器，并跟踪一些页面。

下面的代码示例使用不同的代码设计策略实现了相同的任务。

### 第 1 天——使用 ES6 类语法(又名对象原型模式包装器)

文件名:`empty-index-tracking.js`

```
import SuccessPlanTracker from './success-plan-tracker';
import TrackNewPlanAdd from './track-new-plan-add';

class EmptyIndexTracking {
  constructor(dataset) {
    this.trackingProperties = dataset;
    this.emptyIndexButtons = [];
  }

  track(element) {
    const successPlanTracker = new SuccessPlanTracker(this.trackingProperties);
    const emptyIndexProperty = {
      emptyIndexAction: element.dataset.trackingIdentifier,
    };

    successPlanTracker.track('SuccessPlans: EmptyIndex Interact', emptyIndexProperty);
  }

  bindEvents() {
    this.emptyIndexButtons = Array.from(document.getElementsByClassName('js-empty-index-tracking'));

    this.emptyIndexButtons.forEach((indexButton) => {
      indexButton.addEventListener('click', () => { this.track(indexButton); });
    });
  }
}

document.addEventListener('DOMContentLoaded', () => {
  const trackProperties = document.getElementById('success-plan-tracking-data-empty-index').dataset;

  new EmptyIndexTracking(trackProperties).bindEvents();
  new TrackNewPlanAdd(trackProperties).bindEvents();
});

export default EmptyIndexTracking; 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到，我开始智能隔离通用跟踪器`SuccessPlanTracker`，以便在空索引之外的另一个页面中重用。但是，等一下。如果这是空的索引跟踪器，那么这个外国人`TrackNewPlanAdd`到底在那里做什么？

### 第二天-(代码评审开始)-清除类样板代码

文件名:`bind-empty-index-tracker.js`

```
import SuccessPlanTracker from './success-plan-tracker';

let emptyIndexButtons = [];
let emptyIndexTrackingData = {};
let emptyIndexActionProperty = {};
let emptyIndexTrackingProperties = {};

const trackEmptyIndex = (properties) => {
  const successPlanTracker = new SuccessPlanTracker(properties);
  successPlanTracker.track('SuccessPlans: EmptyIndex Interact', properties);
};

const populateEmptyIndexData = () => {
  emptyIndexButtons = document.querySelectorAll('.js-empty-index-tracking');
  emptyIndexTrackingData = document.getElementById('success-plan-tracking-data-empty-index').dataset;
};

const bindEmptyIndexTracker = () => {
  populateEmptyIndexData();
  emptyIndexButtons.forEach((indexButton) => {
    indexButton.addEventListener('click', () => {
      emptyIndexActionProperty = { emptyIndexAction: indexButton.dataset.trackingIdentifier };
      emptyIndexTrackingProperties = { ...emptyIndexTrackingData, ...emptyIndexActionProperty };
      trackEmptyIndex(emptyIndexTrackingProperties);
    });
  });
};

export default bindEmptyIndexTracker; 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在文件名清楚地反映了特性责任，看那里，没有更多的 EmptyIndexTracker 类(更少的样板代码-了解更多[这里](https://gist.github.com/indiesquidge/f8c486795d7dd455c0327ce7e0aa8c16)和[这里](https://www.accelebrate.com/blog/javascript-es6-classes-and-prototype-inheritance-part-1-of-2/))，我们使用简单的函数变量，伙计，你甚至使用那些闪亮的 [ES6 对象传播](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)点！

[querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Element/querySelectorAll) 方法已经返回了一个数组，所以我们能够从`Array.from(document.getElementsByClassName('js-empty-index-tracking'))`中移除 **Array.from()** 函数——记住 [getElementsByClassName](https://developer.mozilla.org/en-US/docs/Web/API/Element/getElementsByClassName) 返回一个对象！

此外，由于核心职责是绑定 HTML 元素，`document.addEventListener('DOMContentLoaded')`不再属于文件。

干得好！

### 第三天——去除旧的做法，进一步分离责任

文件名:`bind-empty-index.js`

```
import successPlanTrack from './success-plan-tracker';

export default () => {
  const buttons = document.querySelectorAll('.js-empty-index-tracking');
  const properties = document.getElementById('success-plan-tracking-data-empty-index').dataset;

  buttons.forEach((button) => {
    properties.emptyIndexAction = button.dataset.trackingIdentifier;
    button.addEventListener('click', () => {
      successPlanTrack('SuccessPlans: EmptyIndex Interact', properties);
    });
  });

  return buttons;
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你密切注意，上面的代码中没有 SuccessPlanTracker 类，与旧的 EmptyIndexTracker 的命运相同。阶级杀戮的心态一旦形成，就会蔓延并自我繁殖。但是不要害怕，我的好孩子！记住，总是尽量保持你的 JS 文件简单:因为不需要知道类实例的状态，而且类实际上只公开一个方法，你不认为使用 ES6 类抽象有点过分了吗？

你注意到我从文件的顶部移除了变量实例吗？这个练习重新安装到 ES5，我们不需要太担心它，因为我们有 ES6+语法！

最后，第三个版本的最后一个主要变化:我们的空索引跟踪器绑定器现在只做一件事:元素绑定！遵循这些步骤使代码非常接近单一责任原则——最重要的坚实原则之一。

### 第 4 天-(代码评审结束)-避免 DOM 草率操作

```
import successPlanTrack from './tracker';

const trackAction = (properties, button) => {
  const trackProperties = { ...properties, emptyIndexAction: button.dataset.trackingIdentifier };
  successPlanTrack('SuccessPlans: EmptyIndex Interact', trackProperties);
};

export default () => {
  const buttons = document.querySelectorAll('.js-empty-index-tracking');
  const dataset = document.getElementById('success-plan-tracking-data-empty-index').dataset;
  const properties = { ...dataset, emptyIndexAction: '' };

  buttons.forEach(button => (
    button.addEventListener('click', () => trackAction(properties, button))
  ));

  return buttons;
}; 
```

Enter fullscreen mode Exit fullscreen mode

嘿，现在有更多的线了，你这个骗子！

问题是我们的第三个版本有点破损。我们在第`properties.emptyIndexAction = button.dataset.trackingIdentifier;`行不恰当地改变了 DOM 元素数据集。一个按钮的属性被传递给另一个按钮，生成混乱的跟踪事件。为了解决这种情况，我们通过创建自己的作用域方法 **trackAction()** ，从绑定循环中将`emptyIndexAction`属性分配给适当的函数。

通过添加这些额外的行，我们按照单一责任和封装的良好原则改进了我们的代码。

* * *

#### 最后，总结一下，写下:

*   如果你想设计和编写精彩的代码，你需要愿意进一步探索，超越适当的现代语法的限制。
*   即使你的代码的第一个版本非常简单易读，也不一定意味着这个系统有一个好的设计，或者它遵循了至少一个[坚实的](https://hackernoon.com/solid-principles-made-easy-67b1246bcdf)原则。
*   接受建设性的代码评审并让其他开发人员指出你可以做得更好也是很重要的。记住:为了让你的代码简单，你需要想得更大。

**ProTip to-go:** 这里有一张[非常有用的 ES6 备忘单](https://devhints.io/es6)

* * *

非常感谢您阅读这篇文章。还有重构的例子或者代码评审的经验可以分享吗？请在下面留言评论！还有，你可以通过点赞和分享来帮我把这个消息分享给别人。

PS:非常感谢 [@anderson06](https://github.com/anderson06) 是一个如此好的代码伙伴，在代码审查会议上给了我非常棒的反馈。