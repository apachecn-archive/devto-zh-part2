# 重新赋值还是过滤？

> 原文：<https://dev.to/hydroweaver/re-assign-or-filter-4fh4>

大家好！

这里有一个简单的帖子，看看我所做的和对 **CodeWars Array.diff 6 Kyu** 问题的最佳解决方案之间的比较。

#### 问题

给定两个数组 **a** 和 **b** ，从 **a** 中移除所有 **b** 项并返回，如果不匹配则返回 **a** 。

#### 我做了什么？

1.  使用 forEach 循环遍历 **b** 中的每一项。
2.  如果您在 **a** 中找到该项目，请将其移除，并使用过滤功能*重新分配*T4 a。
3.  转到点 1 并从 **b** 获取下一个项目。
4.  当 **b** 中没有剩余物品时，返回新的 **a** 。

```
function array_diff_ME(a, b) {
  b.forEach(function(item, index){
      a = a.filter(x=>x===item?false:true);
  });
  return a;
}

array_diff([1,2,3,4], [4,1,3]); 
```

#### 那么有什么问题呢？

它使用了一个额外的 forEach 循环，这是不必要的，问题可以通过使用一个单独的过滤函数来解决。

#### 更好的解决方案

事实证明， **a** 上的一个单独的过滤函数就可以做到这一点。它的工作原理如下:

1.  浏览 **a** 中的每个项目，并使用 indexOf 函数检查该项目是否存在于 **b** 中。
2.  如果 indexOf(item)为-1，则过滤函数返回 true，该值保存在直通过滤器中，但是，如果 indexOf(item)不为-1，则过滤函数返回 false，并从 **a** 的中过滤掉项目**。**
3.  返回新的**一个**。

这里是“伪”——伪代码！

```
function array_diff_PEOPLE_WHO_STUFF(a, b) {
    a.filter({if (b.indexOf(current item of a) is -1) then
           return false;
              else
           return true;
    });
return a;
} 
```

希望你喜欢这个，就像我喜欢记录另一个完成事情的次优方法一样:)，TTYL！