# 我如何成为一名 javascript 事件魔术师

> 原文：<https://dev.to/ekansss/how-i-become-an-javascript-event-magician-n11>

几年前，当我学习 javascript 时，像许多人一样，我用一个著名框架开始了 JS:jQuery。事件、ajax、dom 操纵，对我来说是一个全新的世界！

它简单易用，具有良好学习曲线，但现在我清楚地看到了它的局限性，像许多框架一样，它非常慢，而且有了新的 navigator，许多 jQuery 特性都直接包含在浏览器中，所以为什么不使用 vanillaJS 并加快我的应用程序和网站。

但是有一个浏览器 api 几年来没有什么变化:javascript Event。处理动态添加的元素(委托)或使用自定义事件仍然令人头疼！这就是为什么我开发了一个工具来像变魔术一样使用这些功能。

所以我的 MagicEvent 来了，它扩展了事件功能，用类似 jQuery 的语法简化了事件，简化了延迟和自定义事件管理。

```
MagicEvent.on("click", ".clickme", myClickFunc);
MagicEvent.on("slideswitch", ".diapo", slideSwitchHandler); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我只设置了两个监听器，一个用于所有点击。clickme 元素，每个元素上各有一个。当我发出一个 slideswitch 自定义事件时，它们对你来说没有区别！

```
MagicEvent.emit("click", link);
MagicEvent.on("slideswitch", diapoDiv);
MagicEvent.on("slideswitch", diapoDiv, {next: 3}); 
```

Enter fullscreen mode Exit fullscreen mode

这次我选择发射事件，这很简单，我只需要选择一个事件和一个元素，也不需要更多的工作，自定义事件上的事件，如果我需要，我可以开始自定义数据，这很容易！

所以如果你想试试，回购在这里！尽情享受吧。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [埃坎斯](https://github.com/ekanSSS) / [魔法事件](https://github.com/ekanSSS/MagicEvent)

### 像魔术师一样使用 javascript 事件

<article class="markdown-body entry-content container-lg" itemprop="text">

# MagicEvent

MagicEvent 是 vanillaJS 中一个简单快速的事件处理程序，它本身就可以处理动态创建的元素！成为魔术师！

## 它是如何工作

这很简单，只需在你的页面中调用 MagicEvent.js，然后开始游戏

```
<script type="text/javascript" src="pathtoassets/MagicEvent.js"></script>
<script type="text/javascript">
  //here start the fun
<script>
```

Enter fullscreen mode Exit fullscreen mode

### 我们能做什么？

#### 听一个事件

要注册事件侦听器，只需使用 on 方法

```
  //place an event listener for click on a tag
  MagicEvent.on("click", "a", myLinkClickFunc);
```

Enter fullscreen mode Exit fullscreen mode

#### 一次多个事件

它一次性处理多个事件绑定

```
  //place an event listener for click, hover and touch on a tag
  MagicEvent.on("click hover touch", "a", myLinkClickFunc);
```

Enter fullscreen mode Exit fullscreen mode

#### CSS 选择器

您可以通过 CSS 选择器直接选择您的元素

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/ekanSSS/MagicEvent)

即使它工作得很好，我仍然在努力，所以任何反馈都是非常感谢的！