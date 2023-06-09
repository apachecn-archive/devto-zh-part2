# 用 JS、Python 和 Java 6 学习算法:数组分块

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-6-array-chunking-3mii>

这是我尝试用三种不同的语言学习斯蒂芬·格里德的 Udemy 课程的第六篇文章。JavaScript 解决方案是由斯蒂芬的[。我试着把它“翻译”成 Python 和 Java。](https://github.com/StephenGrider/AlgoCasts/blob/master/completed_exercises/chunk/index.js)

今天的问题是:

> -方向
> 给定一个数组和块大小，将数组分成许多子数组
> ，其中每个子数组的长度大小为
> -示例
> 块([ 1，2，3，4]，2) - > [[ 1，2]，[3，4]]
> 块([1，2，3，4，5]，2) - > [[ 1，2]，[3，4]，[5]]
> 块([1，2，3，4，5，6，7，8 4) - > [[ 1，2，3，4]，[5]]
> 组块([ 1，2，3，4，5]，10) - > [[ 1，2，3，4，5]]

我将每个解加上将 10，000，000 个元素的数组分成 1，000 个元素的子数组所花费的时间(毫秒)。

# 1:添加各元素

JavaScript:

```
function chunk1(array, size) { // 227.480ms
    const chunked = [];

    for (let element of array) {
        const lastChunk = chunked[chunked.length - 1];

        if (!lastChunk || lastChunk.length === size) {
            chunked.push([element]);
        } else {
            lastChunk.push(element);
        }
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def chunk1a(lst: list, size: int) -> list: # 2409.636ms
    chunked = []

    for element in lst:
        if not chunked or len(chunked[-1]) == size:
            chunked.append([])
        last_chunk = chunked[-1]
        last_chunk.append(element)

    return chunked 
```

Enter fullscreen mode Exit fullscreen mode

就像在[练习 4](https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-4-maxchar-1eo2) 中一样，我们不能像在 JS 中一样从`last_chunk = chunked[-1]`开始，因为这会导致索引错误。

因为它两次查找`chunked`的最后一个元素，所以将它重写为:
会快一点

```
def chunk1b(lst: list, size: int) -> list: # 2014.493ms
    chunked = []

    for element in lst:
        if not chunked:
            chunked.append([])
        last_chunk = chunked[-1]
        if len(last_chunk) == size:
            last_chunk = []
            chunked.append(last_chunk)
        last_chunk.append(element)

    return chunked 
```

Enter fullscreen mode Exit fullscreen mode

还想到用 collections.deque 代替 list:

```
from collections import deque

def chunk1c(lst: list, size: int) -> list: # 2618.956ms
    chunked = deque()

    for element in lst:
        if not chunked or len(chunked[-1]) == size:
            chunked.append([])
        last_chunk = chunked[-1]
        last_chunk.append(element)

    return list(chunked) 
```

Enter fullscreen mode Exit fullscreen mode

但是这导致了比第一种解决方案稍长的执行时间。

Java:

```
import java.util.ArrayList;
import java.util.List;

public static List<List<Integer>> chunk1a(List<Integer> list, int size) { // 2072.358ms
    List<List<Integer>> chunked = new ArrayList<>();

    for (int element : list) {
        if (chunked.isEmpty() || chunked.get(chunked.size() - 1).size() == size) {
            chunked.add(new ArrayList<>());
        }
        List<Integer> lastChunk = chunked.get(chunked.size() - 1);
        lastChunk.add(element);
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

像 Python 1b 这样的解决方案要比第一个快得多。

```
import java.util.ArrayList;
import java.util.List;

public static List<List<Integer>> chunk1b(List<Integer> list, int size) { // 404.818ms
    List<List<Integer>> chunked = new ArrayList<>();

    for (int element : list) {
        if (chunked.isEmpty()) {
            chunked.add(new ArrayList<>());
        }
        List<Integer> lastChunk = chunked.get(chunked.size() - 1);
        if (lastChunk.size() == size) {
            lastChunk = new ArrayList<>();
            chunked.add(lastChunk);
        }
        lastChunk.add(element);
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

当我使用 LinkedLists:
时，它可以改进得更多

```
import java.util.LinkedList;
import java.util.List;

public static List<List<Integer>> chunk1c(List<Integer> list, int size) { // 295.885ms
    LinkedList<List<Integer>> chunked = new LinkedList<>();

    for (int element : list) {
        if (chunked.isEmpty()) {
            chunked.add(new LinkedList<>());
        }
        List<Integer> lastChunk = chunked.getLast();
        if (lastChunk.size() == size) {
            lastChunk = new ArrayList<>();
            chunked.add(lastChunk);
        }
        lastChunk.add(element);
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，这是第一个 Java 代码的 LinkedList 版本，它比任何解决方案都慢。我想知道为什么，因为在我看来，它与上面的 1c 基本相同。

```
public static List<List<Integer>> chunk1d(List<Integer> list, int size) { // 4556.835ms
    LinkedList<List<Integer>> chunked = new LinkedList<>();

    for (int element : list) {
        if (chunked.isEmpty() || chunked.getLast().size() == size) {
            chunked.add(new LinkedList<>());
        }
        List<Integer> lastChunk = chunked.getLast();
        lastChunk.add(element);
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章比我预期的要长，但是现在是第二组解决方案，更简洁也更快。

# 2:使用切片法

JavaScript:

```
function chunk2(array, size) { // 83.652ms
    const chunked = [];
    let index = 0;

    while (index < array.length) {
        chunked.push(array.slice(index, index + size));
        index += size;
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def chunk2a(lst: list, size: int) -> list: # 240.898ms
    chunked = []
    index = 0

    while index < len(lst):
        chunked.append(lst[index:index+size])
        index += size

    return chunked 
```

Enter fullscreen mode Exit fullscreen mode

Pythonic 式的一行程序:

```
def chunk2b(lst: list, size: int) -> list: # 234.880ms
    return [lst[i:i+size] for i in range(0, len(lst), size)] 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
import java.util.ArrayList;
import java.util.List;
import java.lang.Math;

public static List<List<Integer>> chunk2(List<Integer> list, int size) { // 1.250ms
    List<List<Integer>> chunked = new LinkedList<>();
    int index = 0;

    while (index < list.size()) {
        chunked.add(list.subList(index, Math.min(index + size, list.size())));
        index += size;
    }

    return chunked;
} 
```

Enter fullscreen mode Exit fullscreen mode

这一个绝对是所有中最快的！