# 如何在 ES6 中有条件地构建一个对象

> 原文：<https://dev.to/sanity-io/how-to-conditionally-build-an-object-in-es6-53p0>

最近，我在 [CLIs](https://github.com/sanity-io/podcast-to-sanity) 、 [Express 和无服务器功能](https://github.com/sanity-io/Syntax)中修补播客的 RSS 提要，这涉及到解析和构建具有大量字段和信息的复杂对象。因为您处理的是来自不同来源的用户生成的数据，所以不能保证所有字段都被填充。有些字段也是可选的。

之前我会通过有条件地在一个对象上应用新的键来处理这个问题，就像这样:

```
function episodeParser(data) {
  const { id, 
    title,
    description,
    optionalField,
    anotherOptionalField
  } = data
  const parsedEpisode = { guid: id, title, summary: description }
  if (optionalField) {
    parsedEpisode.optionalField = optionalField
  } else if (anotherOptionalField) {
    parsedEpisode.anotherOptionalField = anotherOptionalField
  }
  // and so on
  return parsedEpisode
} 
```

Enter fullscreen mode Exit fullscreen mode

这并不完全顺利(但它的工作)。我还可以做一些漂亮的事情，比如循环对象键等等，但是这需要一些更复杂的代码，而且你也不能很好地理解数据对象是什么。

然而，ES6 中的新语法再次派上了用场，我发现了一种模式，可以将代码改写成这样:

```
function episodeParser({
    id, 
    title, 
    description = 'No summary', 
    optionalField, 
    anotherOptionalField
}) {
    return {
      guid: id,
      title,
      summary: description,
      ...(optionalField && {optionalField},
      ...(anotherOptionalField && {anotherOptionalField})
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将这个函数付诸实施，它看起来会像这样:

```
const data = { 
  id: 1, 
  title: 'An episode', 
  description: 'An episode summary', 
  anotherOptionalField: 'some data' 
}
episodeParser(data)
//> { guid: 1, title: 'An episode', summary: 'An episode summary', anotherOptionalField: 'some data' } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数有几个特性。第一个是参数对象析构，如果您想处理函数中的大量参数，这是一个很好的模式。第二个是三点[展开语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) ( `...`)，这里用于在条件为真时“展开”对象，我们检查 AND 运算符(`&&`)。您最终得到的是一个简洁明了的函数，它也很容易测试。

你可以在我们的 podcast feeds 实现中看到它对 [express.js](https://github.com/sanity-io/Syntax/blob/master/routeHandlers/rss.js) 和 [netlify lambdas](https://github.com/sanity-io/Syntax/blob/master/functions/rss.js) 的作用。