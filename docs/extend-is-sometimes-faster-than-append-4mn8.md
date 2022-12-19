# 扩展有时比追加更快

> 原文：<https://dev.to/lyfolos/extend-is-sometimes-faster-than-append-4mn8>

我只是在比较`[].append(x)`和`[] + [x]`之间的*性能*差异。我刚刚意识到`[].append(x)`更快。但是我用`[].extend([x])`试了一下就更快了。您可以在优化代码时使用它。不要在你编码的时候使用它，但是当你要优化它的时候使用`[].extend([x])`。简单基准测试

```
>>> import timeit
>>> start = timeit.timeit()
>>> [1, 2, 3, 4] + [5]
[1, 2, 3, 4, 5]
>>> end = timeit.timeit()
>>> end - start
0.006420466001145542
>>> start = timeit.timeit()
>>> [1, 2, 3, 4].append(5)
>>> end = timeit.timeit()
>>> end - start
0.004373588995804312
>>> start = timeit.timeit()
>>> [1, 2, 3, 4].extend([5])
>>> end = timeit.timeit()
>>> end - start
0.0022365170007105917 
```

第一次基准测试的结果

```
Fastest: [1, 2, 3, 4].extend([5])
0.0022365170007105917 seconds

Medium: [1, 2, 3, 4].append(5)
0.004373588995804312 seconds

Slowest: [1, 2, 3, 4] + [5]
0.006420466001145542 seconds 
```

来自 CLI 的第二个基准

```
lambdef ~ λ python -mtimeit -s'[1, 2, 3, 4] + [5]'
100000000 loops, best of 3: 0.00939 usec per loop
lambdef ~ λ python -mtimeit -s'[1, 2, 3, 4].append(5)'
100000000 loops, best of 3: 0.00938 usec per loop
lambdef ~ λ python -mtimeit -s'[1, 2, 3, 4].extend([5])'
100000000 loops, best of 3: 0.00993 usec per loop 
```

第二次基准测试的结果

```
Fastest: [1, 2, 3, 4].append(5)
100000000 loops, best of 3: 0.00938 usec per loop

Medium: [1, 2, 3, 4] + [5]
100000000 loops, best of 3: 0.00939 usec per loop

Slowest: [1, 2, 3, 4].extend([5])
100000000 loops, best of 3: 0.00993 usec per loop 
```

所以当你连续添加物品时，应该首选`.append()`，否则就用`.extend()`。