# Cookie、本地存储还是会话存储？

> 原文：<https://dev.to/mingt/cookie-localstorage-or-sessionstorage-4lp2>

Cookie、localStorage 和 sessionStorage 都是客户端存储的简单数据存储方式。Cookie 更像是一种处理存储在客户端的简单数据的老方法。在 HTML5 中，引入了 web 存储，如 localStorage 或 sessionStorage，它也更容易使用。

### 饼干

1.  持久性:用户可以为每个 cookies 设置过期时间。
2.  存储:整个 cookie 数据只有大约 4KB 的空间。
3.  流量:所有 HTTP 请求都需要将数据发送回服务器，这增加了客户机和服务器之间的流量。
4.  适用于旧浏览器。

### 本地存储

1.  持久性:数据在被删除之前一直存在
2.  存储:可用空间增加到 5 MB 左右
3.  流量:更少的流量，因为不是所有的 HTTP 请求都需要将数据发送回服务器
4.  域:数据只停留在同一个域中，这意味着如果数据存储在网站 A 上，下次只能在网站 A 上访问，为了明确这里使用的域，它指的是同一个网站字段。例如，你在脸书浏览的任何不同的帖子或不同的个人页面都在 facebook.com 之下。因此，这些页面都在同一个域下。

### [会话存储](#sessionstorage)

sessionStorage 类似于 localStorage。唯一的区别是数据的持久性。对于会话存储，一旦用户离开域，如关闭标签或窗口，会话存储将被清空。

### 如何使用 localStorage 和 sessionStorage？

localStorage 和 sessionStorage 都使用键值对，它们的语法类似。以下是一些例子。

### 语法

```
//To store the data:
localStorage.setItem(“key”,”value”);

//To retrieve the data:
localStorage.getItem(“key”);

//To update the data (the same as to store the data):
localStorage.setItem(“key”,”value”);

//To remove one entry:
localStorage.removeItem(“key”);

//To clear all the data:
localStorage.clear(); 
```

Enter fullscreen mode Exit fullscreen mode