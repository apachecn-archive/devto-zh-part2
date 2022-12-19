# 测角镜快速指南

> 原文：<https://dev.to/bnevilleoneill/a-quick-guide-to-angularjs-scopes-54je>

[![](../Images/83a3ef86299182ea97ff82591ef7048c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xvIjQuiZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGmMtKznzJ1dS8sSzxzR3ow.png)

AngularJS 应用程序通常依靠控制器来控制应用程序中的数据流。然后，这些数据被传递到视图中进行渲染。为了将控制器和视图连接在一起，Angular 使用了一个名为 **scope** 的特殊对象。这个 scope 对象充当表达式的执行上下文，并模仿 DOM 结构分层排列。

在模板链接阶段，指令在作用域上设置 *$watch* 表达式。这些允许在属性发生变化时通知指令。这样，指令可以将更新后的值呈现给 DOM。

控制器和指令都可以访问作用域，但不能相互访问。现在，作用域确保了来自指令和 DOM 的控制器的正确封装。

[https://medium . com/media/bed 1c 2e F9 ed 019 b 888d 767 b 2552 fc 7 ef/href](https://medium.com/media/bed1c2ef9ed019b888d767b2552fc7ef/href)

正如我们在示例中看到的，控制器既可以将数据写入范围，也可以为其分配行为。

它首先将“LogRocket”赋给作用域的“initial”属性。然后将“sayIlove()”行为分配给“love！”按钮。“sayIlove()”方法可以读取`initial`属性并创建一个`token`属性。在这个例子中，我们可以看到，当绑定到 HTML 输入小部件时，作用域上的属性可以自动更新。

当我们最终渲染''时，我们实际上:

*   正在检索与定义“”的 DOM 节点关联的范围，并且
*   根据上面检索的范围计算“token”表达式，并将结果赋给封闭 DOM 元素的文本。

总而言之，以一种非常简单的方式，范围可以被看作仅仅是用于呈现视图的数据。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### **范围层级**

每个 AngularJS 应用程序都有一个**根作用域**，并且可以有任意数量的子作用域。每当创建 AngularJS 应用程序时，都会创建根作用域，但是之后，指令可以创建新的子作用域。当一个新的子作用域被创建时，它被添加为其父作用域的子作用域。这种子作用域树通常与它们所在的 DOM 平行。

这种效果的一个很好的例子是动态生成的列表。控制器从任何来源获取列表的每个元素，然后为每个元素创建一个新的范围。这个新的范围然后被传递给视图，在那里它将被绑定到一个新创建的 DOM 元素。正如我们在下一个例子中看到的:

[https://medium . com/media/97 C9 CBC 66139501 C5 e 743 BC 0 bb 2 deb F9/href](https://medium.com/media/97c9cbc66139501c5e743bc0bb2debf9/href)

[![](../Images/627b76fbe2e89922587f5b7697d4386d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--si9KyoZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/680/1%2AMPB_akdyDmQH913vjgll9w.png) 

<figcaption>一棵普通的 DOM 树，检查' ng-scope '中的所有元素。</figcaption>

如果我们仔细观察 DOM 树，我们可以看到 AngularJS 将“ng-scope”添加到所有附加了作用域的元素中。所有这些子作用域都是必要的，因为根据计算表达式的作用域，会产生不同的结果。

' $scope '数据属性被附加到 DOM，因此，它可以被检索用于调试目的。要在调试器中检查特定的范围，我们可以使用浏览器的内置调试器。这主要有三个步骤:

*   右键单击浏览器中感兴趣的元素，并选择*检查元素*选项。这将打开调试窗口，突出显示单击的元素。
*   如果尚未选择*控制台*选项，则选择该选项。调试器允许我们使用变量“$0”访问控制台中当前选定的元素。
*   若要检索与当前元素关联的范围，请执行“angular.element($0)”。“scope()”命令。

[![](../Images/041f953dda443162c107058cf0b9d6a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MD9gfe7q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/855/1%2Ab8X4q0ZVIa_DxFeNFRszYQ.png) 

<figcaption>调试一个范围对象。</figcaption>

#### **范围事件传播**

与 DOM 事件类似，作用域也可以传播事件。该事件可以广播给子范围，也可以发送给父范围。下面的例子说明了如何做到这一点。

[https://medium . com/media/c5cf 1290 E4 fdf 16843 c48e 2358 b8 AE 0/href](https://medium.com/media/c5cf1290e4fdf16843cc48e2358b8ae0/href)

' $emit '函数用于通过作用域层次结构向上传播事件。正如我们在示例中看到的，当单击“emit”按钮时，事件被传播到较高的作用域层，这意味着根作用域。

“$broadcast”函数将事件向下传播到每个子作用域及其子作用域。在我们的示例中，当单击“broadcast”按钮时，事件被传播到较低的作用域层，这意味着子作用域和第二个子作用域。

#### **范围生命周期**

在我们结束之前，让我们讨论一下范围生命周期。

Angular 使用我们称之为 *$digest — $apply* 循环的东西来处理事件的生命周期。

AngularJS 本身不知道模型修改。发生这种情况是因为当浏览器调用 Javascript 时，代码在 AngularJS 执行上下文之外运行。要进入角度执行上下文，需要调用' $apply '方法。对' $apply '的调用将计算传递给它的表达式，然后执行' $digest '。

“$digest”是一个内部循环，它贯穿应用程序并执行“$watch”表达式，并将返回的值与作用域中已有的上一个值进行比较。如果值不匹配，将触发一个侦听器。这个循环将一直运行，直到不再有侦听器被激发。

例如，像“$scope.company=LogRocket”这样的赋值不会立即触发“$watch”。这是' $digest '责任，它可能触发也可能不触发' $watch '，这取决于' $scope.company '上已有的值。

这种效果有利于 AngularJS 的性能，因为它将所有模型更新合并到一个单一的“$watch”通知中。它还确保我们不会进入不一致的状态，因为在给定的时间只能运行一个' $watch '通知。对于额外的模型修改，必须触发新的“$digest”循环。

#### **结论**

在这篇文章中，我们谈到了 AngularJS 作用域，以及它们是 AngularJS 应用程序的重要组成部分。我们查看了范围层次结构和事件，并触及了范围生命周期的基础。

我希望这篇文章能够让你开始使用角度观测仪，并更好地理解它们在每个角度应用中的重要性。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *