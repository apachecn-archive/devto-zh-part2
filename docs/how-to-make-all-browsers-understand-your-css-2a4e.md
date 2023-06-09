# 如何让所有浏览器都理解你的 CSS

> 原文：<https://dev.to/neshaz/how-to-make-all-browsers-understand-your-css-2a4e>

你有没有想过 CSS 中的 **-moz-** 或者 **-webkit-** 标记是什么意思？如果你有，那你就来对地方了！这些标记被称为**厂商前缀**。

## 关于厂商前缀

让我们来回答这个问题:什么是厂商前缀？简单地说，供应商前缀是一种在所有浏览器完全支持 CSS 新特性之前，浏览器支持新特性的方式。

当 CSS3 变得流行时，各种各样的新特性开始出现。不幸的是，并非所有浏览器都支持它们。厂商前缀帮助开发人员使用这些新特性，并让他们立即得到支持**,而不必等待每个浏览器都可以使用它们。**

供应商前缀不是黑客，你可以随意使用。

检查哪个属性可以在没有供应商前缀的情况下使用的一个好方法是检查[can use](https://caniuse.com/)服务。在那里，您可以看到哪个浏览器当前支持哪个属性。

## 前缀

主要浏览器使用以下前缀:

*   **-webkit-** Chrome，Safari，较新版本的 Opera，几乎所有的 iOS 浏览器，
*   **-moz-** 火狐、
*   **-o-** 旧版歌剧，
*   **-ms-** 微软 Edge 和 ie 浏览器。

使用供应商前缀时，请记住**它们只是暂时的**。许多需要附加供应商前缀的属性现在已经完全支持，不需要它们了。

## 应该如何使用它们？

您可以很容易地使用供应商前缀，只需在属性前添加它们，就像这样:

```
.element {
  -webkit-transform: rotate(60deg);
  -ms-transform: rotate(60deg);
  -o-transform: rotate(60deg);
  transform: rotate(60deg);
} 
```

在这种情况下，您需要确保浏览器支持该属性。

将不带前缀的属性放在底部是一个很好的做法。这样，通过使用 CSS 的级联性质，您可以确保当完全支持属性时，这是它将使用的属性。

## 哪个属性需要加前缀？

这是一个常见的问题。一个好办法是停止猜测，去看看这些网站:

*   [http://shouldiprefix.com/](http://shouldiprefix.com/)
*   [https://www.w3.org/TR/css-2010/#properties](https://www.w3.org/TR/css-2010/#properties)

## 厌倦了写前缀？

如果你厌倦了每次需要时都要写前缀，有几个 **autoprefix** 服务可以帮你:

*   [https://github.com/postcss/autoprefixer](https://github.com/postcss/autoprefixer)-**PostCSS 插件**，
*   [https://www . npmj . com/package/autofix](https://www.npmjs.com/package/autoprefixer)-**NPM 前置字元**，
*   [https://github . com/sinresorhus/high-autofix](https://github.com/sindresorhus/sublime-autoprefixer)-**用于 high text**。

## 结论

希望这篇简短的供应商前缀介绍能帮助您更好地理解它们，减少对它们的恐惧。确保在您的新项目中正确使用它们，并记住:供应商前缀是我们的朋友！

如果你需要帮助，包括你的项目中的一些 CSS，不要犹豫[联系我们的团队](https://kolosek.com/startproject/)。我们很乐意帮你一把！:)