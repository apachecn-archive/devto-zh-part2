# 压缩的基本原理

> 原文：<https://dev.to/mellen/the-basic-basics-of-compression-lap>

很长时间以来，我一直对学习信息论感兴趣。我甚至有关于这个主题的书，我试着去读，但是一旦它们走出类比，进入数学，我就迷失了。

尽管如此，我还是设法理解了它的一小部分。我想到的一件事是，因为存储某些类型的信息(例如 ASCII 字符数据)的标准方法有开销(例如每个 ASCII 字符占用 1 个字节)，如果您的文档不需要所有的开销(例如您只使用' a '到' z' ASCII 字符)，您可以重新映射信息以具有更小的占用空间(例如每个字符可以占用 1 个字节)。

为了证明我理解这个想法，我用 python 3 编写了一个小脚本来压缩文本文件:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [梅伦](https://github.com/Mellen) / [文件 _ 压缩](https://github.com/Mellen/file_compression)

### 用于压缩文件的 python 3 程序

<article class="markdown-body entry-content p-5" itemprop="text">

# 文件压缩

探索如何压缩文件。

tc.py 使用了我对损耗压缩应该如何工作的基本理解(见[https://dev.to/mellen/the-basic-basics-of-compression-lap](https://dev.to/mellen/the-basic-basics-of-compression-lap)来看看我是如何想出它的)。)

py 是 Huffman 编码的一个实现，适用于任何类型的文件。与我的基本实现相比，它在压缩方面要好得多。

(**需要 python 3** )

py 是一个文本文件压缩程序

```
 usage: tc.py [-h] [-d] file name

 positional arguments:  
    file name   The name of the file to be processed.

 optional arguments:  
    -h, --help  show this help message and exit  
    -d          Decompress the file (defaults to compressing). 
```

py 是一个适用于任何类型文件的压缩程序

```
 usage: bc.py [-h] [-d] file name

 positional arguments:  
    file name   The name of the file to be processed.

 optional arguments:  
    -h, --help  show this help message and exit  
    -d          Decompress the file (defaults to compressing). 
```

</article>

[View on GitHub](https://github.com/Mellen/file_compression)

当然不如压缩。事实上，这种压缩几乎看不出来，但它确实存在。让我们来看看它是如何工作的(`compressor.py`):

首先我们必须找出文档中的独特字符:

```
with open(self.filename, 'r') as textfile:
            text = textfile.read()

chars = ''.join(set(text)) 
```

内置的 [`set`](https://docs.python.org/3/tutorial/datastructures.html#sets) 数据类型使这变得非常容易。我们将集合变回字符串，因为我们需要保持字符的顺序。

由此我们可以算出我们需要多少位(位长)来表示每个字符:

```
bl = len(chars).bit_length() 
```

在这之后，我相当随意地决定将字符压缩成一个大约 64 位数的列表。事后看来，python 允许我将所有字符放入一个任意大小的整数中，所以我不需要列出一个列表，但这就是我所做的:

```
max_inserts = 64//bl;
current_insert = 0
value = ''
values = []
for c in text:
    n = chars.index(c) + 1
    b = (bin(n)[2:]).zfill(bl)
    value += b
    current_insert += 1
    if current_insert == max_inserts:
        values.append(int(value, 2))
        current_insert = 0
        value = ''

if current_insert < max_inserts and current_insert > 0:
    values.append(int(value, 2)) 
```

那是一大段密集的代码，让我们把它分解一下。所以首先我们设置一些初始值。告诉我们一个 64 位整数可以容纳多少个字符。`current_insert`是我们插入到整数中的字符数。`value`是整数，用一串 1 和 0 表示。`values`是整数列表。

接下来，我们一次一个字符地检查文件的文本。首先，我们得到字符在唯一字符列表中的索引。然后，我们将该数字转换为二进制表示(位长填充零)。

二进制表示都需要相同的长度，以便我们稍后可以从文件中提取数据。如果这些值的长度不同，就需要有一种方法来分隔它们，无论如何都会填充这些值，或者需要一些复杂的方法来将这些值放在一起，但我想不出一种方法。事实上，如果你查看回购的 git 日志，你会看到我尝试了不同的东西，直到我来到这里。

一旦索引是二进制的，我们就把它附加到当前的整数上。然后我们检查当前整数是否足够长。如果是，我们把它加到整数列表中，并为下一个整数重置。就在最后，我们加入了最后一个整数，以防遗漏。

就是这样。解压基本上是反过来的。

我知道压缩有比这更多的东西，我只是不理解它(目前？).我确实想改进它来处理二进制文件。我认为这不会太难。在此之后，我认为下一步应该是弄清楚如何根据一个模式出现的频率来压缩文件。这个想法是，最经常出现的字符或字节组应该被赋予最小的值，等等，但是我还没有想出如何以压缩的方式存储这些信息。