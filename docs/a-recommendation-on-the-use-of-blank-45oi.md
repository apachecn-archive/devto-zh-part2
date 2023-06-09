# 关于使用 _blank 的建议

> 原文：<https://dev.to/webdva/a-recommendation-on-the-use-of-blank-45oi>

对用户体验(UX)感兴趣的开发者指南。

# 背景

`<a>` HTML 元素/标签的`target`属性使开发人员能够控制如何在浏览器中打开链接。我们关心这个属性的两个值，`_self`和`_blank`。

当没有值被提供给`target`属性时，那么`_self`将是所使用的值，因为它是默认值。它的使用指示用户的浏览器在当前标签中打开`<a>`链接。

将`_self`值用作默认值的直观示例:

```
<a href="/arbitrary-url">arbitrary text</a> 
```

Enter fullscreen mode Exit fullscreen mode

相比之下，`_blank`值指示用户的浏览器在新的浏览器标签中打开`<a>`链接，而不是当前标签。

```
<a href="/arbitrary-url" target="_blank">arbitrary text</a> 
```

Enter fullscreen mode Exit fullscreen mode

# 推荐

已经确定的是，在使用`<a>` HTML 标签时，为`target`属性使用`_blank`值是极不可能的。然后，您可以假设在十次中有九次，使用`_blank`值是不必要的。

作出这一决定的原因是，默认值`_self`的使用提供了可接受的用户体验，因为它使用户能够选择以他们喜欢的方式打开`<a>`链接，假定用户具有现代浏览器技术，这使得根据偏好或环境选择打开`<a>`链接的这种手段成为可能。

# 用例示例

## 一个莫须有的使用例子

当网站的当前页面上的内容非常少时，很有可能使用`target`属性的`_blank`值是不必要的。专门用于介绍即将推出的产品或服务的介绍性登录页面就是这样一个例子。

## 一个保修使用的例子

作为使用`target`属性的`_blank`值的合适例子，我有一个[视频游戏](https://webdva.itch.io/pantsu-versus-baka)，它有一个反馈表单，允许用户访问一个新页面，一个 Twitter 个人资料。

[![Feedback form](img/19ab7008ef81b3dc8e0105f06d91efb2.png "<a> link that opens Twitter in a new tab")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bf8rGyqN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k3xnzhmh2ly1nu1gn5vs.PNG)

因为这是一个 HTML5 视频游戏，所以假设或相信保存视频游戏的当前状态是非常必要的，例如不要使用`<a>` HTML 标签链接来破坏包含状态的标签。因此，在这种情况下，使用`target`属性的`_blank`值是首选的，也是可以接受的。当用户点击指向 Twitter 个人资料的链接时，会打开一个新的浏览器标签，而不是包含当前视频游戏状态的当前浏览器标签。

# 灵感

作为开发人员，你有很大的影响力和权力。关注你提供给用户的体验，并对用户感同身受。