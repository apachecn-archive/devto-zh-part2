# 专业提示:使用 Promise.then 进行函数组合

> 原文：<https://dev.to/shalvah/pro-tip-using-promisethen-for-function-composition-324i>

最近一段时间，我成了函数式编程的粉丝(尽管我对它还不是很了解)。随着时间的推移，我已经养成了用 JavaScript 处理承诺的习惯。我试着构造我的代码，所以它看起来像这样:

```
let tweetsToAttendTo = fetchTweets()
  .then(removeDuplicates)
  .then(removeTweetsIveRepliedTo)
  .then(fetchRelevantTweets); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我不仅使用`then`来控制流，还将一个函数的输出通过管道传输到另一个函数的输入(从而创建一个管道)。我发现这种模式使得代码更容易阅读和推理，并且去掉了不必要的填充符，如箭头函数和下面的额外变量:

```
fetchTweets()
  .then(tweets => removeDuplicates(tweets)); 
```

Enter fullscreen mode Exit fullscreen mode

不过要小心！使用这种模式时，有几件事您必须知道:

1.  应用此方法取决于前一个函数的结果是否是下一个函数的输入
2.  使用对象方法时要小心。例如，下面两段代码不是一回事:

```
getModels().then(r => manager.applyFilters(r))

// any calls to `this` in `manager.applyFilters` will return undefined
getModels().then(manager.applyFilters) 
```

Enter fullscreen mode Exit fullscreen mode

最终还是不要勉强。我经常使用它，但是如果它不起作用(F1 的输出不是 F2 的输入，F2 的行为不同，我需要做特殊的错误处理)，我就放弃它。记住，没有银子弹！