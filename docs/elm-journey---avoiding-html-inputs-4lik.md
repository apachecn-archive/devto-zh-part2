# Elm 之旅-避免 HTML 输入

> 原文：<https://dev.to/kspeakman/elm-journey---avoiding-html-inputs-4lik>

当我使用 Elm 时，我已经开始想办法避免在我的应用程序中使用 HTML 输入。我想分享更多关于这一点和我到目前为止的进展。

## 为什么？

HTML 输入有自己独立的状态，与你自己的 Elm `Model`分开。当这些不同时，您可以观察到一些令人困惑且难以追踪的行为。

我们遇到的第一个例子是单选按钮。如果你给几个单选按钮相同的`name`，那么**浏览器**将把它们作为一组，并跟踪用户最后选择的是哪一个。但是，除非您设置点击事件并适当地更新模型，否则 Elm 不会意识到这一点。这导致了一些最初的混乱，因为即使模型没有更新，看起来一切正常。我们几个有角度经验的人很难理解发生了什么，因为我们太习惯于双向绑定了。

该问题的另一个常见例子是用户快速输入的文本输入。在大多数情况下，这不是问题。但是我们遇到了 USB 条形码扫描仪，它输入数字非常快。

例如，如果用户非常快速地键入“ABC”*，那么“b”将会被删除。原因如下。*

 *1.  用户键入“a”，浏览器将输入值更改为“a”
2.  `UserEntered "a"`送到榆树
3.  用户键入“b”，浏览器将输入值更改为“ab”
4.  `UserEntered "ab"`送到榆树
5.  Elm 处理`UserEntered "a"`，更新模型，重新渲染
6.  Elm 将输入值更改为“a”(替换“ab”)
7.  用户键入“c”，浏览器将输入值更改为“ac”
8.  `UserEntered "ac"`送到榆树
9.  “ab”将被处理，但稍后会被“ac”覆盖
10.  对用户来说，它看起来像是“b”被丢弃了。

出现这样的问题是因为 HTML 输入有自己的状态，可以从许多不同的地方一次更新。在 Elm 应用中，我们更愿意成为国家的守护者。

不幸的是，对于文本输入来说，这不是一个已解决的问题，正如您将在下面看到的，但是其他类型的输入可以用只显示的 HTML 来代替。

### 单选按钮和复选框

一旦你意识到问题，你可以继续使用这些输入...只是不要设置`name`属性(也没有必要设置`value`)。但是为了样式的灵活性，我倾向于用只显示的元素来替换它们。例如，您可以使用图标和文本。下面是一个使用语义 UI 的复选框替换示例。

[![active toggle switch](img/404a0b555fa018fd499d215169a1a8a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ezHzrdyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dyjax4src8lpdkbxdwrj.png)T3】

```
div []
  [ i [ class "input-sized blue toggle on icon" ] []
  , b [ class "gap left" ] [ text " Active" ]
  ] 
```

Enter fullscreen mode Exit fullscreen mode

[![inactive toggle switch](img/5fcc1fa76d54be3ce9b9c07c580802eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nVYxn_3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o8dqnudoex0zpxwq512l.png)T3】

```
div []
  [ i [ class "input-sized horizontally flipped grey toggle on icon" ] []
  , span [ class "gap left" ] [ text " Inactive" ]
  ] 
```

Enter fullscreen mode Exit fullscreen mode

我将模型属性转换成 HTML 非常简单，就像这样:

```
if something.isActive then
  activeToggle
else
  inactiveToggle 
```

Enter fullscreen mode Exit fullscreen mode

*语义用户界面有一个`off toggle`图标，但我不想用它，因为它被淡化了，看起来不再像一个控件。所以我用的`on toggle`翻过来变成了灰色。*

以上代码片段中的 CSS 类来自语义 UI。除了两个额外的风格，我用了微小的视觉调整。

```
.gap.left { margin-left: 3px; }

/* same size as other inputs, i.e. on same line */
i.input-sized.icon, i.input-sized.icons { font-size: 2.7em; line-height: 1; vertical-align: middle } 
```

Enter fullscreen mode Exit fullscreen mode

### 选择

有大量不同风格的下拉列表替换。大多数 UI 样式工具包(包括语义、引导和 Zurb 基础)都有一个。我喜欢语义的一个原因是，我可以完全用 HTML 类(没有 JS)来设计它的所有状态，这与 Elm 完全吻合。但是，需要记住的一点是性能。如果你有这样的场景，你需要在一个页面 ***** 的一个或多个下拉列表中包含大量数据，那么 javascript 渲染的下拉列表将会比浏览器渲染的`select`慢得多。

这些都不是很好的设计选择，但有时却是必须的。

我过去通常讨厌样式化的下拉列表替换。但是当时的原因是因为我仍然依赖于直接从 HTML 元素中使用状态。这些替换用了非常做作的方式来保存状态，这通常需要我编写额外的代码。而从原生 HTML 元素获取状态“刚刚好”。然而，由于我在使用 Elm 时只关心显示方面，所以一个样式化的替代物就可以了...只要它足够健壮。

### 文本输入

这个问题要困难得多，而且还没有解决。最初我考虑使用一个`div`并处理 onKeyUp，但是后来你如何处理光标定位(在字母之间点击来移动光标)？你真的想处理退格键、删除键、箭头键等等吗？“contenteditable”也没有帮助，因为它也有同样的输入状态问题。

现在我使用`defaultValue`属性来解决上面提到的快速输入问题。如果没有设置`value`，浏览器会显示`defaultValue`。然而，defaultValue 面临着新的问题，即您有大量动态生成的文本输入。Elm 的虚拟 DOM 实现显然有一个不能正确处理这种情况的错误。在这种情况下，我只使用`value`，大多数用户打字速度不够快，无法体验这个问题。

如果我试图解决这个问题，我想我会使用文本输入作为用户输入，但是在只显示的 div 前面隐藏它(不透明度 0%)。然后让 div 看起来像一个输入，并在用户输入文本时从 Elm 接收更新。因此，输入仅用作“源”,但显示的是从 Elm 呈现的 div 内容。但那只是现阶段一个模糊的想法。

### 其他输入:数字、日期等。

我通常发现这些在不同的浏览器中实现不一致。因此，我没有写一堆代码来解决这种不一致，而是对所有事情都使用文本输入。

## 结论

对我来说，这仍是一项进行中的工作。主要启示是，在使用 Elm 时，我不需要 HTML 元素来为我保存状态。事实上，使用有状态元素已经不止一次地在团队中引起了众所周知的“野鹅追逐”。在一个理想的场景中，我希望 HTML 仅用于视觉目的。然而，文本输入仍然是一个棘手的问题，因为它对我来说处理了很多重要的数据输入。如果有人有任何进一步的想法，我很想听听。

/∞*