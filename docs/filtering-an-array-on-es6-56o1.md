# 在 ES6 上筛选阵列

> 原文：<https://dev.to/papaponmx/filtering-an-array-on-es6-56o1>

ES6 发布已经有一段时间了，现在 2017 年。它的大部分功能在现代浏览器上都得到了广泛支持。

因为大多数人已经熟悉(或不熟悉)过滤数组。下面是如何用箭头函数过滤数组的复习。

免责声明:我假设你对 ES6 相当熟悉。如果你需要，这里有一个关于箭头函数的很棒的帖子。这是我几个月前的一个问题，我决定发布一个关于答案的帖子。

给定下面的对象数组(又名集合)

```
 let shoes = [{"brand":"Converse", "color": "blue" },
              {"brand":"Nike",     "color": "white" },
              {"brand":"Adidas",   "color": "red" },
              {"brand":"Puma",    "color": "black" }]; 
```

Enter fullscreen mode Exit fullscreen mode

这个数组包含你拥有的每双鞋，它们的品牌和颜色。这是一个雨天，你想要一双既能完成工作又不会太脏的鞋子。

这就是为什么在你收集的鞋子中，你只想买黑色的。

为了实现这一点，我们使用了 filter()。这是一个创建新数组的方法，所有元素都通过了由提供的函数实现的测试。

这在代码中看起来如何？很高兴你这么问。下面是答案。

```
shoes.filter(shoe => shoe.color === "black"); 
```

Enter fullscreen mode Exit fullscreen mode

filter()方法中有一个函数，其中:

`shoe`是数组中正在处理的当前元素。

`shoe.color === “black”`是我们要评估的条件。

最后，这里是一个块中的所有代码，供您复制并粘贴到您的控制台中。

```
let shoes = [{"brand":"Converse", "color": "blue" },
              {"brand":"Nike",     "color": "white" },
              {"brand":"Adidas",   "color": "red" },
              {"brand":"Puma",    "color": "black" }];
shoes.filter(shoe => shoe.color === "black"); 
```

Enter fullscreen mode Exit fullscreen mode

这就是所有的乡亲，感谢阅读。