# 浮点不精确性

> 原文：<https://dev.to/vladimirfomene/floating-point-imprecision-4f8k>

# 什么是浮点不精确？

python 中的这两个表达式应该产生相同的结果。但是他们为什么不这样做呢？

```
>>> 7 / 3 * 3
7.0

>>> 1 / 3 * 7 * 3
6.999999999999999 
```

Enter fullscreen mode Exit fullscreen mode

# 什么时候应该小心浮点不精确？