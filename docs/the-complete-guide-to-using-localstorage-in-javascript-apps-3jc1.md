# 在 Javascript 应用程序中使用本地存储的完整指南

> 原文：<https://dev.to/bnevilleoneill/the-complete-guide-to-using-localstorage-in-javascript-apps-3jc1>

[![](../Images/b3438510a7ca3fe3b183644ed3633aeb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8cLeWRpS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Akrty-B-4mEibZa7er8Uz_A.jpeg)

LocalStorage 是一种 web 存储类型，它允许 Javascript 网站和应用程序在浏览器中存储和访问数据，没有到期日。这意味着存储在浏览器中的数据即使在浏览器窗口关闭后仍将存在。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 使用本地存储

要在 web 应用程序中使用 LocalStorage，有 5 种方法可供选择:

**setItem():** 将键和值添加到本地存储

**getItem():** 通过 LocalStorage 中的键获取一个值

**removeItem():** 从 LocalStorage 中通过键删除一个项目

**清除():**清除所有本地存储

**key():** 传递了一个数字来检索本地存储的第 n 个键

#### setItem()

顾名思义，这个方法允许您在 LocalStorage 对象中存储值。

它有两个参数，一个键和一个值。稍后可以引用该键来获取附加到它的值。

```
window.localStorage.setItem('name', 'Obaseki Nosa'); 
```

其中 **`name`** 是键， **`Obaseki Nosa`** 是值。另请注意，LocalStorage 只能存储字符串。

要存储数组或对象，你必须将它们转换成字符串。

为此，我们在传递给 setItem()之前使用 JSON.stringify()方法。

```
const person = {
    name: "Obaseki Nosa",
    location: "Lagos",
}

window.localStorage.setItem('user', JSON.stringify(person)); 
```

#### getItem()

getItem()方法允许您访问存储在浏览器的 localStorage 对象中的数据。

它只接受一个作为键的参数，并以字符串形式返回值。

检索上面存储的用户密钥:

```
window.localStorage.getItem('user'); 
```

这将返回一个值为的字符串；

```
“{“name”:”Obaseki Nosa”,”location”:”Lagos”}” 
```

要使用该值，您需要将其转换回对象。

为此，我们利用 JSON.parse()方法将 JSON 字符串转换成 Javascript 对象。

```
JSON.parse(window.localStorage.getItem('user')); 
```

#### [T1】remove item()](#removeitem)

当向 removeItem()方法传递一个键名时，如果该键名存在，它将从存储中删除该键名。如果没有与给定键相关联的项，此方法将不执行任何操作。

```
window.localStorage.removeItem('name'); 
```

#### 清除()

该方法在被调用时清除该域的所有记录的整个存储。它不接收任何参数。

```
window.localStorage.clear(); 
```

#### 键()

key()方法在需要循环遍历键的情况下非常有用，它允许您向本地存储传递一个数字或索引来检索键的名称。

```
var KeyName = window.localStorage.key(index); 
```

#### 浏览器支持

LocalStorage 作为一种 web 存储是 HTML5 规范。包括 IE8 在内的主流浏览器都支持。为了确保浏览器支持本地存储，您可以使用下面的代码片段进行检查:

```
if (typeof(Storage) !== "undefined") {
    // Code for localStorage
    } else {
    // No web storage Support.
} 
```

#### LocalStorage JavaScript 限制

使用 LocalStorage 很容易，但误用它也很容易。以下是不使用 localStorage 的限制和方法:

*   不要在本地存储中存储敏感的用户信息
*   它不能替代基于服务器的数据库，因为信息只存储在浏览器中
*   所有主流浏览器的本地存储都被限制在 5MB 以内
*   LocalStorage 非常不安全，因为它没有任何形式的数据保护，并且可以被网页上的任何代码访问。
*   本地存储是同步的。这意味着每个被调用的操作只会一个接一个地执行。

有了这些，我们就拥有了 web 应用程序中本地存储的能力。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[关于在 JavaScript 应用中使用本地存储的完整指南](https://blog.logrocket.com/the-complete-guide-to-using-localstorage-in-javascript-apps-ba44edb53a36/)首先出现在[博客](https://blog.logrocket.com)上。