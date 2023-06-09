# 用二进制编码！一件事？

> 原文：<https://dev.to/hamza/coding-in-binary-code--a-thing--5bck>

*最初发布于[我的作品集](https://hamza-tam.gitlab.io)。*

在你开始做任何错误的假设之前，不，这篇文章不会让你成为一个机器语言忍者！它的目的是向您展示一个用机器语言实现的基本而简单的算法，希望让您在更深层次上理解事情是如何工作的。

首先，我想向您展示如何实现的算法是插入排序算法。但是在写这篇文章的时候，我发现对于这篇文章的初学者来说，它的实现很难理解。所以我决定做一个更简单的程序，一个计算 16 个整数(32 位长的整数)之和的程序。

# “机器语言”不是语言而是语言

如果你不知道，机器语言并不像高级语言(C - JavaScript)一样是所有机器通用的语言...)，但它是一种特定于每个微处理器的语言。因此，我应该使用哪个文本编辑器，这是您可能会问的最后一个问题。因为现在你应该询问你应该使用哪个文档，因为它取决于你的程序将运行哪个微处理器。

# 我们要用哪个处理器？

在这个例子中，我将使用摩托罗拉 MC68000，也称为 MC68K，一个 32 位处理器，具有 2 MHz 的巨大频率(是的，你没听错*M*Hz)。是的，这是一个古老的方法，但是在 80 年代被广泛使用，并且从简单开始。

# 快速浏览硬件

首先我们有微处理器(哇！#队长 _ 明显)。除了它的逻辑和算术单元之外，让我们感兴趣的是它的寄存器。你可能会问这是一个什么样的寄存器。嗯，寄存器是位于微处理器内部的临时存储器。有两种类型，数据寄存器，它可以保存数据(再次感谢船长明显)，和地址寄存器，它保存内存地址。在我们的例子中，MC68K 有 8 个地址寄存器和 8 个数据寄存器，它们都可以容纳多达 32 位的数据。我们还将使用 16 M 字节的主内存(是的，谁需要更多)。

# 算法

```
i ← 0
sum ← 0
while i < length(A)
    sum ← sum + A[i]
end while 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你喜欢用更友好的语言，比如 C:

```
void sum(int arr[], int n)
{
   int i, sum = 0;
   for (i = 1; i < n; i++)
   {
       sum += arr[i];
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 等一下，我们先写汇编

为了不让我们淹没在“1”和“0”中，我们应该从用汇编语言编写代码开始。汇编语言是关于机器语言的一层。而且因为汇编只是一种更友好的写二进制的方式，所以也叫机器语言。

# 汇编代码

终于！为了简单起见，我们假设在**内存**中有我们要使用的数组。

**注意:为了简单起见，我将跳过一些步骤**

```
START   MOVE    #15,D0         We initialize the counter, we will be decrementing 
        CLR     D1             We clear some registers nothing fancy, this one for the cumulative sum
        CLR     D2             this one for the current value to add to the sum 
        LEA     ARRAY(PC),A0   We store the array adress in an adress register
        LEA     SUM,A1         We store the memory address where we are going to write the result

 LOOP   MOVE.W  (A0)+,D2       Now copy each each element of the array 
        ADD.L   D2,D1          we add it to the cumulative sum 
        DBRA    D0,BCL         We loop till the counter is equal to -1, yep that's how loops without condition are done in assembly 
        TRAP    #0             We exit ( It's more complicated than that, but hey we're simplifiying )
        SUM     DS.L    1      We're asking for an empty spot in the memory 
```

Enter fullscreen mode Exit fullscreen mode

# 有趣的部分(欢迎来到黑客帝国)

等一下，是的，我们用二进制写，但不是 1 和 0，而是十六进制。为了得到二进制代码，要么我们运行一个汇编程序，它将读取每条指令(一条指令是一行)，并以二进制形式写入，然后我们读取它放入的内存，以读取二进制代码，但这将破坏乐趣。或者我们手动翻译。为此，我们将再次需要处理器文档来了解如何将每个指令及其参数翻译成二进制。完成后，我们将获得以下代码:

```
303C 000F 4241 4242 41FA 0014 43F9 0000 103E 3418 D282 51C8 FFFA 2281 4E40 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果你坚持，这是二进制的:

```
1100000011110000000000000011110100001001000001010000100100001001000001111110100000000000010100010000111111100100000000000000000001000000111110001101000001100011010010100000100101000111001000111111111111101000100010100000010100111001000000 
```

Enter fullscreen mode Exit fullscreen mode

**注意:根据数组的地址和程序存放结果的地址**，代码会略有不同

# 结论

我希望这篇文章读起来有趣，也许你在生活中永远不需要使用汇编语言，但看到我们用高级语言编写的程序在现实中被微处理器看到总是很有趣的。