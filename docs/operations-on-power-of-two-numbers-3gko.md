# 两个数的幂运算

> 原文：<https://dev.to/reg__/operations-on-power-of-two-numbers-3gko>

2 的幂的数字(即 1、2、4、8、16、32、64、128、256、512、1024 等等...)在编程中尤其重要，这是由于计算机的工作方式——它们以二进制表示进行操作。有时需要确保某个数字是 2 的幂。例如，对于某些内存块的大小和对齐可能很重要。这个特性简化了对这些量的运算——它们可以用按位运算而不是算术运算来操作。

在这篇文章中，我想介绍在 C++中对 2 的幂数字进行 3 种常见运算的有效算法。我这样做只是为了把它们收集在一个地方，因为它们可以很容易地在互联网上的许多其他地方找到。这些操作也可以使用其他算法来实现。最显而易见的实现将涉及在位上的循环，但是相对于操作数类型中的位数，这将给出 O(n)的时间复杂度。下面的算法使用聪明的比特技巧来提高效率。他们有常数或对数时间，他们不使用任何流量控制。

**1。检查一个数是否是 2 的幂。**例子:

```
IsPow2(0)   == true (!!)
IsPow2(1)   == true
IsPow2(2)   == true
IsPow2(3)   == false
IsPow2(4)   == true
IsPow2(123) == false
IsPow2(128) == true
IsPow2(129) == false 
```

Enter fullscreen mode Exit fullscreen mode

这是我第一次想到的。这里的技巧是基于这样的观察:当一个数的二进制表示正好有一个位设置时，它是 2 的幂，例如 128 = 0b10000000。如果递减，所有较低有效位都会置 1:127 = 0b 1111111。按位 AND 检查这两个数字是否没有共同的位数。

```
template <typename T> bool IsPow2(T x)
{
    return (x & (x-1)) == 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

**2。找出大于或等于给定数字的 2 的最小幂。**例子:

```
NextPow2(0)   == 0
NextPow2(1)   == 1
NextPow2(2)   == 2
NextPow2(3)   == 4
NextPow2(4)   == 4
NextPow2(123) == 128
NextPow2(128) == 128
NextPow2(129) == 256 
```

Enter fullscreen mode Exit fullscreen mode

这本书我在图书馆藏了很久。

```
uint32_t NextPow2(uint32_t v)
{
    v--;
    v |= v >> 1; v |= v >> 2; v |= v >> 4; v |= v >> 8;
    v |= v >> 16;
    v++;
    return v;
}
uint64_t NextPow2(uint64_t v)
{
    v--;
    v |= v >> 1; v |= v >> 2; v |= v >> 4; v |= v >> 8;
    v |= v >> 16; v |= v >> 32;
    v++;
    return v;
} 
```

Enter fullscreen mode Exit fullscreen mode

**3。找出小于或等于给定数字的 2 的最大幂。**例子:

```
PrevPow2(0)   == 0
PrevPow2(1)   == 1
PrevPow2(2)   == 2
PrevPow2(3)   == 2
PrevPow2(4)   == 4
PrevPow2(123) == 64
PrevPow2(128) == 128
PrevPow2(129) == 128 
```

Enter fullscreen mode Exit fullscreen mode

我最近才需要这个，花了一段时间才在谷歌上找到。终于在 stack overflow 上的[这个帖子里找到了。](https://stackoverflow.com/a/48816285) 

```
uint32_t PrevPow2(uint32_t v)
{
    v |= v >> 1; v |= v >> 2; v |= v >> 4; v |= v >> 8;
    v |= v >> 16;
    v = v ^ (v >> 1);
    return v;
}
uint64_t PrevPow2(uint64_t v)
{
    v |= v >> 1; v |= v >> 2; v |= v >> 4; v |= v >> 8;
    v |= v >> 16; v |= v >> 32;
    v = v ^ (v >> 1);
    return v;
} 
```

Enter fullscreen mode Exit fullscreen mode