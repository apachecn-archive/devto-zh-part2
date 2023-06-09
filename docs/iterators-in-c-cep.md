# C 语言中的迭代器

> 原文：<https://dev.to/noah11012/iterators-in-c-cep>

把这篇文章当作我关于容器的文章的姊妹篇:[https://dev.to/noah11012/containers-in-c-34pi](https://dev.to/noah11012/containers-in-c-34pi)

在 C 中使用迭代器的动力是什么？我们只要看看 C++就能找到答案。使用像`std::find()`这样的算法对容器本身不起作用。相反，使用一个叫做迭代器的抽象层。这允许标准算法通过它提供的迭代器间接地在任何标准容器上操作。为了访问这些容器提供的迭代器，我们对常量版本使用方法`begin()`和`end()`或者`cbegin()`和`cend()`。

`begin()`指向容器中的第一个元素，而`end()`指向最后一个元素之后的一个元素。几乎就像 C 字符串中的空终止符。

该标准提供的算法可以在定制类型上操作，只要它实现的迭代器具有典型迭代器的公共方法。迭代器也有不同的类型:`Input`和`Output`就是其中的两种。`Input`用于读取一个元素，然后递增到下一个元素。`Output`类似，只是它将值写入容器。

比方说，在这次讨论中，我们将使用可以在必要时调整自身大小的向量或数组。我们也有一个二分搜索法算法，`find()`,它需要一个开始迭代器，一个结束迭代器和一个要搜索的值。为了不使事情复杂化，我们将只增加搜索整数的能力。

我们将有下面的结构:

```
typedef struct
{
    int *values;
    int  curr_index;
} Iterator; 
```

还有一些功能:

```
void iter_new(Iterator *iter);
void iter_advance(Iterator *iter);
void iter_jump(Iterator *iter, int pos);
void iter_read(Iterator *iter, int *out_value);
void iter_write(Iterator *iter, int value); 
```

我们不会担心这些函数的实现细节，以免混淆迭代器重要性的讨论。

我们制作的这种迭代器叫做随机访问迭代器。这意味着我们可以在容器的元素中跳转到任何我们想要的地方，而不是依次读取每个元素。

这很有帮助，因为二进制算法会跳到数组的中间来查看元素是否在那里。