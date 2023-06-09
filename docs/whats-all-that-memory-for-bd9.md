# 那些记忆是干什么用的？

> 原文：<https://dev.to/philpearl/whats-all-that-memory-for-bd9>

#### 或许是用来存放字符串的？

[![](img/59a7cc63659c0429b9cbfe59731c3641.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iUNKhDYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A7juUZmdxIM0id44W.) 

<figcaption>照片由 [Sandrachile 拍摄。](https://unsplash.com/@sandrachile?utm_source=medium&utm_medium=referral)on[un splash](https://unsplash.com?utm_source=medium&utm_medium=referral)T8】</figcaption>

如果你真的想用 Go 来使用你的计算机上的内存——真的使用它，分配千兆字节的内存——那么你可能要为 Go 垃圾收集器(GC)付出很大代价。但是你可以做一些事情。

Go GC 检查您分配的内存中有哪些部分仍在使用。它通过在所有内存中查找对其他内存的引用来实现这一点。如果你已经分配了数百万块内存，那么所有这些“寻找”必然会占用一些 CPU 时间。因此，如果你真的想使用电脑中的千兆字节内存，你可能要对自己的行事方式小心一点。

#### 有多糟糕？

想象一下，你迫切需要记住 1 亿个随机的 20 字节字符串。如果以正常的方式进行，GC 会带来什么样的开销？

下面是一些分配这些字符串的代码。这使用了大约 3.5 GB 的内存。

```
space  :=  make([]string,  100*1000*1000)  for  i  :=  range  space  {  space[i]  =  fmt.Sprintf("%20.20d",  rand.Int())  } 
```

那么这对 GC 有什么影响呢？我们可以做的一件简单的事情就是调用 Go runtime 来强制执行 GC，然后测量这需要多长时间。

```
start  :=  time.Now()  runtime.GC()  fmt.Printf("GC takes %s\n",  time.Since(start)) 
```

那需要多长时间？

```
GC takes 730.93697ms 
```

哦。那是相当长的时间。

嗯，看一亿个东西(一个东西大概 7ns)还是挺快的。但是每次 GC 运行时消耗 700 毫秒的 CPU 时间无疑是“不理想”的。

如果我们再次运行 GC，花费的时间也差不多。每次 GC 运行时，我们都要处理大约 700 毫秒的 GC 工作，直到我们处理完这些字符串。

#### 怎么才能修好？

幸运的是，Go GC 非常聪明，它不会查看分配的每一块内存。如果它知道内存不包含任何指针，它就不会查看它。如果没有指针，内存就不能引用其他内存，所以 GC 不需要查看它来确定哪个内存不再被引用，因此可以被释放。

如果我们可以安排事情，这样我们可以存储没有任何指针的字符串，我们可以节省这个 GC 开销。

#### 哦，字符串包含指针？

是的，字符串包含指针。反射包向我们展示了一个字符串实际上是什么。

```
type  StringHeader  struct  {  Data  uintptr  Len  int  } 
```

字符串是一个指针，指向包含字符串字节和字符串长度的一块内存。因此，我们的 1 亿个字符串片段包含 1 亿个指针和 1 亿个长度。和 1 亿个单独的分配，为字符串保存字节。

#### 我们的解决方案

我们可以分配一个单独的字节片来包含所有字符串的所有字节，并在这个字节片中创建我们自己的包含偏移量的类字符串对象，而不是有 1 亿个单独的分配和 1 亿个指针。

我们定义一个字符串库来包含字符串字节。

```
type  stringbank  []byte 
```

这是我们字符串的“存储”版本，用偏移量代替指针。

```
type  bankedString  struct  {  offset  int  len  int  } 
```

我们可以制作一个函数，将一个字符串添加到字符串 bank 中，并返回一个 bankedString。这会将字符串中的字节复制到我们的字符串库中，并保存字符串的偏移量和长度。

```
func  (sb  *stringbank)  store(in  string)  bankedString  {  offset  :=  len(*sb)  *sb  =  append(*sb,  in…)  return  bankedString{  offset:  offset,  len:  len(in),  }  } 
```

然后，可以使用这个 bankedString 检索原始字符串。

```
func  (sb  stringbank)  get(in  bankedString)  string  {  return  string(sb[in.offset  :  in.offset+in.len])  } 
```

存储我们的随机字符串只需要一点点修改。我们需要分配一个 stringbank，我们将它做得足够大以容纳我们所有的字符串数据，并且我们保存一个[]bankedString 而不是[]string。

```
sb  :=  make(stringbank,  0,  20*100*1000*1000)  space  :=  make([]bankedString,  100*1000*1000)  for  i  :=  range  space  {  space[i]  =  sb.store(fmt.Sprintf("%20.20d",  rand.Int()))  } 
```

如果我们现在对 GC 计时，我们会得到显著的改进。

```
GC takes 108.166528ms 
```

对于 GC 来说，这仍然是一个相当长的时间，但是如果我们再次运行 GC，我们会看到一个更大的下降。GC 的第一次运行释放了我们在构建字符串切片时(相当不小心地)创建的临时字符串。一旦完成，GC 开销几乎为零。

```
GC takes 348.923µs 
```

#### 结论

我怀疑正常做这种事有没有意义。只有当您打算在流程的整个生命周期中保留这些字符串时，它才真正有意义，因为没有办法删除单个的字符串。

这对于其他情况说明了什么？也许你不想存储大量的数据。也许您正在构建某种 API 服务。这东西适用吗？好吧，如果你在所有的 goroutines 和 API 处理程序中使用了大量的 RAM，那么也许确实如此。如果您可以避免到处使用指针，也许您的一些分配最终会是无指针的，这可能会减少 GC 的整体 CPU 使用率。这可能会使您的程序性能更好，或者运行成本更低。只要确保你在任何改变之前和之后都进行了测量，以确保你确实有所改进。

* * *