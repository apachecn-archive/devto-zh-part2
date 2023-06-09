# 根据参数个性化您的页面

> 原文：<https://dev.to/gsto/personalize-your-page-based-on-params-3l6>

如果你是网飞或亚马逊的常客，那么你应该很熟悉他们如何利用他们对你的了解来定制他们的平台以满足你的口味。个性化是一种有效的营销技巧。这会让你成为一个更积极的顾客，更有可能掏出你的钱包。

但你没有数据科学家团队或昂贵的企业级营销技术。你如何将个性化应用到你的业务中？

好消息！这些都不是必需的。你甚至不需要任何 JavaScript 库或框架！下面是一个可以开始的例子:根据查询参数更改页面。

## 项目:改变标题和隐藏表格

我们将创建一个简单的登录页面，然后进行一些优化:

如果我们知道这个人是订阅者，我们不会给他们看选择加入的电子邮件表单。我们将改变标题，使其更加具体。
一个[市场开发人员的完美项目](https://glennstovall.com/what-is-a-marketing-developer/)。你的文案越具体，就越能引起用户的共鸣。想象一下，如果这篇文章的标题是“嘿，马特，这是如何优化你的优质图标集的登陆页面！”对你们这些人来说，那不是很好吗？

## 结果

这是最终产品。下面我会解释它是如何工作的。

[https://codepen.io/GSto/embed/QVOPgG?height=600&default-tab=result&embed-version=2](https://codepen.io/GSto/embed/QVOPgG?height=600&default-tab=result&embed-version=2)

## 观察它的行动:

[![Personalization Demo](img/eb7b6f90889fce20ac369619b7c0410c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kCzcBg7f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d3vv6lp55qjaqc.cloudfront.net/items/2k0w3K3O1j3G3j0L1N31/Screen%2520Recording%25202018-09-08%2520at%252011.59%2520AM.gif)

## 现在了解它的工作原理:

### 首先解析查询字符串:

获取查询字符串(在“？”之后的所有内容)在 URL 中。)这里引用一下:

`windows.location.search`

然后，将该字符串解析为一个对象，以便可以访问特定的变量。这里有一个函数来处理这个问题:

```
/* Converts a query string into an object. 
 * example input:  ?src=agency
 * results: { src: "agency" }
 */
function parseQuery(str) {
  //Remove '?' from beginning.
  str = str.substring(1) 
  //split the string into key value pairs
  var pairs = str.split("&")
  //convert them into an object
  return pairs.reduce(function(map, pair) {
      console.log(pair)
      var kv = pair.split("=")
      var key = kv[0]
      var value = kv[1]
      map[key] = value
      return map
  },{})
} 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，每个值都是一个字符串。如果您想将一个值解释为整数或浮点数，您需要使用 ParseInt 和 ParseFloat。对于布尔值，可以使用 value == 'true '

### 然后，显示和隐藏元素

现在我们有了一个可用的对象，我们可以开始操作页面了。在这些示例中，我们将使用本机 document.querySelector 和 document.querySelectorAll 方法。使用本机方法可以保持代码的可移植性。您可以在任何地方使用这些代码，从单个页面应用程序到 Google Tag Manager。

这里的功能隐藏了我们的电子邮件选择。如果我们已经有了访问者的电子邮件地址，我们就不需要再纠缠他们了。

```
if(profile.subscriber) {
  document.querySelector(".course-opt-in").style.display = "none"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 最后，更新页面上的文字

同样，我们可以更新文本。下面是一个例子，它查找特定类的每个实例，并更改该元素中的文本。首先，进行更新的辅助函数:

```
function updatePlurals(text) {
  var targets = document.querySelectorAll(".target-plural");
  for(var i = 0; i < targets.length; i++) {
    targets[i].innerText = text;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后是调用所述函数的 switch 语句。

```
switch(profile.src) {
  case "agency":
    updatePlurals("agencies")
    break
  case "freelancer":
    updatePlurals("freelancers")
    break
  case "startup":
  default:
    updatePlurals("startups")
    break
} 
```

Enter fullscreen mode Exit fullscreen mode

## 练习:如何将这一点应用到你的网站中？

有哪些方法可以让你的页面更能引起访问者的共鸣？你的内容越能引起共鸣，你对你的观众就越有帮助和说服力。以下是一些开始的想法:

*   **头条新闻。**您可以在哪里更新标题，使其更加具体？
*   **选择加入。**为了让用户体验更加愉快，您可以在哪里取消选择加入？你能根据你对它们的了解展示不同的选择加入吗？(例如，你可以要求他们注册免费试用，而不是要求他们选择订阅时事通讯)。
*   **行动号召。**除了标题，行动号召是页面上最重要的文字。你如何让他们更有说服力？

*(文章原贴于此:[如何基于 URL params 更新内容；不需要库](https://glennstovall.com/how-to-update-content-based-on-url-params-no-libraries-required/) )*