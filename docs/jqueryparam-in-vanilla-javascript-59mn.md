# 普通 JavaScript 中的 jQuery.param()

> 原文：<https://dev.to/mornir/jqueryparam-in-vanilla-javascript-59mn>

假设我们想要向 Google Translate REST 端点`https://translation.googleapis.com/language/translate/v2`发出 GET 请求。

我们需要传递源和目标语言、要翻译的文本、格式和我们的 API 密钥。

```
 const params = {
                  key: 'XXX',
                  q: 'I like coding',
                  target: 'fr',
                  source: 'en',
                  format: 'text'
                } 
```

Enter fullscreen mode Exit fullscreen mode

我们的 GET 请求 URL 应该是这样的:
`https://translation.googleapis.com/language/translate/v2?key=XXX&q=I+like+coding&target=fr&source=en&format=text`

为了获得 URL，我们需要序列化 params 对象。使用 jQuery，我们可以简单地编写`jQuery.param(params)`。

在普通 JavaScript 中，我们可以使用 URLSearchParams 构造函数，它返回一个 URLSearchParams 对象。

问题是 URLSearchParams 对它接受的格式有很大的限制。例如，我们不能只传递给它我们的 params 对象。它需要采用以下格式:

`[ ["key","XXX"], ["q","I like coding"], ["target","fr"], ["source","en"],["format","text"] ]`

不要担心，EcmaScript 2017 给了我们 Object.entries 方法，它将我们的对象转换成我们需要的确切格式:

```
Object.entries(params) // [ ["key","XXX"], ["q","I like coding"], ["target","fr"], ["source","en"], ["format","text"] ] 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以这样初始化我们的 URLSearchParams 对象:

```
const URLparams = new URLSearchParams(Object.entries(params))
urlParams.toString() // "key=XXX&q=I+like+coding&target=fr&source=en&format=text" 
```

Enter fullscreen mode Exit fullscreen mode

我们终于可以提出要求了。请注意，您可以直接将 URLparams 对象附加到 URL 字符串，而无需调用 toString()方法。但是不要忘记在它前面加上`?`字符。

```
fetch('https://translation.googleapis.com/language/translate/v2?' + URLparams) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！感谢你阅读我的帖子，如果你知道更好的/其他的方法来实现这一点，请在下面的评论区告诉我。

## 关于浏览器支持的说明

所有现代浏览器都支持 Object.entries 和 URLSearchParams，并且可以在 IE11 中多填充: [Object.entries 多填充](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries#Polyfill)， [URLSearchParams 多填充](https://github.com/jerrybendy/url-search-params-polyfill)