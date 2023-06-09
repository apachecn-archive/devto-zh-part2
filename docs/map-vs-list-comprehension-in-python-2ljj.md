# Python 中的地图与列表理解

> 原文：<https://dev.to/lyfolos/map-vs-list-comprehension-in-python-2ljj>

于是，我在努力学习 Elm(我刚放弃)，然后我看到了这个功能要求:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 请求:添加列表理解 #147](https://github.com/elm/compiler/issues/147) 

[![deadfoxygrandpa avatar](img/8a687f35ffd82ef71340ef1536aa53e7.png)](https://github.com/deadfoxygrandpa) **[deadfoxygrandpa](https://github.com/deadfoxygrandpa)** posted on [<time datetime="2013-05-04T18:49:44Z">May 04, 2013</time>](https://github.com/elm/compiler/issues/147)

我建议在 Elm 中加入列表理解。它们在实现它们的语言中被大量使用。考虑到 Elm 和 Haskell 语法之间的相似之处，我建议使用 Haskell 风格的列表理解，如下所示:

```
[(x, y) | x <- [1..10], y <- [1..20], x /= y] 
```

[View on GitHub](https://github.com/elm/compiler/issues/147)
I was trying to find an alternative to `list comprehension` in Elm, they was recommending to use `map`. List comprehension is just a [Syntactic Sugar](https://www.wikiwand.com/en/Syntactic_sugar). Then let's compare both of them in Python.

# 表现

`map`性能:

```
lambdef lab λ python -m timeit "map(lambda a: a*a, range(100))"
100000 loops, best of 3: 11.5 usec per loop 
```

`List comprehension`性能:

```
lambdef lab λ python -m timeit "[a*a for a in range(100)]"     
100000 loops, best of 3: 5.51 usec per loop 
```

`List comprehension`快了 2 倍！

# 句法比较

其实，`List comprehension`看起来比地图更简洁美观。
比如:

```
[n for n in nlist]|
                  |
                  |
map(lambda n: n, nlist) 
```

大了 5 个字节，慢了！不好！

# 字节码生成

有 [dis](https://docs.python.org/3.7/library/dis.html) 来显示 Python 代码的 Python 字节码。我们来看看`list comprehension`和`map`的字节码，比较一下性能。

```
Bytecode of [n for n in range(100)] (List comprehension)

  1           0 LOAD_CONST               0 (<code object <listcomp> at 0x7f042c8eac00, file "<dis>", line 1>)
              2 LOAD_CONST               1 ('<listcomp>')
              4 MAKE_FUNCTION            0
              6 LOAD_NAME                0 (range)
              8 LOAD_CONST               2 (100)
             10 CALL_FUNCTION            1
             12 GET_ITER
             14 CALL_FUNCTION            1
             16 RETURN_VALUE 
```

它加载列表理解，创建函数，加载范围函数，加载 100 作为参数，调用范围函数，并返回值。

```
Bytecode of map(lambda n: n, range(100)) (Map)

  1           0 LOAD_NAME                0 (map)
              2 LOAD_CONST               0 (<code object <lambda> at 0x7f042c8ead20, file "<dis>", line 1>)
              4 LOAD_CONST               1 ('<lambda>')
              6 MAKE_FUNCTION            0
              8 LOAD_NAME                1 (range)
             10 LOAD_CONST               2 (100)
             12 CALL_FUNCTION            1
             14 CALL_FUNCTION            2
             16 RETURN_VALUE 
```

它加载 map 函数，加载 lambda ( `lambda n: n`)，使 lambda 函数成为对象，加载 range 函数并加载 100 作为参数，调用两个函数并返回值。

在我看来，Python 字节码是人类可以理解的。

# 优点&缺点

### 列表理解

*   赞成的意见
    *   支持直接生成列表
    *   支持直接生成发电机
    *   过滤(ifs)
    *   简明的
    *   快的
*   骗局
    *   没有缺点！(海事组织)

### 地图

*   赞成的意见
    *   没有优点！(海事组织)
*   骗局
    *   只生成**的**贴图对象(你需要在以后把它变成你想要的对象)
    *   没有如果(过滤)
    *   慢的
    *   冗长的

# 代码示例

*   将每个数字加倍到 100

```
# List comprehension [n*2 for n in range(100)]

# Map map(lambda n: n*2, range(100)) 
```

*   将`fn`应用到 100 的每个数字

```
# List comprehension [fn(n) for n in range(100)]

# Map map(fn, range(100)) 
```

*   过滤事件

```
# List comprehension [n for n in range(100) if n % 2 == 0]

# Map not_possible()
# But you can use filter:
# filter(lambda n: n % 2 == 0) 
```

# 结论

优化代码时，使用`list comprehension`而不是`map`。

感谢阅读！