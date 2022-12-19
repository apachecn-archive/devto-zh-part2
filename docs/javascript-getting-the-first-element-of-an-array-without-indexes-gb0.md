# JavaScript:获取不带索引的数组的第一个元素

> 原文：<https://dev.to/mxl/javascript-getting-the-first-element-of-an-array-without-indexes-gb0>

```
const a = [1, 2, 3];
const [b] = a;
b
// 1 
```

[]是分解运算符。

另外:

```
const [c, ...rest] = a;
c
// 1
rest
// [2, 3] 
```

...是 rest 运算符。