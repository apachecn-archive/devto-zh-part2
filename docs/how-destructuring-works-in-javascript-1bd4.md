# JavaScript 中的析构是如何工作的💢💢💢

> 原文：<https://dev.to/sait/how-destructuring-works-in-javascript-1bd4>

析构有助于编写更少的代码，并且是在 JavaScript 中访问属性的简单方法。

## 物体毁坏。💔

假设我们有一个对象文字。

```
let obj={
 name:'sai',
 show:true

} 
```

Enter fullscreen mode Exit fullscreen mode

##### 我们如何访问对象中的属性？

*   答案是我们使用点符号来访问对象属性

就像我们需要使用 **obj.propertyname 或 obj['propertyname']**

[![](img/83ee3c7658792afe739f94951a3e9cfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OCB0DJvM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxpnmqpesmsql5zc7ss5.png)

**让我们用对象析构来看看同样的事情**

[![](img/3bd2d374b3551f0eeba9ecb1a29f4b41.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yTLdD8n5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8ewvg0p1jqfcmg5vnea.png)

你在对象析构中看到过我们用一种简单的方式访问同一个属性，而不是写点符号吗？

## 数组解构

让我们看一个例子

#### 怎样才能访问数组中的元素？

*   答案是我们使用它的位置，比如 0，1，2，3 等等

[![normal](img/31e25ee5b941f3431d28ac7207cda707.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qr99X4q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2htuck587p13rw4q8m1n.png)

我们可以使用 arr['0'] 访问的第二个东西

[![string](img/04be3a9bdf713b1922830b74e9bec45a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lSRYKYvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0srjrjw1rnf7qdhbuy0q.png)

现在我们可以使用数组析构来访问相同的元素，而不是每次都使用它的位置。

[![array destruturing](img/4d9ca8fb0de94ae09c769f937fa63e37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nKxOeRoJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kdmkt7pya9w3i49rlis1.png)

### 如何在函数中使用析构？

让我们看看这个例子

我将对象作为参数传递给函数。

显示而不解构
[![func normal](img/669b42b696867f88a0fe9e4d4ecf955d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k9JadIh---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3oozjxxzignu57m5nbf2.png)

**同样的事情还有析构**

[![func des](img/bdc53405b7be30f3cf548dadfa3a70bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MyMrMfXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0ydfuu2syu3mqev3gm2.png)

在上面的代码中，我们在函数参数本身中析构了对象。你有没有看到我们为访问同样的东西而编写的代码减少了多少？

# 现在我们正在向水平纵深移动

### 嵌套析构💣

[![](img/7a4585286920493957ff299139a9a4f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l9R3KetE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/00c7nzawn9y4vlr7y6ht.png)

这比我们不需要编写 obj.show.items 来访问 items 属性要简单一些。

希望你们喜欢！！！！！如果你有任何疑问，请随时询问。
快乐编码........