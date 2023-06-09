# 将 csv 编写器从 Python 2 转换为 Python 3

> 原文：<https://dev.to/thejessleigh/converting-a-csv-writer-from-python-2-to-python-3-1cnb>

# 请使用 Python 3

将项目从 Python 2.7 转换到 Python 3.x 通常是一个相当轻松的过程。通常，清单项目是这样的:

*   将打印调试语句从`print`关键字更改为`print()`函数
*   [检查 Python 3 兼容性的依赖关系](https://pypi.org/project/caniusepython3/)
*   考虑使用 [mypy](http://mypy-lang.org/) 渐进静态类型。进一步考虑。决定这对于现在来说是太多的工作，但是对于`v1`来说是一个伟大的目标——无论何时。
*   检查以确保对于任何关键的业务逻辑，您没有依赖整数而不是浮点除法

但是处理字符串的读写会突然变得棘手。即便如此，随着 Python 2 的 2020 年停产日期的临近，使用 Python 3 无疑是负责任的选择。

## 举例

我最近决定将我用 Python 2 编写的一个项目移植到 Python 3。

这个项目非常简单:

*   以 csv 格式导出一个 [Goodreads](https://goodreads.com) 用户的库
*   使用 [Libib](https://libib.com) 的预期输入字段将内容写出到新的 csv 中

```
import csv
import re

def convert_csv():
    .
    .
    .
    with open('goodreads_export.csv', 'r') as f:
        reader = csv.DictReader(f)
    rows = [x for x in reader]

    books = [book for book in rows]
    print(books[1])
    # Simplifying the actual fields here so the example won't get too long :)
    header_keys = ['Author', 'ISBN-13', 'Title']
    print(len(header_keys))

    with open('libib_export.csv', 'wb') as f:
        writer = csv.writer(f)
        writer.writerow(header_keys)
        for book in books:
            row = []
            authors = [book.get('Author', '')]
            authors.append(book.get('Additional Authors', ''))
            row.append(','.join(authors))
            row.append(book.get('ISBN13', ''))
            row.append(book.get('Title', ''))

            writer.writerow(row) 
```

我喜欢这个项目，因为它是一个非常简单的过程，需要一个文件输入并创建一个文件输出，所以它是测试不同部署过程或系统配置的一个很好的例子。所以我改变了我的打印调试语句，但是我一直得到一个错误:

```
File "converter.py", line 15, in convert_csv
    writer.writerow(libib_keys)
TypeError: a bytes-like object is required, not 'str' 
```

我觉得这很有道理。Python 3 默认使用 UTF-8 编码的字符串，除非您指定使用字节字符串。所以我注释掉了我的大部分过程，将我的头键转换成字节字符串，然后再试一次。

```
header_keys = [b'Author', b'ISBN-13', b'Title']

with open('libib_export.csv', 'wb') as f:
        writer = csv.writer(f)
        writer.writerow(header_keys) 
```

但由于某种原因，我仍然得到同样的错误。在徒劳地运行了几次之后，希望得到不同的结果，我决定谷歌一下这个错误。当然，这告诉了我我已经知道的 Python2 和 Python3 中字符串的区别。所以我决定回顾一下 python csv writer 文档，看看我可能搞砸了哪些假设。

在 [Python 2 文档](https://docs.python.org/2/library/csv.html#csv.writer)中，构建 csv 编写器的例子如下:

```
import csv
with open('eggs.csv', 'wb') as csvfile:
    etc
    etc 
```

但是 [Python 3 文档](https://docs.python.org/3/library/csv.html#csv.writer)是这样做的

```
import csv
with open('eggs.csv', 'w', newline='') as csvfile: 
```

Python 2 文档在读写文件时使用`b`模式，但是 Python 3 文档没有！我觉得这很奇怪，所以我将输出文件定义改为不使用`b`模式，将所有字节串改回 unicode 字符串，csv 转换器工作了！

## 发生了什么？

`b`模式使`open`内置函数以二进制模式打开文件，适合打开非文本文件。关于 [`open`函数](https://docs.python.org/2/library/functions.html)的 Python 2 文档指出，一些系统不会区别对待文本和二进制文件，将`b`附加到模式有利于文档化。因为在 Python 2 文档中，所有的文档都使用`rb`和`wb`进行 csv 操作，所以在我的 csv 编写器中包含`b`模式对 past-me 是有意义的。

然而，当以二进制模式打开文件时，Python 3 [`open`函数](https://docs.python.org/3/library/functions.html)期望并返回未编码的字节。当我最终钻研`CSVWriter.writerow`的实际定义时，我发现了这个:

```
 def writerow(self, row):
        if sys.version_info[0] < 3:
            r = []
            for item in row:
                if isinstance(item, text_type):
                    item = item.encode('utf-8')
                r.append(item)
            row = r
        self.writer.writerow(row) 
```

`writerow`将我的所有项转换回`utf-8`，即使我在传入它们之前已经将它们声明为字节串！所以当文件需要字节对象时，它得到的是错误的类型，不管我给写者什么。

# 有什么意义？

如果您习惯了 Python 2 的自由放任态度，那么 Python 3 中的字符串交互会变得很奇怪，而且内置函数并不总是按照您期望的方式处理输入。这是一个你应该寻找的确切的东西的例子(并且为之编写回归测试！)将项目移植到 Python 3 时。