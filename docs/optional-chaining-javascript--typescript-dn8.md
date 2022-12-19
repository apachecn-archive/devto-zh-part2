# 可选链接 JavaScript / TypeScript

> 原文：<https://dev.to/nabinadhikari/optional-chaining-javascript--typescript-dn8>

[![alt text](../Images/573a5c5459e69b5f66136b17804feef8.png "Nabin Adhikari, SafeChainJs")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FKH0QOGz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1527427337751-fdca2f128ce5%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D7f0965bc37ef09044780baa8af91e2b3%26auto%3Dformat%26fit%3Dcrop%26w%3D1350%26q%3D80)

在使用 JavaScript 或 Typescript 时，我最可怕的经历之一是读取对象的属性，因为没有可选的链接(如果我错了，请纠正我)。显然，我们可以有多个 if/else 语句用于安全的属性提取，我认为应该有一个更好的干净的方法。

然后，我开始搜索这样的库，它们工作得很好，除了它们缺少一个最重要的特性(对我来说)来提供后备价值。这就是为什么我开始考虑创建 npm 模块来解决这个问题，作为我的第一个 npm 模块，这样我也可以帮助其他开发人员。

今天，我创建了 [safechainjs](https://www.npmjs.com/package/safechainjs) ，这正是我想要的，我非常兴奋地发布了我的第一个 npm 模块。这里的如果有任何反馈或建议，我将不胜荣幸。

入门非常容易。例如

```
$ npm install --save safechainjs 
```

Enter fullscreen mode Exit fullscreen mode

对于这样一个物体:

```
const obj = {
    name: {
        firstName: "Albert",
        lastName: "Einstein"
    },
    dob: "1897/03/14",
    wife: ['Maric', 'Elsa']
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以像这样使用 safechainjs 轻松安全地提取属性。

```
const safechain = require('safechainjs');
const fname = safechain(obj, 'name', 'firstName', 'Unknown'); // Albert
const address = safechain(obj, 'address', 'postcode', 0); // 0
const wives = safechain(obj, 'wife', []); // ['Maric', 'Elsa'] 
```

Enter fullscreen mode Exit fullscreen mode

函数成功运行至少需要三个参数。

*   第一个参数是要从中获取属性的对象
*   多个参数在多个级别获取属性
*   最后一个参数是在缺少请求属性的情况下返回的后备值

如果您使用它并提供任何建议或反馈，我将不胜感激。
快乐 JS
[纳宾阿迪卡里](http://www.nabinadhikari.com)