# 课堂笔记:熊猫数据框架入门

> 原文：<https://dev.to/mxl/class-notes-getting-started-with-pandas-dataframes-2m2k>

在这里，我将写下处理熊猫数据帧的基础知识。

DataFrame 是主要的 Pandas 数据结构，它允许我们轻松地使用数据表。

数据帧可以从字典:
中构造

```
import pandas as pd
frame = pd.DataFrame({'numbers': range(3), 'chars': ['a'] * 3}) 
```

Enter fullscreen mode Exit fullscreen mode

它为我们提供了以下输出:

|  | 烧焦 | 数字 |
| --- | --- | --- |
| Zero | a | Zero |
| one | a | one |
| Two | a | Two |

同样，DataFrame 可以从一个. csv 文件 :
初始化

```
frame = pd.read_csv('file.csv', headers=0, sep='\t') 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是文件名，第二个参数是包含标题的行的索引(int 或 int 的列表)，第三个参数是将要使用的数据分隔符，这里是一个制表符(' s '表示单个空格，' \s+'表示多个空格)。

**列标题**可以使用以下代码提取:

```
frame.columnsm
# Index([u'chars', u'numbers], dtype='object') 
```

Enter fullscreen mode Exit fullscreen mode

另一个有用的命令返回**帧尺寸** :

```
frame.shape
# (3, 2) 
```

Enter fullscreen mode Exit fullscreen mode

我们来加一行:

```
new_line = {'chars': 'b', 'numbers': 8}
frame.append(new_line, ignore_index=True, inplace=True)
frame 
```

Enter fullscreen mode Exit fullscreen mode

它为我们提供了以下输出:

|  | 烧焦 | 数字 |
| --- | --- | --- |
| Zero | a | Zero |
| one | a | one |
| Two | a | Two |
| three | b | eight |

*inplace* 关键字表明结果应该写入原始变量，而不仅仅是将结果帧返回到输出。

添加一列更容易:

```
frame['bools'] = [False] * 3 + [True]
frame 
```

Enter fullscreen mode Exit fullscreen mode

|  | 烧焦 | 数字 | bools |
| --- | --- | --- | --- |
| Zero | a | Zero | 错误的 |
| one | a | one | 错误的 |
| Two | a | Two | 错误的 |
| three | b | eight | 真实的 |

行和列可以被[丢弃](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.drop.html) :

```
# the first argument is a list if indexes, 
# the second is the axis (0 is for rows, 1 is for columns) frame.drop([0,1], axis=0, inplace=True) 
```

Enter fullscreen mode Exit fullscreen mode

|  | 烧焦 | 数字 | bools |
| --- | --- | --- | --- |
| Zero | a | Two | 错误的 |
| one | b | eight | 真实的 |

结果可以[保存成一个. csv 文件](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.to_csv.html) :

```
frame.to_csv('updated.csv', set=',', header=True, index=None) 
```

Enter fullscreen mode Exit fullscreen mode