# 如何从 Sails.js 迁移到 Express.js(或者如何最终到达陆地)

> 原文：<https://dev.to/ecostack/how-to-migrate-from-sails-js-to-express-js-or-how-to-finally-reach-land-2e3b>

[![](img/ec10feec82211f01602a1eb79817a1c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--clT1t-AU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARVia951vicq7LpQbISBlLw.jpeg)

在我工作的公司，我们有一个用 *Sails.js* 编写的相当大的项目。现在，经过这么长时间的开发，项目越来越大，现在需要进行结构性的改变。此外，使用编译器进行语法检查也变得很有必要。

所以，我们引入了 *TypeScript* 。这一步很简单，只需一个新的 *Grunt* 任务和对文件夹结构的一些修改就可以快速完成。

在 *Sails.js* 中只有一件事让 *TypeScript* 不那么强大了 _。_ 它使所有控制器、服务和模型对**全局**变量可用。这限制了*类型脚本*的可能性，因为 *Sails.js* 总是期望

```
module.exports = {
 ...
} 
```

待定。

有了这么大的代码库，就有必要依赖类型检查和来自 *TypeScript* 的代码完成等功能。

对于这些特性，我们需要实现类。现在，拥有类和标准导出模块并不是一个理想的组合。

一个类是这样的: