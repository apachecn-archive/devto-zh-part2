# 面向 Go 程序员的 C++:第 2 部分-切片

> 原文：<https://dev.to/dayvonjersen/c-for-go-programmers-part-2---slices-2771>

> **免责声明** : *本系列标题的灵感来自 Go Wiki* **上的一篇精彩文章[，但是，与那篇文章](https://github.com/golang/go/wiki/GoForCPPProgrammers)**，*不同的是，本系列中的任何信息都不应被误认为是好的建议。[参见第 0 部分的介绍](https://dev.toabout:blank#coming-soon-i-promise)。*
> 
> 说真的，不要做我在这篇文章中将要演示的内容。如果你使用的是 C++，只需使用 [std::vector](https://en.cppreference.com/w/cpp/container/vector) 。如果你使用 C 语言，看看[的弹性缓冲区](https://github.com/nothings/stb/blob/master/stretchy_buffer.h)。
> **编辑**:我也弄错了切片语义。Go 中的`s1 := s0[:]`实际上是让[两者引用同一个底层数组](https://play.golang.org/p/AgiI3p902pI)，而不是进行复制。而且`realloc()`也可能把事情搞砸。感谢 jstag on [#go-nuts](//irc://irc.freenode.net/#go-nuts) 指出了这些错误。

作为一个思想实验，让我们尝试用 C 和 C++实现 Go 切片。如果你不想要详细解说，看看 C [和 C++](https://gist.github.com/dayvonjersen/2730d637c7e5950c43f655035f060183#file-slices-cpp) 中的成品[。](https://gist.github.com/dayvonjersen/2730d637c7e5950c43f655035f060183#file-slices-c)

让 Go slices 如此出色的部分原因是它方便的语法:

```
s0 := []int{1, 2, 3}       // literals
s1 := make([]byte, 0, 512) // or specify length and capacity
s2 := s0[1:]               // s2 == {2, 3}, s0 is still == {1, 2, 3}
s3 := s0[:] // create a copy of a slice
s3[2] = 5   // modify in-place
somethingThatImplementsioReader.Read(s1) // slices are reference types, s1 has data in it now (maybe)
s0 = append(s0, 4, 5, 6) // append implicitly resizes
s1 = append([]int{0}, append(s0[:1], s0[2:]...)...) // like I said, nice! 
```

Enter fullscreen mode Exit fullscreen mode

我们不要那种快乐的废话。这是 c。

> **注意** : Go 还有一个非常好的特性，它可以毫不费力地将本机 utf8 `string`类型转换为`[]byte`或`[]rune`类型。我不知道如何在 C/C++中实现这一点。

## 切片在 C

切片具有长度、容量和指向底层数组的指针。

```
typedef struct _slice {
    int   len;
    int   cap;
    void* arr;
} slice; 
```

Enter fullscreen mode Exit fullscreen mode

内置功能 [`len`](https://godoc.org/builtin#len) 和 [`cap`](https://godoc.org/builtin#cap) 让您访问切片的长度和容量。

```
int len(slice* s) { return s->len; }
int cap(slice* s) { return s->cap; } 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这非常简单明了。

但实际上，我们需要存储类型信息。在 C 语言中，类型不是一级值，所以我们将把类型的**大小存储在字节**中，希望这样就够了。我们稍后将回到这一点。

```
typedef struct _slice {
    size_t type;
    int    len;
    int    cap;
    void*  arr;
} slice; 
```

Enter fullscreen mode Exit fullscreen mode

内置函数 [`make`](https://godoc.org/builtin#make) 分配具有指定长度和可选容量的切片。

我们需要这两个参数以及数组元素类型的大小(以字节为单位)作为第一个参数。

```
slice* make(size_t type, int len, int cap) {
    slice* s = calloc(1, sizeof(slice));
    s->arr   = calloc(cap, type);
    s->type = type;
    s->len  = len;
    s->cap  = cap;
    return s;
} 
```

Enter fullscreen mode Exit fullscreen mode

对切片进行切片会创建一个新切片。然而，我们不能使用括号符号，所以我们将它作为一个函数。

```
// naming stuff is hard
slice* sslice(slice* s, int start, int end) {
    int len = end-start;
    slice* s2 = make(s->type, len, len);
    memcpy(s2->arr, s->arr + start * s->type, len * s->type);
    s2->len = len;
    return s2;
} 
```

Enter fullscreen mode Exit fullscreen mode

指针算法的乐趣就从这里开始。如你所知，`arr[i]`只是`*(arr + i)`的简写，但是编译器需要知道`arr`的类型，这样才能工作。我们在这里使用`void*`，所以我们必须将偏移量乘以类型的大小(以字节为单位)。

> “复制内置函数将元素从源切片复制到目标切片。[...]源和目的地可能重叠。Copy 返回复制的元素数，这将是 len(src)和 len(dst)中的最小值。
> 
> [https://godoc.org/builtin#copy](https://godoc.org/builtin#copy)T2】

```
int copy(slice* dst, slice* src) {
    if(dst->type != src->type)  return 0; // should generate an error somehow actually
    int len = dst->len < src->len ? dst->len : src->len;
    memcpy(dst->arr, src->arr, len * src->type);
    return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有足够的资源来开始使用这些切片，但是`append()`才是让 Go 切片如此有价值的真正原因。

如果我们知道底层数组的类型，我们可以这样做:

```
typedef struct _int_slice {
    int  len;
    int  cap;
    int* arr;
} int_slice;

// same len, cap, make, sslice, and copy... except you could could omit type and use sizeof(int)

int_slice* append(slice* s, int elem) {
    if(s->cap < s->len+1) {
        s->arr = realloc(s->arr, sizeof(int) * s->len+1);
        s->cap = s->len+1;
    }
    s->arr[s->len++] = elem;
    return s;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是因为我们想要任何 T2 类型的切片，我们必须使用 T0。

> **注意**:因此，我们不能传递文字或值类型给我们的`append()`，我们总是必须传递一个指针类型。类似于`int a = 5; s = append(s, &a);`的东西在我的小玩具示例中起作用，但并不太好。

回想一下，在 C 语言中，一个字节是一个`unsigned char`，我们以字节为单位存储了每个单独元素的类型大小，因此:

```
slice* append(slice* s, void* elem) {
    if(s->cap < s->len+1) {
        s->arr = realloc(s->arr, s->type * s->len+1);
        s->cap = s->len+1;
    }
    int offset = s->len * s->type;
    for(int i = 0; i < s->type; i++) {
        *((unsigned char*)s->arr + offset + i) = *((unsigned char*)elem + i);
    }
    s->len += 1;
    return s;
} 
```

Enter fullscreen mode Exit fullscreen mode

“啊”，我现在几乎能听到你说，“但是真正的`append()`需要变量参数！”我掩护你，家人:

```
slice* append(int count, slice* s, ...) {
    va_list args;
    va_start(args, s);

    if(s->cap < s->len+count) {
        s->arr = realloc(s->arr, s->type * s->len+count);
        s->cap = s->len+count;
    }

    int offset = s->len * s->type;
    for(int j = 0; j < count; j++) {
        unsigned char* elem = va_arg(args, unsigned char*);
        for(int i = 0; i < s->type; i++) {
            *((unsigned char*)s->arr + offset + i + j*s->type) = elem[i];
        }
    }
    va_end(args);

    s->len += count;
    return s;
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我们必须指定追加多少元素，因为`<stdarg.h>`没有提供计算可变参数数量的机制。这与 C 希望程序员不断跟踪数组长度的方式是完全一致的，并且会很高兴地读出你脸上的 end 和 Segfault，这又回到了为什么你首先会想使用像 slices 这样的东西。

**完整源代码:[slices . c](https://gist.github.com/dayvonjersen/2730d637c7e5950c43f655035f060183#file-slices-c)T3】👀**

> 当你完成时，不要忘记`free()`你的切片和它们的底层数组。

## c++中的切片

所以现在我们在 C 中有了 Go slices，但是如果我们试图编译这段代码，C++(和它的创建者)会对我们大喊大叫。这只是强制转换*alloc 的返回值并忽略和/或关闭一些编译器警告的问题，但是让我们用类和模板来尝试正确的方法:

```
template<typename T>
class slice {
    int _len;
    int _cap;
    T*  _arr;
public:
    slice(int len) : slice(len,len) {}
    slice(int len, int cap) {
        _len = len;
        _cap = cap;
        _arr = (T*) calloc(cap, sizeof(T));
    }
    ~slice() {
        free(_arr);
    }

    int len() { return _len; }
    int cap() { return _cap; }

    T& operator { return _arr[i]; }

    void append(T elem) {
        if(_cap < _len+1) {
            _arr = (T*) realloc(_arr, _len+1 * sizeof(T));
            _cap = _len+1;
        }
        _arr[_len++] = elem;
    }

    template<typename... Targs>
    void append(T elem, Targs... args) {
        append(elem);
        append(args...);
    }

    slice<T> sslice(int start, int end) {
        int len = end-start;
        slice<T> s2(0, len);
        for(int i = 0; i < len; i++) {
            s2.append(_arr[start + i]);
        }
        return s2;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

**完整源代码:[slices . CPP](https://gist.github.com/dayvonjersen/2730d637c7e5950c43f655035f060183#file-slices-cpp)T3】👀**

模板让我们可以拥有任何类型的切片，而不必做任何指针运算或令人讨厌的转换。

我想我开始听起来有点太像 Bjarne 了，所以我应该跳过详细报道，尽快结束。

> **注意**:我必须使用 stdlib *alloc 和 friends，因为`new[]`对于一个*无界的*数组类成员会导致一些奇怪的结果...

使用 C++的好处:

*   我们可以自动调用析构函数中的`free()`

*   用于直接访问切片元素的括号符号。

*   构造函数重载让我们获得与 Go 的`make()`相同的参数(可以省略容量)

*   C++11 为变量参数引入了参数包，这个好家伙在这里做了很好的解释。我并不热衷于使用递归，但是实际上你永远不会在一个调用中添加足够的元素来溢出堆栈。

## 结束

总之，不要这样。您没有 Go 切片的方便语法，也没有 Go 字符串和`[]byte`或`[]rune`之间转换的灵活性，同时还必须管理自己的内存。总是`realloc()`对性能没有好处，如果你忘记`free()`每一个切片，你将会泄漏内存，特别是因为每次切片都会创建一个新的切片。同样，如果你正在使用 C++只需使用 [std::vector](https://en.cppreference.com/w/cpp/container/vector) 否则检查[弹性缓冲区](https://github.com/nothings/stb/blob/master/stretchy_buffer.h)。这两篇文章都检查了 OOM 条件，并且没有在 30 分钟内被拼凑起来为一个甚至不是真正系列的系列写一篇后续文章。