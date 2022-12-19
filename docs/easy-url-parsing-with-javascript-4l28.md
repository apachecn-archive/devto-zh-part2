# 使用 DOM 进行简单的 URL 解析

> 原文：<https://dev.to/danwolfdev/easy-url-parsing-with-javascript-4l28>

## 问题及解决方法

我最近不得不在 React 中编写一个动态路由器。必须对照 API 检查给定的 URL，看它是否有任何内容要显示。为此，我必须验证从资源返回的路径名最终与地址栏中的路径名匹配。有一个一致的方法来解析 URL 是必要的。

我了解到 [HTMLAnchorElements](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAnchorElement) 实际上有很多与全局`location`相同的属性，可以用来解析不同的 URL 字符串以确定路径名。

因此，为了解析我的 URL 字符串，我只需创建一个 HTMLAnchorElement，给它分配一个 href，然后查看 pathname 属性。

```
let a = document.createElement('a');
a.href = "/hello";
console.log(a.pathname); // === '/hello'

a.href = "https://dev.to/hello";
console.log(a.pathname); // === '/hello' 
```

## 明白了

在应用程序中尝试之前，我想提一些需要注意的事情。

首先，由于显而易见的原因，当 URL 上没有方案时，它会失败。

```
let a = document.createElement('a');
a.href = "dev.to/hello";
console.log(a.pathname); // === '/dev.to/hello' 
```

其次，IE11 不会添加前面的斜杠，这会导致不一致。

```
let a = document.createElement('a');
a.href = "https://dev.to/hello";
console.log(a.pathname); // === 'hello' (IE11) // === '/hello' (Chrome) 
```

我所做的就是检查第一个字符是否是斜杠，如果不是，就把它加在前面。

```
let pathname = a.pathname;
if (pathname.substr(0, 1) !== '/')
    pathname = '/' + pathname; 
```

希望你觉得这有用！