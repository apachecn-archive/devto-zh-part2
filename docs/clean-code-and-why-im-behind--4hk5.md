# 干净的代码和我落后的原因！

> 原文：<https://dev.to/hydroweaver/clean-code-and-why-im-behind--4hk5>

[![image](img/3bac6a7911cfd7c7ae8067e2f00e69ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TO4T6u4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2016/11/19/14/00/code-1839406_1280.jpg)

大家好，

这是我在这里的第一篇文章，所以我会尽量简短😃。我最近开始在 [Codewars](https://codewars.com) 上磨我的 javascript 斧子，之前几天我一直在研究 [Watch &代码](//watchandcode.com)作者 [Gordon Zhu](https://twitter.com/gordon_zhu?lang=en) ，这是我开始使用 JS 的惊人的第一步，因为我什么都不知道，仍然知道得不多，总之这是重点...

codewars 上有一个对专家来说更简单的形，但这个是在 **6 kyu** 上，它是这样的:

###### 给定一个链表 lst 和一个数 N，创建一个新的链表，最多包含每个数 lst N 次，不需要重新排序。例如，如果 N = 2，输入为[1，2，3，1，2，1，2，3]，则取[1，2，3，1，2]，去掉下一个[1，2]，因为这将导致 1 和 2 在结果中出现 3 次，然后取 3，这将导致[1，2，3，1，2，3]。

因此，本质上，您需要在不破坏索引的情况下删除一个数字的第 n 次出现。

简单对吗？我知道**现在**！上周我几乎花了 3 天(请不要评价我)打破了我的头，当我应该只是清楚地考虑它和**清楚地阅读过滤函数文档**。

现在对于我写的令人沮丧的糟糕代码，回想起来我感到惭愧！😓

```
function deleteNth(arr, n){
    var final = [];
    var last = [];
    var k = [];
    var f = [];
    var count_inside = 0;

    arr.forEach(function(item, index){
        if(last.indexOf(item)===-1){
            final[index] = arr.map(i=>{
                if(i===item && count_inside<n){
                    count_inside +=1;
                    return item;
                }
                else{
                    return '';
                }
            }
            );
            count_inside = 0;
            last.push(item);
        }
    });

    k = final.filter(function(i){return true;})

    for(var i =0;i<arr.length;i++){
        var sum = '';
        for(var j = 0;j<k.length;j++){
            sum += k[j][i];
        }
        f.push(parseInt(sum));
    }
    return f.filter(function(p){return p>=p});
} 
```

我基本上在尝试做以下事情:

1.  用一个数字筛选数组，并在具有该数字的数组中获得结果，所有其他索引为空或“”。
2.  不要重复第一步中的数字，重复其他数字。
3.  将生成的数组压缩成一个数组，然后开始工作！

同时仍然自豪地提交它...明知有人可能只用一行**就完成了**！是的，在提交之后...我看到这个:

```
function deleteNth(arr,x) {
  var result = [];
  return arr.filter(function(n) {
    result[n] = (result[n]||0) + 1;
    return result[n] <= x;
  });
} 
```

就这么多了！

##### 只计算一个数字出现的次数，如果出现次数超过 N 次，就不要把它放在返回的数组中。

我错过了那个！我知道我需要一段时间才能想清楚。但是我只是想把这个说出来，也许它能帮助你今天以不同的方式思考。

谢谢 Codewars！