# 。forEach()，。map()，。过滤器()....有什么区别？

> 原文：<https://dev.to/ogwurujohnson/foreach-map-filter--whats-the-difference-304l>

**。forEach:**

`.forEach()`，用于对数组中的每个元素执行相同的代码，但不改变数组，返回 undefined。

*示例:*
在下面的示例中，我们将使用`.forEach()`来遍历一个食物数组，并记录我们想要吃的每一种食物。

```
let food = ['mango','rice','pepper','pear'];
food.forEach(function(foodItem){
    console.log('I want to eat '+foodItem);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在您的控制台上运行此程序；

[![forEach result](img/a38960fbd2ec20cbe62a13b4bc7437c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2tcc4-sD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r20nz5ulnpktqo1ke2zz.PNG)

**。map():**
`.map()`对数组中的每个元素执行相同的代码，并返回一个包含更新元素的新数组。

*示例:*
在下面的示例中，我们将使用`.map`来迭代`cost`数组的元素，并将每个元素除以 10，然后将包含新成本的新数组赋给变量`newCost`。

```
let cost = [100,400,300,700];
let newCost = cost.map(function(costItem){
    return costItem / 10;
});
console.log(newCost); 
```

Enter fullscreen mode Exit fullscreen mode

在您的控制台上运行此程序；

[![map result](img/e7b1bc927ccdd5c1a7bf8834d37d80bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZ3KhGip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tehhysnkfra7a009lh06.PNG)

**。filter():**
`.filter()`检查数组中的每个元素，看它是否满足某个标准，并返回一个新数组，其中的元素返回符合标准的`truthy`。

*示例:*
在下面的示例中，我们将使用`.filter`返回小于 200 的值。

```
let cost = [100,400,50,40,700];
let smallCost = cost.filter(function(costItem){
    return costItem < 200
});
console.log(smallCost); 
```

Enter fullscreen mode Exit fullscreen mode

在您的控制台上运行此程序；

[![filter result](img/059cc7e2d374a6099350ec71d9eee739.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4qNjH4Lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r7346my32b8zmi38gdg.PNG)

**这就是所有的乡亲...**