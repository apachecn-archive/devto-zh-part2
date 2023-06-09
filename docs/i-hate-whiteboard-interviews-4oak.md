# 我讨厌白板面试

> 原文：<https://dev.to/finallynero/i-hate-whiteboard-interviews-4oak>

我知道这篇文章的标题暗示了一个负面的含义，但也许在读完这篇文章中我的故事后，你应该学到一些东西，但如果你什么也没选也没关系。

上周我面试过，我赶紧补充一下；(我目前正在寻找一个前端开发人员的角色，远程或在尼日利亚拉各斯)我参加了 Interswitch 的面试，我被要求带着书面材料，因为这将是一个白板测试，更像是白纸测试，因为我们是在白纸上做的，而不是在白板上，
让我也坦然承认我不是白板测试的粉丝，所以在面试前两周，我决定每天至少练习一种算法，直到面试那天。

[![alt text](img/20bd9cc7d3126ddfcaf68d70c1f3da95.png)T2】](https://i.giphy.com/media/IPbS5R4fSUl5S/giphy.gif)

我掌握了很少的算法和求解算法的技巧。后来有一天，我走进 interswitch 的办公室，自信满满地知道我可以解决任何抛给我的算法。

[![alt text](img/cec6a2f7fdd6dea685d8d3a880aac91b.png)T2】](https://i.giphy.com/media/ymQYKyz7STEwo/giphy.gif)

时候到了

问题:给定两个数组，数组 A 和数组 B 把它们放入一个新数组但是有一个扭曲，数组 A 的零索引应该是新数组的零索引，数组 B 的最后一个索引应该是新数组的一个索引，那么数组 A 的一个索引应该是新数组的两个索引， 数组 B 的倒数第二个索引应该是新数组的三个索引，以此类推，直到新数组包含数组 A 和数组 B。你只有 15 分钟的时间来提供一个解决方案，不要使用任何特殊的数组方法。

该图对测试
[![alt text](img/dcc52b47f4134c9f29c3fc7b006e17a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--67X_pCzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i66.tinypic.com/iwq7q1.jpg) 进行了直观的解释

当我第一次听到这个问题时，我非常自信我能解决它，但是你知道，自信并不意味着你会解决它。长话短说，我没有通过白板测试，我今天仍然没有工作。那天剩下的时间，以及接下来的三天，我都在思考我永远不会成为软件开发员。好吧，我能够鼓起勇气再次审视这个问题并尝试解决它，在我的朋友 [@debugmonstar](https://twitter.com/debugMonstar) 的帮助下，我想到了一个解决方案。

```
function interweave(array1, array2){
    let newArray =[];
    let longestArray = (array1.length > array2.length) ? array1 : array2;
    let reverseArray = array2.reverse()

    for(let i =0; i < longestArray.length ; i++){
        if(array1[i]|| array1[i] === 0){
         newArray[newArray.length] = array1[i]
        }
        if(array2[i] || array2[i] === 0){
         newArray[newArray.length] = array2[i]
        }

    }

    return newArray
}
let arrayA =[5,7,9,2,6,1,4,0,3]
let arrayB =[12,3,8,1,6]

let output = interweave(arrayA, arrayB)
console.log(output) //returns [5,6,7,1,9,8,2,3,6,12,1,4,0,3] 
```

Enter fullscreen mode Exit fullscreen mode

*   首先，我们声明一个名为 new array 的新数组。

*   然后我们找到 array1 和 array2 之间较长的数组，这样我们就可以知道迭代的最大次数，从而得到新的数组。

*   然后反转 array2，因为这样更容易将它插入到新数组中。

*   然后我们开始迭代，首先我们检查数组 1 中是否存在索引(I ),或者数组 1 的索引 I 是否等于零，如果是，我们将它添加到新数组中，然后我们对数组 2 进行同样的检查，然后将它添加到新数组中，直到迭代结束。

*   然后归还我们的新阵列。

当我在面试中被问到这个问题时，我一直在努力解决的一个问题是，我不能快速集中我的思想，不同的想法在我的脑海中飞过，在我落笔之前，15 分钟就结束了，所以我对未来的自己和其他像我一样必须处理白板测试的人的建议是:

*   首先，保持冷静，因为这个问题一开始可能看起来很棘手，但它可能是你可以轻松实现的，甚至可能用不到 10 行代码就可以实现。

*   不要直接跳到代码中开始写，首先要批判性地思考最终的结果是什么，以及你如何才能实现它。

*   最后，白板测试失败并不意味着你是一个失败者或者你是一个垃圾程序员。

> ![I Am Devloper profile image](img/6d70b9abbf0bf1c0637433b3c86d4250.png)我是开发者@ iamdevloper![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)新编程工作的第一天，拿着一把崭新的笔站在白板前
> 
> >从面试来看，我只是推测这将是我的办公桌上午 11:03-2018 年 9 月 26 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1044905355933876225)[>](https://twitter.com/intent/retweet?tweet_id=1044905355933876225)

如果你读到这里，我很高兴这个标题没有让你分心，希望你能学到一些东西。还有，。如果你有一个不同的或者更好的解决方案，请在评论区写下来。

* * *

**特别感谢我的朋友[茵卡·金尤美-约瑟夫](https://twitter.com/YJTheRuler)帮我编辑这篇文章**