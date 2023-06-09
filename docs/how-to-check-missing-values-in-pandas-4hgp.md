# 如何检查熊猫中缺失的值

> 原文：<https://dev.to/hygorxaraujo/how-to-check-missing-values-in-pandas-4hgp>

在这篇文章中，我将描述如何在熊猫数据帧中找到 NaN 值。这种操作非常有用，因为经常会发现数据集缺少数据值或数据值不正确。

我将使用 numpy 包来生成一些具有 NaN 值的数据。

#### 导入必要的包

```
import pandas as pd
import numpy as np
import platform 
```

Enter fullscreen mode Exit fullscreen mode

```
print(f'Python version: {platform.python_version()} ({platform.python_implementation()})')
print(f'Pandas version: {pd.__version__}')
print(f'Numpy version: {np.__version__}') 
```

Enter fullscreen mode Exit fullscreen mode

```
Python version: 3.6.4 (CPython)
Pandas version: 0.23.1
Numpy version: 1.14.5 
```

Enter fullscreen mode Exit fullscreen mode

### 生成具有 NaN 值的数据

```
num_nan = 25 # number of NaN values wanted in the generated data np.random.seed(6765431)  # set a seed for reproducibility A = np.random.randn(10, 10)
print(A) 
```

Enter fullscreen mode Exit fullscreen mode

```
[[-1.56132314 -0.16954058 -0.17845422 -1.33689111 -0.19185078 -1.18617765
   0.44499302 -0.61209568  0.31170935  1.4127548 ]
 [ 0.85330488  0.68517546 -1.10140989  0.84918019  0.72802961 -0.35161197
   0.73519152  1.13145412  0.53231247  0.78103143]
 [-0.81614324  0.15906898  0.49940119 -0.09319255 -1.07837721 -0.76053341
   0.73622083 -0.45518154 -0.69194032  1.02550409]
 [-1.96339975  0.07593331 -0.16798377 -1.20398958  0.88333656  1.17908422
   0.26324698 -2.65442248 -0.31583796 -0.16065732]
 [-1.24321376 -0.89816898  0.02824671  0.15304093  0.56505667 -0.78115883
   0.74504467  1.14025258 -0.04518221 -0.83908358]
 [ 1.00967019  0.84240102  1.15043436 -0.40120489  0.00664105 -1.23247563
   0.64738343  1.66096762 -0.92556683  0.47575796]
 [ 0.96516278  1.11158059 -0.82155143  0.88900313  2.16943761 -2.05250161
   2.40156233  0.92453867 -0.24437783 -2.91029265]
 [-0.86492662  0.82443151 -0.48246862 -1.05183143 -1.15272524 -0.77170733
   0.07177233  1.02820181 -2.08947076  0.89859677]
 [-0.07263982 -0.56840867  1.30910275 -0.52846822  0.06019191 -0.61000727
   0.40782356 -0.36124333 -1.54522486 -0.07891861]
 [-1.96361682 -1.06315325 -0.45582138 -0.74566868  1.27579529 -2.46306005
   0.57022673 -0.02793746  0.78652775  1.27690195]] 
```

Enter fullscreen mode Exit fullscreen mode

```
# Set random values to nan A.ravel()[np.random.choice(A.size, num_nan, replace=False)] = np.nan
print(A) 
```

Enter fullscreen mode Exit fullscreen mode

```
[[-1.56132314 -0.16954058 -0.17845422 -1.33689111 -0.19185078 -1.18617765
          nan -0.61209568  0.31170935  1.4127548 ]
 [ 0.85330488  0.68517546         nan  0.84918019         nan -0.35161197
   0.73519152         nan  0.53231247  0.78103143]
 [-0.81614324  0.15906898  0.49940119         nan -1.07837721 -0.76053341
   0.73622083         nan -0.69194032  1.02550409]
 [-1.96339975  0.07593331         nan -1.20398958  0.88333656         nan
   0.26324698         nan -0.31583796 -0.16065732]
 [-1.24321376 -0.89816898  0.02824671  0.15304093  0.56505667 -0.78115883
   0.74504467  1.14025258 -0.04518221 -0.83908358]
 [ 1.00967019  0.84240102         nan -0.40120489  0.00664105         nan
   0.64738343  1.66096762 -0.92556683  0.47575796]
 [ 0.96516278         nan -0.82155143  0.88900313  2.16943761         nan
   2.40156233         nan -0.24437783         nan]
 [-0.86492662  0.82443151 -0.48246862 -1.05183143 -1.15272524 -0.77170733
   0.07177233  1.02820181 -2.08947076         nan]
 [-0.07263982         nan  1.30910275 -0.52846822  0.06019191 -0.61000727
   0.40782356 -0.36124333         nan         nan]
 [        nan         nan         nan         nan  1.27579529 -2.46306005
          nan         nan  0.78652775  1.27690195]] 
```

Enter fullscreen mode Exit fullscreen mode

```
# Create a DataFrame from the generated data df = pd.DataFrame(A)
df 
```

Enter fullscreen mode Exit fullscreen mode

|  | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | -1.561323 | -0.169541 | -0.178454 | -1.336891 | -0.191851 | -1.186178 | 圆盘烤饼 | -0.612096 | 0.311709 | 1.412755 |
| one | 0.853305 | 0.685175 | 圆盘烤饼 | 0.849180 | 圆盘烤饼 | -0.351612 | 0.735192 | 圆盘烤饼 | 0.532312 | 0.781031 |
| Two | -0.816143 | 0.159069 | 0.499401 | 圆盘烤饼 | -1.078377 | -0.760533 | 0.736221 | 圆盘烤饼 | -0.691940 | 1.025504 |
| three | -1.963400 | 0.075933 | 圆盘烤饼 | -1.203990 | 0.883337 | 圆盘烤饼 | 0.263247 | 圆盘烤饼 | -0.315838 | -0.160657 |
| four | -1.243214 | -0.898169 | 0.028247 | 0.153041 | 0.565057 | -0.781159 | 0.745045 | 1.140253 | -0.045182 | -0.839084 |
| five | 1.009670 | 0.842401 | 圆盘烤饼 | -0.401205 | 0.006641 | 圆盘烤饼 | 0.647383 | 1.660968 | -0.925567 | 0.475758 |
| six | 0.965163 | 圆盘烤饼 | -0.821551 | 0.889003 | 2.169438 | 圆盘烤饼 | 2.401562 | 圆盘烤饼 | -0.244378 | 圆盘烤饼 |
| seven | -0.864927 | 0.824432 | -0.482469 | -1.051831 | -1.152725 | -0.771707 | 0.071772 | 1.028202 | -2.089471 | 圆盘烤饼 |
| eight | -0.072640 | 圆盘烤饼 | 1.309103 | -0.528468 | 0.060192 | -0.610007 | 0.407824 | -0.361243 | 圆盘烤饼 | 圆盘烤饼 |
| nine | 圆盘烤饼 | 圆盘烤饼 | 圆盘烤饼 | 圆盘烤饼 | 1.275795 | -2.463060 | 圆盘烤饼 | 圆盘烤饼 | 0.786528 | 1.276902 |

### 检查 NaN 值

现在我们有了一些要操作的数据，让我们看看可以检查缺失值的不同方法。

DataFrame 对象有两种方法可以使用:`DataFrame#isna()`和`DataFrame#isnull()`。但是如果你检查源代码，似乎`isnull()`只是`isna()`方法的别名。为了简单起见，我只使用`isna()`方法，因为我们使用`isnull()`会得到相同的结果。

```
df.isna() 
```

Enter fullscreen mode Exit fullscreen mode

|  | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 | 错误的 |
| one | 错误的 | 错误的 | 真实的 | 错误的 | 真实的 | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 |
| Two | 错误的 | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 |
| three | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 | 真实的 | 错误的 | 真实的 | 错误的 | 错误的 |
| four | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 |
| five | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 | 真实的 | 错误的 | 错误的 | 错误的 | 错误的 |
| six | 错误的 | 真实的 | 错误的 | 错误的 | 错误的 | 真实的 | 错误的 | 真实的 | 错误的 | 真实的 |
| seven | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 真实的 |
| eight | 错误的 | 真实的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 错误的 | 真实的 | 真实的 |
| nine | 真实的 | 真实的 | 真实的 | 真实的 | 错误的 | 错误的 | 真实的 | 真实的 | 错误的 | 错误的 |

从上面可以看出，当我们使用`isna()`方法时，它返回一个带有布尔值的数据帧，其中`True`表示 NaN 值，否则表示`False`。

如果我们想知道每行或每列有多少个缺失值，我们可以使用`DataFrame#sum()`方法:

```
df.isna().sum(axis='rows')  # 'rows' or 0 
```

Enter fullscreen mode Exit fullscreen mode

```
0    1
1    3
2    4
3    2
4    1
5    3
6    2
7    5
8    1
9    3
dtype: int64 
```

Enter fullscreen mode Exit fullscreen mode

```
df.isna().sum(axis='columns')  # 'columns' or 1 
```

Enter fullscreen mode Exit fullscreen mode

```
0    1
1    3
2    2
3    3
4    0
5    2
6    4
7    1
8    3
9    6
dtype: int64 
```

Enter fullscreen mode Exit fullscreen mode

为了简单地知道缺失值的总数，我们可以再次调用`sum()`:

```
df.isna().sum().sum() 
```

Enter fullscreen mode Exit fullscreen mode

```
25 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只想知道是否有丢失的值，而不关心数量，我们可以简单地使用`any()`方法:

```
df.isna().any()  # can also receive axis='rows' or 'columns' 
```

Enter fullscreen mode Exit fullscreen mode

```
0    True
1    True
2    True
3    True
4    True
5    True
6    True
7    True
8    True
9    True
dtype: bool 
```

Enter fullscreen mode Exit fullscreen mode

再次调用它，我们得到一个布尔输出:

```
df.isna().any().any() 
```

Enter fullscreen mode Exit fullscreen mode

```
True 
```

Enter fullscreen mode Exit fullscreen mode

除了`isna()`方法，我们还有`notna()`方法，这是它的布尔逆方法。应用它，我们可以得到没有丢失的值的数量，或者简单地，如果所有的值都没有丢失(但是使用`all()`方法代替`any()`)。

```
print(df.notna().sum().sum())  # not missing print(df.notna().all().all()) 
```

Enter fullscreen mode Exit fullscreen mode

```
75
False 
```

Enter fullscreen mode Exit fullscreen mode

注意 1:在示例中，使用了 DataFrame 方法来检查丢失的值，但是 pandas 包有自己的函数，目的相同，可以应用于其他对象。示例:

```
print(pd.isna([1, 2, np.nan]))
print(pd.notna([1, 2, np.nan])) 
```

Enter fullscreen mode Exit fullscreen mode

```
[False False  True]
[ True  True False] 
```

Enter fullscreen mode Exit fullscreen mode

注 2:这里应用于 DataFrame 对象的方法也适用于 Series 和 Index 对象。

### 时间对比

比较两种方法所用的时间，我们可以看到使用`any()`更快，但是`sum()`会给我们关于有多少丢失值的额外信息。

```
%timeit df.isna().any().any() 
```

Enter fullscreen mode Exit fullscreen mode

```
333 µs ± 33.1 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each) 
```

Enter fullscreen mode Exit fullscreen mode

```
%timeit df.isna().sum().sum() 
```

Enter fullscreen mode Exit fullscreen mode

```
561 µs ± 97.7 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each) 
```

Enter fullscreen mode Exit fullscreen mode

### 处理缺失值

处理缺失值的两种简单方法是删除它们或者用一些值填充它们。这些可以通过`dropna()`和`fillna()`方法实现。

`dropna()`方法将返回一个没有包含缺失值的行和列的数据帧。

```
df.dropna() 
```

Enter fullscreen mode Exit fullscreen mode

|  | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| four | -1.243214 | -0.898169 | 0.028247 | 0.153041 | 0.565057 | -0.781159 | 0.745045 | 1.140253 | -0.045182 | -0.839084 |

`fillna()`方法将返回一个数据帧，用指定的值填充缺失的值。

```
df.fillna(value=5) 
```

Enter fullscreen mode Exit fullscreen mode

|  | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | -1.561323 | -0.169541 | -0.178454 | -1.336891 | -0.191851 | -1.186178 | 5.000000 | -0.612096 | 0.311709 | 1.412755 |
| one | 0.853305 | 0.685175 | 5.000000 | 0.849180 | 5.000000 | -0.351612 | 0.735192 | 5.000000 | 0.532312 | 0.781031 |
| Two | -0.816143 | 0.159069 | 0.499401 | 5.000000 | -1.078377 | -0.760533 | 0.736221 | 5.000000 | -0.691940 | 1.025504 |
| three | -1.963400 | 0.075933 | 5.000000 | -1.203990 | 0.883337 | 5.000000 | 0.263247 | 5.000000 | -0.315838 | -0.160657 |
| four | -1.243214 | -0.898169 | 0.028247 | 0.153041 | 0.565057 | -0.781159 | 0.745045 | 1.140253 | -0.045182 | -0.839084 |
| five | 1.009670 | 0.842401 | 5.000000 | -0.401205 | 0.006641 | 5.000000 | 0.647383 | 1.660968 | -0.925567 | 0.475758 |
| six | 0.965163 | 5.000000 | -0.821551 | 0.889003 | 2.169438 | 5.000000 | 2.401562 | 5.000000 | -0.244378 | 5.000000 |
| seven | -0.864927 | 0.824432 | -0.482469 | -1.051831 | -1.152725 | -0.771707 | 0.071772 | 1.028202 | -2.089471 | 5.000000 |
| eight | -0.072640 | 5.000000 | 1.309103 | -0.528468 | 0.060192 | -0.610007 | 0.407824 | -0.361243 | 5.000000 | 5.000000 |
| nine | 5.000000 | 5.000000 | 5.000000 | 5.000000 | 1.275795 | -2.463060 | 5.000000 | 5.000000 | 0.786528 | 1.276902 |

### 参考文献:

*   [用随机放置的 nan 创建样本 numpy 数组(StackOverflow)](https://stackoverflow.com/questions/32182409/create-sample-numpy-array-with-randomly-placed-nans)
*   [如何检查熊猫数据帧中的值是否为 NaN(stack overflow)](https://stackoverflow.com/questions/29530232/how-to-check-if-any-value-is-nan-in-a-pandas-dataframe)
*   [pandas.isnull](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.isnull.html)
*   [pandas.isna](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.isna.html)
*   [pandas.notna](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.notna.html)
*   [熊猫。DataFrame.dropna](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.dropna.html)
*   熊猫。DataFrame.fillna