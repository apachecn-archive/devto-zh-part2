# 用 Python 实现简单的 Bloom Filter 数据结构

> 原文：<https://dev.to/anarchyrucks/implementing-a-simple-bloom-filter-in-python-11bh>

这篇文章最初发表在我的个人博客上。

Bloom Filter 是一种节省空间的概率数据结构，由 Burton Howard Bloom 于 1970 年创建。它用于测试一个元素是否是没有[假阴性](https://en.wikipedia.org/wiki/False_positives_and_false_negatives)的集合的成员。

在 Bloom Filter 中，我们使用 k-散列算法散列一个元素，从这些散列中生成索引，然后在 filter-list 中将这些索引处的值设置为 1。为了检查元素是否在集合中，我们对该元素运行相同的 k-散列算法，并检查散列产生的索引处的所有值是否都是 1。如果所有的值都是 1，那么元素**可能是集合中的**，如果任何一个值是 0，那么元素**肯定是**不在集合中。

[![Bloom Filter](img/11bde68427e706e359dd165cf394c878.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2WhDy4RT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/a/ac/Bloom_filter.svg)

在上图中，`w`是**肯定**不在集合中，因为过滤器列表中的一个值是 0。

# 布鲁姆滤镜用在哪里？

布隆过滤器最常用于缓存层，以防止缓存“一击即中的奇迹”。它避免了只缓存请求一次的数据。只有在对同一数据进行多次请求后，数据才会被缓存。类似地，它也用于避免推荐用户以前读过的文章。Bloom Filter 还有很多其他应用，可以在这里找到[。](https://en.wikipedia.org/wiki/Bloom_filter#Examples)

# 实现简单的布隆过滤器

我们将通过使用 Python 作为编程语言编写一个简单的实现来学习 Bloom Filter。在这个程序中，我们想要使用 Bloom Filter 来检查一个查询是绝对新的还是旧的。

### 过滤列表

布隆过滤器可以通过使用特定长度的列表来实现。最初，列表的所有成员都被初始化为零。

```
bloom_len = 10
bloom = [0] * bloom_len 
```

### 获取用户输入

首先，我们在 while 循环中获得用户的输入，这样我们就可以获得多个输入。我们还需要用“utf-8”编码输入文本，这将在下面描述。

```
while True:
    query = str(input("Enter a query: ")).encode('utf-8') 
```

### 散列法

这里我们使用三种散列算法- [md5](https://en.wikipedia.org/wiki/MD5) 、 [sha1](https://en.wikipedia.org/wiki/SHA-1) 和 [sha224](https://en.wikipedia.org/wiki/SHA-2) ，因为它们在`hashlib`包中很容易获得。这三种散列算法为相同的用户输入产生三个输出。哈希函数的输出存储在一个列表中，以供进一步处理。

```
 # query = str(input("Enter a query: ")).encode('utf-8')

    m1 = hashlib.md5()
    m2 = hashlib.sha1()
    m3 = hashlib.sha224()

    m1.update(query)
    m2.update(query)
    m3.update(query)

    hash_values = [m1.digest(), m2.digest(), m3.digest()] 
```

### 查找过滤器索引

在计算出数据类型为`bytes`的哈希值后，我们将它们转换成整数并存储在一个列表中。然后，整数被布隆过滤器的长度模除，以找到需要在布隆过滤器内设置的索引。

```
# bloom = [0] * bloom_len

def bytes_to_int(hash_value):
    return int.from_bytes(hash_value, byteorder='big')

def bloom_index(hashint):
    return hashint % bloom_len

# while True: 
```

```
 # hash_values = [m1.digest(), m2.digest(), m3.digest()]

    hashints = list(map(bytes_to_int, hash_values))
    indices = list(map(bloom_index, hashints)) 
```

### 过滤查询

现在，我们检查查询是否是新的。正如我们已经提到的，Bloom Filter 发现如果查询是**肯定是新的**或**可能是旧的**。要做到这一点，我们需要检查过滤列表中的值，这些值是通过散列查询得到的。如果过滤器中这些索引处的任何值为零，我们可以确定该查询是一个新查询。如果所有的值都是 1，那么查询可能是旧的。

如果我们确定查询是一个新的查询，那么我们将索引值设置为 1，用于过滤未来的查询。

```
 # return hashint % bloom_len

    def new_query(indices):
    new = False
    for i in indices:
        if bloom[i] == 0:
            new = True
            break

    return new

    def set_bloom(indices):
    for i in indices:
        bloom[i] = 1 
```

```
 # indices = list(map(bloom_index, hashints))

    if new_query(indices):
        print("Definitely new query")
        set_bloom(indices)
    else:
        print("Probably old query") 
```

### 重置过滤器

随着越来越多的查询被处理，过滤器列表中的值被更新，这意味着假阳性率稳步增加，直到列表中的每个元素都变成 1。为了防止这种情况发生，在我们的简单实现中，如果列表中 1 的数量大于列表中总元素的 80%,我们将重置列表。

```
# while True:
    if fill_percentage() > 80.0:
        print("Resetting Bloom Filter-List")
        bloom = [0] * bloom_len

    # word = str(input("enter a word: ")).encode('utf-8') 
```

### 结论

您可以通过输入来尝试程序检查每个查询后生成的输出。你也可以改变`bloom_len`的值来玩这个程序。这个小实现的设计并不意味着用于真实的系统，但它有助于了解 Bloom Filter 通常是如何工作的。

以下是该程序的完整代码:

```
import hashlib

bloom_len = 10
bloom = [0] * bloom_len

def bytes_to_int(hash_value):
    return int.from_bytes(hash_value, byteorder='big')

def bloom_index(hashint):
    return hashint % bloom_len

def fill_percentage():
    return bloom.count(1) / bloom_len * 100;

def new_query(indices):
    new = False
    for i in indices:
        if bloom[i] == 0:
            new = True
            break

    return new

def set_bloom(indices):
    for i in indices:
        bloom[i] = 1

while True:
    if fill_percentage() > 80.0:
        print("Resetting Bloom Filter-List")
        bloom = [0] * bloom_len

    word = str(input("enter a word: ")).encode('utf-8')
    m1 = hashlib.md5()
    m2 = hashlib.sha1()
    m3 = hashlib.sha224()

    m1.update(word)
    m2.update(word)
    m3.update(word)

    hash_values = [m1.digest(), m2.digest(), m3.digest()]
    hashints = list(map(bytes_to_int, hash_values))
    indices = list(map(bloom_index, hashints))

    if new_query(indices):
        print("Definitely new query")
        set_bloom(indices)
    else:
        print("Probably old query") 
```

### 外部资源

你可以通过杰森·戴维斯的交互式布鲁姆滤镜演示来帮助你理解这些概念。维基百科也有一个关于[布鲁姆过滤器](https://en.wikipedia.org/wiki/Bloom_filter)的综合页面。

### 参考

1.  [维基百科](https://en.wikipedia.org/wiki/Bloom_filter)。