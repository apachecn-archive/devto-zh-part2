# TIL:CSS 属性选择器有一个不区分大小写的模式

> 原文：<https://dev.to/stefanjudis/til-the-css-attribute-selector-has-a-case-insensitive-mode-1ncb>

我在 GitHub 上关注 caniuse.com 项目，这意味着我会收到关于网络平台增加和更新浏览器支持的通知。今天，我偶然发现了一个问题，包括[新的 CSS 级选择器](https://github.com/Fyrd/caniuse/issues/1058)，它们已经或将要包含在【caniuse.com】的[中。](https://caniuse.com/)

结果是有很多新的选择器正在路上，其中一个有趣的是一个标志，它使得属性选择器不区分大小写。

```
/**
 * matches:
 * <div class="foo">...</div>
 * <div class="Foo">...</div>
 * <div class="fOo">...</div>
 * ...
 */
[class=foo i] {
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的缺点是，[边缘还不支持这个选择器](https://caniuse.com/#feat=css-case-insensitive)。

如果你想玩这个，我创建了一个快速代码笔。我能想到的唯一情况是，当你必须处理用户生成的内容时，用户输入不准确数据的可能性很高。我很想了解更多关于这方面的其他使用案例——如果你有想法，请告诉我。

*已编辑:[张秀坤指出](https://twitter.com/DKundel/status/1039102339041378304)这对于使用`value`属性的输入字段中的用户生成内容确实有用。*

```
/**
 * matches:
 * <input value="hello world">
 * <input value="hello World">
 * <input value="hElLo WoRlD">
 * ...
 */
[value="hello world" i] { /* ... */ } 
```

Enter fullscreen mode Exit fullscreen mode