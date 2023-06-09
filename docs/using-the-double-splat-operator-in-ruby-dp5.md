# 在 Ruby 中使用双 splat 运算符

> 原文：<https://dev.to/michaelherold/using-the-double-splat-operator-in-ruby-dp5>

double-splat 操作符是 Ruby 2.0 中我最喜欢的新增功能之一。出于某种原因，这两个小星号比方法中可选的`Hash`参数更让我高兴。即便如此，在方法定义之外，我并没有意识到 double-splat 操作符的用途，直到我在 Hashie 中发现了一个 [bug。我非常喜欢这个结果，所以我想花一点时间来分享我的发现。](https://github.com/intridea/hashie/issues/353)

## JavaScript 中的相关行为

如果您编写任何现代 JavaScript，您可能会看到以下模式:

```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return {
        ...state,
        visibilityFilter: action.filter
      };
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`...` spread 操作符非常适合在不修改原始对象的情况下从其他对象创建新对象。当我构建我的第一个基于 Redux 的 JavaScript 应用程序时，我真的很喜欢这样，因为它消除了很多围绕`Object.assign`的仪式。

有时您还没有足够的信息来创建对象。当这种情况发生时，Hash 是一个很好的对象代理，直到你更好地理解你的领域。在这些情况下，我想在 Ruby 中使用相同的行为来构建散列。有段时间我是靠`Hash#merge`的，后来在哈希上看到这个 bug。

## 双斜线运算符

要解决这个问题，您可以以不同的方式使用 double-splat 运算符。看看下面的代码:

```
def todo_app(state = initial_state, action)
  case action.type
  when SET_VISIBILITY_FILTER then { **state, visibility_filter: action.filter }
  else state
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段的工作方式与 JavaScript 版本完全相同！我认为这是对我最初问题的一个非常优雅的回答。

有趣的是，如果你编译这段代码的指令序列，你会发现它使用了 C 版本的`Hash#merge`。我很高兴我得到了`state.merge(visibility_filter: action.filter)`的语义，但是可以用我的 JavaScript 编写朋友们容易理解的方式来表达它。

如果在 Ruby 中有一种方法可以做某件事，那么至少还有一种方法。我认为这是许多人喜欢 Ruby 的原因之一:他们可以用最容易的方式表达自己的想法。

这就是我今天给你的全部。你知道这个例子中的双 splat 操作符吗？

在 Ruby 中使用双 splat 操作符的帖子[最早出现在](https://michaeljherold.com/2018/08/17/using-double-splat-operator-ruby/)[的 Michael Herold](https://michaeljherold.com) 上。