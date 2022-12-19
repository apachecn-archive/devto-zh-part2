# 用乐高积木解释的 JavaScript Array.reduce()

> 原文：<https://dev.to/_arpy/javascript-arrayreduce-explained-with-lego-bricks-4pca>

#### 我们都必须认同 JavaScript 很棒！但是你知道吗？乐高更伟大！为什么？因为你可以用这个神奇的玩具来解释和模拟如此多的想法、行为和算法🚀。

<figure>[![](../Images/794eef1b4234dd54712d1c6cbae55b14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WILcjCJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AHtqyqbVrAooagfsg) 

<figcaption>【混彩塑料玩具拍品】作者[里克·梅森](https://unsplash.com/@egnaro?utm_source=medium&utm_medium=referral)于[上](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

## 定义

Mozilla 开发者网络在数组对象原型中这样定义 reduce 方法:

> **reduce()** 方法对数组的每个成员执行一个 **reducer** 函数(您提供的),产生一个输出值。
> 
> 您的 **reducer** 函数的返回值被分配给累加器，累加器的值在整个数组的每次迭代中被记住，并最终成为最终的单个结果值。

好的，但是我什么时候以及如何使用这个功能呢？仅仅是为了对一个数组中的所有数字求和吗？有现实世界的例子吗？而这个累加器(acc)、当前值(cur)、当前索引(idx)和源数组(src)分别代表什么？

🧒🏻让我们潜入孩子的房间好好解释一下。

## 五彩缤纷的积木

说实话，我是一个不可救药的乐高迷🤪。啊，那些颜色，形状，以及用它们建造你能想象的任何东西的可能性…

现在，由于我没有那么多时间花在他们身上，我更喜欢把所有的布景搭建起来，放在一个架子上，按照模型展示出来。但是，时不时地(尤其是当孩子们拿到我珍贵的收藏品时)，我所有的东西都会混在一起，一起扔进一个大容器里。哦，真是一团糟…然后是时候让我收拾好所有的用品、力量和动力，把它们放回书架上。

但是等等，**减速器**呢？好的，所以在我可以恢复我显示的集合之前，我需要构建它们，为此，我需要知道哪一个作品属于哪一个集合。然后我将能够用它们的指令来构建集合(就好像我不记得指令一样)😁).

而我的大脑使用一个完美的**数组缩减器**的时候到了！

## 让我们减少砖块

好的，为了简化示例，让我们假设不同的集合之间没有共享块。所以我知道，如果我看到一个黑色的 2x2 块，我知道它属于我的星球大战 B 翼战斗机，我所有的红色窗户属于一个古老的 T2 家族房子。让我们假设我只有 3 套乐高玩具:一个 B 翼，一个哈利波特禁止走廊房间和一个简单的白色墙壁和红色屋顶的老房子。

这是我放所有砖块的容器。

```
const bricks = [
    {set: 'b-wing', type: 'brick', size: '2x2', color: 'black'},
    {set: 'house', type: 'roof', size: '4x2', color: 'red'},
    {set: 'hp', type: 'spider', size: '1x1', color: 'black'},
    {set: 'b-wing', type: 'panel', size: '4x8', color: 'gray'},
    {set: 'b-wing', type: 'brick', size: '2x2', color: 'black'},
    {set: 'house', type: 'brick', size: '6x1', color: 'white'}
] 
```

Enter fullscreen mode Exit fullscreen mode

我想把它们和乐高玩具放在一起，就像这样:

```
{"b-wing":  [],  "house":  [],  "hp":  []} 
```

Enter fullscreen mode Exit fullscreen mode

📥📥📥我还没有准备贴标签的盒子，所以我会在路上做，只要我偶然发现一个还没有盒子的作品。

所以，我要做的是，遍历所有的砖块，并将它们放入相应的盒子中。以下是该过程中的一些陈述:

1.  *最初，我没有为每套*准备箱子，但我知道我要把箱子放在 ***一个专门的架子上*** 。**
***   我从 ***初始*** *容器中取出一块新砖，在脑海中*处理它，决定它属于哪里，并把 ***当前*** 砖放进它的设置盒中。*   如果我的 ***当前棋子组没有盒子，*** 我创建并标记一个新的盒子，放在 ***架子上。****   每次我从 ***初始*** *容器中取出一块砖，*我就 ***减少*** 剩下的所有砖的数量来排列。*   最后，当 ***初始*** *容器*中没有剩余的砖块需要处理时，我看了看我的架子，看到我的混合堆乐高积木得到了 ***转化*** 成了我架子上标签盒子的结构化排列。**

 **所以，换句话说(或者视觉上)，我会把左边的堆转化成右边的有组织的结构:

[![](../Images/3ffdc01fabbe3a090254e2afa1347199.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j8iux6G2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJ8eiMZyfTUfdk8qwSpjP9g.jpeg)

<figure>[![](../Images/9442ebae150abcedee86810d2279f76e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D1pX_UyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A0Y0bp0ZOAubWwKtXSSN9og.jpeg) 

<figcaption>变换结果</figcaption>

</figure>

👩🏻‍💻如果有人使用 Array.reduce()方法用 JavaScript 编写了整个过程，它看起来应该是这样的:

```
const bricks = [....]; //the pile of bricks defined above

const shelf = bricks.reduce((shelfOfBoxes, brick, brickNumber, pile) => {

    console.log(`Checking the brick number ${brickNumber + 1}`);

  if(!shelfOfBoxes.hasOwnProperty(brick.set)) {
        shelfOfBoxes[brick.set] = []; //add a new box if needed
    }
    shelfOfBoxes[brick.set].push(brick); //put the brick in its box

    console.log(`Bricks left: ${pile.length - (brickNumber + 1)}`)
}, {} ); //the empty shelf

console.log(shelf); 
```

Enter fullscreen mode Exit fullscreen mode

我们这里有什么？

1.  最初，我有一个混合的砖块堆，存储在一个数组中
2.  最初我的**货架**是空的。reduce 函数中的第二个参数是 shelf 的初始值，它是一个空对象:`{}`
3.  我用 **reduce** 方法遍历数组
4.  在循环的每次迭代中，我知道以下内容:货架上的当前情况(`shelfOfBoxes`)、我正在处理的当前砖块(`brick`)、砖块的当前索引/我已经处理了多少(`brickNumber`)以及最初的砖块堆(`pile`)
5.  在每一次循环中，我都会检查架子上是否有一个带有我当前砖块的名称的盒子:`if(!shelfOfBoxes._hasOwnProperty_(brick.set))`
6.  如果没有盒子，我会添加一个新的**空的**盒子，标签为当前砖块的设置:`shelfOfBoxes[brick.set] = [];`
7.  然后，我将当前砖块放入盒子中，作为它的集合:`shelfOfBoxes[brick.set]._push_(brick);`
8.  我对自己记下这堆砖头中还剩下多少:console.log( `Bricks left: ${pile.length — (brickNumber + 1)}`)
9.  最后，我有一个由盒子组成的**架子**,里面装满了属于每个盒子的所有砖块

所以我用了一个 ***reducer*** 函数**把**一个对象数组转化成*完全不同的*。这就是`Array.reduce()`方法的目的。

### 搞定

酷，现在你应该知道什么是 reduce 方法，它的参数是什么，当我们的大脑像 reducer 🧠.一样工作时，有什么真实的例子

当然,`Array.reduce()`方法还有其他的可能性和用例。您还应该知道，转换的初始值是可选的。我已经知道了另一个与砖块相关的例子。请继续关注另一篇文章，详细的解释即将发布📢。

当然，还有:

[![](../Images/bcde111ada062e2a1595b60dda6537a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wMvzrAtJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2ACR5yvINgplYJStM-YrWWkw.jpeg)

* * ***