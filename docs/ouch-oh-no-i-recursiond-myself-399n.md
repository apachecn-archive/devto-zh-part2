# 哎哟！哦不，我递归了我自己！

> 原文：<https://dev.to/hydroweaver/ouch-oh-no-i-recursiond-myself-399n>

[![image](../Images/297fd692a1e5a82455ae7b4c22a744da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3sZa1757--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://c1.staticflickr.com/3/2041/2361164281_cea03b0faa_z.jpg)

嘿德弗斯！

发生什么事了？这是另一篇关于我用我不知道的概念割伤自己的短文。

这是关于 codewars 中的 [**最佳旅行**](https://www.codewars.com/kata/55e7280b40e1c4a06d0000aa/train/javascript) 的，如果你用 Python 编码，这很简单，但是在 JS 中(如果你像我一样)你必须**把你的头撞在桌子上**和**把你的头撞在桌子上**和**把你的头撞在桌子上**和...**超出最大堆栈大小**！😭

问题:

1.  从输入数组中获得不同的固定长度(比如 f)组合，而无需替换
2.  计算从步骤 1 得出的每个组合的总和。
3.  将每个总和与给定的输入参数(比如 K)进行比较
4.  返回最接近 K 的和，否则为空，依此类推。

如果您使用的是 python，获得组合就像下面这样简单:

```
import itertools

arr = [1,2,3,4,5,6,7,8]

combinations = itertools.combinations(arr, 5) # f is 5 
#print each combination for i in combinations:
    print(i) 
```

查看来自 [**geeksforgeeks**](https://www.geeksforgeeks.org/itertools-combinations-module-python-print-possible-combinations/) 的帖子对了，我现在才看到这个！lol wut！

因此...当我尝试这个问题时，我并不知道这一点，我用 JS 试了一下，看这里，正如你所知，基于我的[上一篇](https://dev.to/hydroweaver/clean-code-and-why-im-behind--4hk5)帖子，它是**重复的，怪诞的！**😬

```
ts = [ 91, 74, 73, 85, 73, 81, 87 ];

function chooseBestSum(t, k, ls){
    var km = t;
    var towns = k;
    var arr = ls;
    var tuple = [];
    var km_sum = [];
    var count = 0;
    var result = 0;
    function karan(arr){
    if(arr.length==0){
        return null;
    }
    else{
        if(tuple.length===towns-1){
            for(var i =0;i<arr.length;i++){
                tuple[towns-1]=arr[i];
                console.log('we are here', tuple);
                km_sum.push(tuple.reduce((i,c)=>i+c));
            }
            //tuple.splice(1,2)
            tuple.splice(1,tuple.length);
        }
        tuple.push(arr[0]);
        karan(arr.slice(count+1, arr.length));
    }
    //return Math.max(...km_sum.filter(i=>i<=km?i:null))
    }

    for(var i = 0;i<=arr.length-towns;i++){
        tuple = [];
        karan(arr.slice(i,arr.length));
    };
    console.log(km_sum);
    result = Math.max(...km_sum.filter(i=>i<=km)); 
    if(result===-1 || result===-Infinity){
        return null;
    }
    else{
        return result;
    }
    //result = Math.max(...km_sum.filter(i=>(km_sum.length>0&&i<=km)?i:null));
}

chooseBestSum(331, 4, ts); 
```

我想做的是:

1.  想多了只有**递归**才能解决！不对！虽然是最好最短的解决方案。
2.  无法真正想象这个问题。
3.  从来没有读过或发现这是什么，直到我在网上看到这些东西
4.  完全不知道函数返回在哪里工作等等。

所以我自己解决不了！你在上面看到的代码工作了 22/30 次，因为它没有返回所有正确的组合**因为**它是命中和错过**因为**它主要是为 f = 3 设计的！😳😳😳😳

我把这个留给你们:RTFM，再一次训练和斯科特.伊斯特伍德的一段话:

**低着头，努力工作，永远不要相信自己的炒作，因为...你只要继续工作。**

日安！