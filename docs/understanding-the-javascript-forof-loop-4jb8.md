# 了解的 JavaScript...循环的

> 原文：<https://dev.to/skptricks/understanding-the-javascript-forof-loop-4jb8>

帖子链接:[了解...循环的起点](https://www.skptricks.com/2018/11/understanding-javascript-forof-loop.html)

本教程解释了如何使用 For...javascript 编程语言中的循环。森林...of 语句创建迭代可迭代对象的循环，包括:内置字符串、数组、类似数组的对象(例如，参数或节点列表)、TypedArray、Map、Set 和用户定义的可迭代对象。它调用一个定制的迭代钩子，为对象的每个不同的属性值执行语句。

语法:
for(iterable 的变量){
语句

变量——对于每次迭代，属性的值被赋给变量。iterable——一个具有可枚举属性并且可以被迭代的对象。

[了解 JavaScript For...循环的起点](https://www.skptricks.com/2018/11/understanding-javascript-forof-loop.html)

[![](img/cb66ba8473e216817c9a6f44a8a2cac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qdEPG5iZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-grqYmn-3hqQ/W_OXepT1KQI/AAAAAAAACKM/7NAjmM-Ts5UvuXgGZRKwGkgH-o3IhFrSQCLcBGAs/s400/forof.png)