# 反应解释，解释

> 原文：<https://dev.to/joshua/react-explaining-explained-1lb8>

[![Feynman with a blackboard](../Images/e13484028072d55fe400d012f505a4ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5ahcEIHu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r7ilhvlik6oggrmilwuv.jpg)

一次，加州理工学院的一名教员问理查德·费曼，为什么自旋 1/2 的粒子服从费米-狄拉克统计。他完美地衡量了他的听众，然后说:“我将为此准备一次新生演讲。”但是几天后他回来说，“你知道，我做不到。我不能把它降低到大一的水平。那说明我们真的不懂。[1]

解释事情很难。这需要对那件事了如指掌。把自己放在一个不懂的人的脑子里，就揭示了你不懂的东西。这是一种移情的[练习，也是一种可以磨练的技能。](https://medium.com/@iantracey/engineering-empathy-158b60dc661e)

有效地解释是非常令人满意的，也是一项需要培养的有用技能。作为软件工程师，我们很大一部分责任不仅仅是理解事物，还要解释它们。每天很多次，我们需要向新的团队成员、产品经理、设计师，甚至来自完全不同学科的人解释事情。

# React 是容易理解还是难理解？

> 老实说，我花了很长时间才想出反应。在我过去 10 年的职业生涯中，我从未接触过比其他任何技术更好的技术。这非常令人沮丧，因为我真的很想学习它，而且很明显这个库有腿。

布拉德·弗罗斯特，[http://bradfrost.com/blog/post/my-struggle-to-learn-react/](http://bradfrost.com/blog/post/my-struggle-to-learn-react/)

这不是第一次听人说 React 难学了。然而，我也听到有人说“React 的核心概念很容易理解！”。

那么，到底是哪个？React 其实很难理解吗？这比理解另一个框架难还是难？

没有确切的答案。React 对某些人来说比较容易理解，对不同的人来说就比较难理解了。人们有着不同的背景和不同的“心智模式”(更多理查德·费曼的故事，对不起😅)，也就是说 React 的核心概念对于不同的人会更容易或者更难！

对于那些与 [web](https://www.polymer-project.org/) [组件](https://github.com/skatejs/skatejs)一起工作过的人来说，React 在他们的心智模型中有一个很好的家。然而，对于那些只是偶尔使用 jQuery 的人来说，有很多新概念需要理解，在解释这些概念时必须小心。

如果 React 的许多概念对人们来说是新的，了解人们是如何理解事物是有用的，这样我们可以更有效地解释。

# 如何解释反应过来的事物

我花了一些时间才意识到你理解反应的方式并不是其他人会理解的方式。

这有很多原因——人们来自不同的背景，有不同的经历。他们可能更习惯于 Angular 或 Backbone 的工作方式，也可能习惯于使用 jQuery 直接操作 DOM。他们可能只有有限的 javascript 经验，或者他们可能知道关于 ES6 的一切。重要的是花时间去了解你要解释的人的背景。人们通过把新概念与他们已经知道的事情联系起来来学习新概念，这可以通过好的类比和比较来鼓励。

其他人不一定以同样的方式理解它的另一个关键原因是由于历史和时间。最近我和一个朋友谈论事情变化有多快。“前端的东西移动得太快了！我一年前学了 javascript，下次回来时一切都不一样了！”。随着时间的推移逐渐建立概念使得学习 React 对我来说很简单，但对于同时面对 React、ES6、Webpack 和 JSX 的人来说，这些都是很容易相互混淆的新概念。

# 那么，如何才能更好的解释呢？

如果你不仔细注意这些经历上的差异，你的解释就不会有效。根据你是一对一解释还是在小组环境中解释，有不同的策略来处理这些不同的体验。

定期提问。这给了你一个机会去了解你正在向其解释事情的人。它让其他人参与进来，使解释成为一个互动的过程，而不是被动的过程。问一些鼓励参与的问题。而不是问“这有意义吗？”这样的是或否的问题(经常遇到空洞的点头或“嗯，是啊。”)，问“什么”或“为什么”的问题，比如“在这种情况下会发生什么？”以及“这段代码为什么重要？”准备好改变策略。因为有各种各样的经验，在一对一的环境中，你应该准备好为个人量身定制你的解释。这更好，因为这有助于他们了解知识，并让他们参与更多。

给出正确的详细程度。如果你对某件事有深入的了解，深入探究有趣但不重要的细节是很有诱惑力的。准备好使用抽象概念或跳过不相关的部分，以确保你的解释符合听众的体验(注意:这并不意味着你应该[简化到不正确的程度](https://xkcd.com/803/)

# 例子

具体到 React，对于没有密切关注的人来说，有一些常见的混淆点。

## ES6 vs JSX

同时引入这两个概念，往往会让人感到困惑。查看下面的代码示例，我们可以看到有多个新概念组合在一起

```
import * as React from 'react'; //ES2015 + React
import * as ReactDOM from 'react-dom';  //ES2015 + React
const allCaps = s => s.toUpperCase(); //ES2015
class HelloWorld extends React.Component {  //ES2015 + React
  render() {  //ES2015 + React
    const { name } = this.props; //ES2015
    return (
      <p>Hello, {allCaps(name)}</p>{/* JSX */}
    );
  }
}
ReactDOM.render(<HelloWorld name="Josh"/>, document.getElementById('app')); 
```

如果不熟悉这个新语法，从概述[的哪些部分是 ES6](http://espadrine.github.io/New-In-A-Spec/es6/) 开始会很有帮助。

我还做了一个工具，[“React or es 2015”](https://react-or-es2015.glitch.me/)，来帮这个忙。粘贴一个代码片段，你可以看到哪些部分是反应，JSX，ES2015 或 ES5，并点击每个部分的相关文档。这个工具可能对那些能立即分辨出哪个语法来自哪里的人没有帮助，但是我希望它对很多人有用！

## 为什么创建组件的方式这么多？

React 组件可以被定义为一个类、一个函数，或者通过调用`React.createClass`。这是一个让初来乍到的人反应混乱的点。小心地介绍这个概念——就何时使用它们提供清晰的指导方针,以避免不必要的混淆(你会使用状态吗？使用类:使用函数)。

## 整体模型

这是我最喜欢解释的部分，因为我认为这是在 React 中构建 UI 的最伟大的部分之一。[整体数据流](https://reactjs.org/docs/thinking-in-react.html)模型一般容易理解，理解这个基本概念是一个有用的心智模型。

这也是与用户习惯的其他语言、方法和框架进行比较的好机会。比较和对比这些方法有助于巩固学习者头脑中的概念。

来自[https://Facebook . github . io/flux/docs/in-depth-overview . html # content](https://facebook.github.io/flux/docs/in-depth-overview.html#content)
指出 React 是上图中的视图层有助于澄清 React 工作的模糊性。

通量模型的基本思想是有用的，但要给出正确的细节，不要花太多时间抽象。在实践中观察事物如何遵循这一模型比深入、抽象地解释通量模型更有用。

## 助手库

当为人们创建演示或编写示例时，注意不要依赖与你要表达的观点不相关的助手库。如果你想说明 React 状态管理，请避免使用 lodash、react-router、react-intl 或任何与你要解释的内容没有直接关系的东西。

虽然熟悉 lodash 的人很清楚`_.concat`不是你的例子的重点，但对于不熟悉 lodash 的人来说，这可能会分散注意力，而这正是你想要表达的重点。

## 状态

另一个常见的问题与使用什么类型的状态有关。这对初学者来说越来越困惑，因为有许多新的替代方案——每个都有一些优势([它们很小](https://github.com/jamiebuilds/unstated)、[它们不在主线程中运行](https://github.com/developit/stockroom)、[它们连接到 GraphQL](https://www.apollographql.com/docs/link/links/state.html) ，或者它们只是使用普通的 React 功能，如存储和上下文)。

就个人而言，我发现这些新的发展令人兴奋，但可以理解的是，新的学习者发现这里的选择数量令人苦恼。提供清晰的指导有助于缓解这种情况。选择其中一个并开始学习——当你开始掌握 React 的主要概念时，哪家商店最好的细微差别就变成了一个更相关的问题。

## 一般如何解释

研究表明，人们主动学习比被动学习更有效[2]。主动学习是一种鼓励学习者分析他们知道什么和不知道什么的方式。对于讲解者来说，这意味着鼓励提问，也意味着定期提问以保持学习者的参与。当人们能够控制自己想学的东西时，他们也能更有效地学习，因为这能鼓励参与[3]。

一旦有人参与其中，就可以通过将新的概念与他们已经理解的现有概念联系起来进行学习。一旦新想法在我们的心智模型中找到了位置，学习和记忆新想法就变得更容易了。

# 这一切是为什么？

前端开发很棒，而且一直在变得更好。我们继续让技术和社区变得更好的方法是努力清晰有效地解释事情。更多的人可以学习，成为专家，并分享新的，不同的想法。

交流技术和反应是如何工作的，是其中的一个关键部分。你理解反应的方式并不是其他人理解它的方式，通过一些有意识的努力，我们可以更好地彼此分享我们对事物的想法😁

* * *

👋嗨！我是约书亚·纳尔逊。我是一名开发人员，我关心的是让网络变得更好，为每个人服务，✨

这是一个来自[https://medium . com/@ joshuanelson/react-explaining-explaining-13 a3 Fe 6 e 5b 9d](https://medium.com/@joshuanelson/react-explaining-explained-13a3fe6e5b9d)的交叉帖子

1.  六个简单的部分:由最杰出的老师讲解的物理学要点
2.  [(布朗斯福德，2000 年，第 15-20 页)](http://life-slc.org/docs/Bransford_etal-Learningtheories_2006.pdf)
3.  J.斯科特·阿姆斯特朗(2012)。“高等教育中的自然学习”。[https://faculty . Wharton . upenn . edu/WP-content/uploads/2014/09/Natural-Learning-in-Higher-Education _ 2 . pdf](https://faculty.wharton.upenn.edu/wp-content/uploads/2014/09/Natural-Learning-in-Higher-Education_2.pdf)