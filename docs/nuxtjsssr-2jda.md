# 是在 Nuxt.js 中判定是 SSR 还是浏览器上的处理的方法之一

> 原文：<https://dev.to/dala00/nuxtjsssr-2jda>

在 Nuxt.js 中开发时，编写的处理有时在服务器端渲染( SSR )时和在浏览器上处理时都执行。 想判定那个时的方法。

## 正规的方法

作为正规的方法，官方也写着的做法。

[窗口或文档未定义？](https://nuxtjs.org/faq/window-document-undefined/)

可以这样判定。

```
if (process.browser) {
  require('external_library')
} 
```

Enter fullscreen mode Exit fullscreen mode

## TypeScriptの場合

但是，在 TypeScript 的情况下，process 的类型中没有 browser 属性，因此会出现错误。 因此，虽然也可能有很好地设定各种各样类型的方法，但首先也可以用古式优雅的方法进行判定。

```
 if (typeof window !== "undefined") { 
```

Enter fullscreen mode Exit fullscreen mode