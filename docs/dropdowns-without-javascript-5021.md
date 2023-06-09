# 没有 Javascript 的下拉菜单

> 原文：<https://dev.to/briankephart/dropdowns-without-javascript-5021>

几个月前，我不得不添加一个下拉菜单到一个项目，该项目没有使用内置下拉菜单的前端框架。我决定试试能否不用 JavaScript 制作一个下拉菜单。我的方法是这些帖子中的混合:

*   [https://design hooks . com/how-to-create-pure-CSS-drop downs-without-JavaScript/](https://designhooks.com/how-to-create-pure-css-dropdowns-without-javascript/)
*   [https://css-tricks.com/solved-with-css-dropdown-menus/](https://css-tricks.com/solved-with-css-dropdown-menus/)

## 点击打开

我做的菜单需要点击才能打开。要完成类似的事情，创建一个带有可见标签的隐藏复选框。这里，我们将使用 [dev.to](https://dev.to) 徽标作为标签。单击标签时，复选框将被选中，即使它保持隐藏状态。

这段代码使用 CSS 通用兄弟组合符`~`来选择跟在`input:not(:checked)`后面的`#collapse-menu`，并将其高度缩放为 0。当输入被检查时，该样式不再适用，因此`scaleY`值返回到默认值 1，菜单出现。过渡设置为 0.3 秒，以便菜单逐渐打开，而不是立即打开，`transform-origin`属性确保菜单从上到下出现，而不是从中心展开。

[https://codepen.io/brian-kephart/embed/xaEEKp/?height=600&default-tab=result&embed-version=2](https://codepen.io/brian-kephart/embed/xaEEKp/?height=600&default-tab=result&embed-version=2)

## 悬停打开

如果你喜欢当你悬停在图标上时打开菜单，那么就不需要`<checkbox>`或`<label>`标签。相反，将`#collapse-menu`缩放到 0 以在不悬停时隐藏它(这意味着当悬停时它将返回到全高)。当图标悬停时，您还需要将`#collapse-menu`缩放到 1。我们将再次使用一般的兄弟组合符`~`来完成这个任务。最后，你应该引入一个过渡延迟。否则，当用户将指针从图标移动到列表时，菜单将开始折叠，然后重新打开。微小的延迟(本例中为 0.2 秒)可以让事情变得顺利。

[https://codepen.io/brian-kephart/embed/MqjjqO/?height=600&default-tab=result&embed-version=2](https://codepen.io/brian-kephart/embed/MqjjqO/?height=600&default-tab=result&embed-version=2)

为了便于学习，我有意保持这些例子简单，但是在使用这些元素时不要忘记考虑可访问性！不要只依赖悬停行为，除非你确定所有用户都可以使用该行为。例如，我之前提到的应用程序将行`&:focus-within { transform: scaleY(1); }`添加到了用于`#collapse-menu`的 CSS 中，这样当用户使用键盘浏览网站时，当他们切换到其中一个选项时，就会看到菜单展开。

编辑:下面有评论抱怨这些解决方案的可访问性。这些问题在上面发布的一个链接中得到了解决，我在这里再次发布:[https://css-tricks.com/solved-with-css-dropdown-menus/](https://css-tricks.com/solved-with-css-dropdown-menus/)

在纯 CSS 中，下拉菜单可以做更多的事情。在评论里加上你最喜欢的招数！