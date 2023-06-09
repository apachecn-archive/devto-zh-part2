# 弦是邪恶的

> 原文：<https://dev.to/indy_singh_uk/strings-are-evil-9f9>

将内存分配从 7.5GB 减少到 32KB

# 目录

1.  [问题的背景](#1)
2.  [建立基线](#2)
3.  [轻松获胜 1](#3)
4.  [轻松取胜 2](#4)
5.  [劈叉从来都不酷](#5)
6.  [列表并不总是好的](#6)
7.  [共享字节数组](#7)
8.  [再见 StringBuilder](#8)
9.  [跳过逗号](#9)
10.  [类和结构之间的战争](#10)
11.  [再见 StreamReader](#11)
12.  [TLDR——给我一张桌子](#12)

## 问题的背景

Codeweavers 是一家金融服务软件公司，我们所做的一部分是让我们的客户能够将他们的数据批量导入我们的平台。对于我们的服务，我们需要来自所有客户的最新信息，包括英国各地的贷款人和制造商。这些导入中的每一个都可能包含数百兆字节的未压缩数据，这些数据通常每天都会被导入。

这些数据随后被用于支持我们的实时计算。目前，这个导入过程必须在工作时间之外进行，因为它会影响内存的使用。

在本文中，我们将探讨导入过程的潜在优化，特别是在导入过程中减少内存的情况下。如果你想自己试一试，你可以使用[这个代码](https://gist.github.com/indy-singh/f10b76c4a00e807625f9b5a8d1989772)生成一个样本输入文件，你可以在这里找到所有关于[的代码。](https://github.com/indy-singh/StringsAreEvil)

## 建立基线

当前的实现使用`StreamReader`并将每一行传递给`lineParser`。

```
using (StreamReader reader = File.OpenText(@"..\..\example-input.csv"))
{
    try
    {
        while (reader.EndOfStream == false)
        {
            lineParser.ParseLine(reader.ReadLine());
        }
    }
    catch (Exception exception)
    {
        throw new Exception("File could not be parsed", exception);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们最初的行解析器的最简单的实现是这样的:-

```
public sealed class LineParserV01 : ILineParser
{
    public void ParseLine(string line)
    {
        var parts = line.Split(',');

        if (parts[0] == "MNO")
        {
            var valueHolder = new ValueHolder(line);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ValueHolder`类在稍后的导入过程中用于将信息插入数据库:-

```
public class ValueHolder
{
    public int ElementId { get; }
    public int VehicleId { get; }
    public int Term { get; }
    public int Mileage { get; }
    public decimal Value { get; }

    public ValueHolder(string line)
    {
        var parts = line.Split(',');

        ElementId = int.Parse(parts[1]);
        VehicleId = int.Parse(parts[2]);
        Term = int.Parse(parts[3]);
        Mileage = int.Parse(parts[4]);
        Value = decimal.Parse(parts[5]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将此示例作为命令行应用程序运行并启用监控:-

```
public static void Main(string[] args)
{
    AppDomain.MonitoringIsEnabled = true;

    // do the parsing

    Console.WriteLine($"Took: {AppDomain.CurrentDomain.MonitoringTotalProcessorTime.TotalMilliseconds:#,###} ms");
    Console.WriteLine($"Allocated: {AppDomain.CurrentDomain.MonitoringTotalAllocatedMemorySize / 1024:#,#} kb");
    Console.WriteLine($"Peak Working Set: {Process.GetCurrentProcess().PeakWorkingSet64 / 1024:#,#} kb");

    for (int index = 0; index <= GC.MaxGeneration; index++)
    {
        Console.WriteLine($"Gen {index} collections: {GC.CollectionCount(index)}");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们今天的主要目标是减少[分配的](https://blog.maartenballiauw.be/post/2016/10/19/making-net-code-less-allocatey-garbage-collector.html) [内存](http://tooslowexception.com/allocation-is-cheap-until-it-is-not/)。简而言之，我们分配的内存越少，垃圾收集器要做的工作就越少。垃圾收集器操作的[有三代](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals#generations)，我们也会监视它们。垃圾收集是一个复杂的主题，超出了本文的范围；但是一个好的经验法则是，短命对象永远不应该被提升到第 0 代以上。

我们可以看到`V01`有如下统计:-

```
Took: 8,750 ms
Allocated: 7,412,303 kb
Peak Working Set: 16,720 kb
Gen 0 collections: 1809
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

几乎 7.5 GB 的内存分配来解析 300 兆字节的文件是不理想的。现在我们已经建立了基线，让我们找到一些简单的成功案例…

## 轻松获胜 1

眼尖的读者会两次发现我们；一次在行解析器中，另一次在`ValueHolder`的构造函数中。这很浪费，我们可以重载`ValueHolder`的构造函数来接受一个`string[]`数组，并在解析器中拆分一次。简单更改后，`V02`的统计数据现在是:-

```
Took: 6,922 ms
Allocated: 4,288,289 kb
Peak Working Set: 16,716 kb
Gen 0 collections: 1046
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们从 7.5GB 降到了 4.2GB。但对于处理 300 兆字节的文件来说，这仍然是很大的内存分配。

## 轻松获胜 2

对输入文件的快速分析显示有`10,047,435`行，我们只对前缀为`MNO`的行感兴趣，其中有`10,036,466`行。这意味着我们不必要地处理额外的`10,969`行。对`V03`的一个快速修改，只解析前缀为`MNO` :-
的行

```
public sealed class LineParserV03 : ILineParser
{
    public void ParseLine(string line)
    {
        if (line.StartsWith("MNO"))
        {
            var valueHolder = new ValueHolder(line);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们推迟拆分整行，直到我们知道这是我们感兴趣的一行。不幸的是，这并没有为我们节省多少内存。主要是因为我们对文件中的`99.89%`行感兴趣。`V03` :-
的统计数据

```
Took: 8,375 ms
Allocated: 4,284,873 kb
Peak Working Set: 16,744 kb
Gen 0 collections: 1046
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

是时候使用可靠的分析器了，在本例中是 [dotTrace](https://www.jetbrains.com/profiler/) :-

[![](img/ee102e730b396ff7166e86d6e19d6704.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--78DtUAz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/di8odrr1ouxlx4mq7nnf.JPG)

中的字符串。NET 生态系统是不可改变的。这意味着我们对一个`string` *做的任何事情总是*返回一个全新的副本。因此，在每一行调用`string.Split(',')`(记住我们感兴趣的是`10,036,466`行)会返回被分成几个小字符串的那一行。每行至少有五个我们想要处理的部分。这意味着在导入流程的生命周期中，我们至少要创建`50,182,330 strings`..！接下来，我们将探讨如何消除`string.Split(',')`的使用。

## 劈叉从来都不酷

我们感兴趣的典型行看起来像这样:-

```
MNO,3,813496,36,30000,78.19,, 
```

Enter fullscreen mode Exit fullscreen mode

调用上面一行中的`string.Split(',')`将返回一个包含:-
的`string[]`

```
'MNO'
'3'
'813496'
'36'
'30000'
'78.19'
''
'' 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以对正在导入的文件做出一些保证:-

*   每条线的长度不固定
*   由逗号分隔的部分的数量是固定的
*   我们仅使用每行的前三个字符来确定我们对该行的兴趣
*   这意味着我们对五个部分感兴趣，但是部分长度未知
*   截面不改变位置(如`MNO`总是第一个截面)

保证建立后，我们现在可以为给定行的所有逗号的位置建立一个短期索引:-

```
private List<int> FindCommasInLine(string line)
{
    var list = new List<int>();

    for (var index = 0; index < line.Length; index++)
    {
        if (line[index] == ',')
        {
            list.Add(index);
        }
    }

    return list;
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们知道了每个逗号的位置，我们就可以直接访问我们关心的部分，并手动解析该部分。

```
private decimal ParseSectionAsDecimal(int start, int end, string line)
{
    var sb = new StringBuilder();

    for (var index = start; index < end; index++)
    {
        sb.Append(line[index]);
    }

    return decimal.Parse(sb.ToString());
}

private int ParseSectionAsInt(int start, int end, string line)
{
    var sb = new StringBuilder();

    for (var index = start; index < end; index++)
    {
        sb.Append(line[index]);
    }

    return int.Parse(sb.ToString());
} 
```

Enter fullscreen mode Exit fullscreen mode

将所有这些放在一起:-

```
public void ParseLine(string line)
{
    if (line.StartsWith("MNO"))
    {
        var findCommasInLine = FindCommasInLine(line);

        var elementId = ParseSectionAsInt(findCommasInLine[0] + 1, findCommasInLine[1], line); // equal to parts[1] - element id
        var vehicleId = ParseSectionAsInt(findCommasInLine[1] + 1, findCommasInLine[2], line); // equal to parts[2] - vehicle id
        var term = ParseSectionAsInt(findCommasInLine[2] + 1, findCommasInLine[3], line); // equal to parts[3] - term
        var mileage = ParseSectionAsInt(findCommasInLine[3] + 1, findCommasInLine[4], line); // equal to parts[4] - mileage
        var value = ParseSectionAsDecimal(findCommasInLine[4] + 1, findCommasInLine[5], line); // equal to parts[5] - value
        var valueHolder = new ValueHolder(elementId, vehicleId, term, mileage, value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`V04`会显示这些统计数据:-

```
Took: 9,813 ms
Allocated: 6,727,664 kb
Peak Working Set: 16,872 kb
Gen 0 collections: 1642
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

哎呦，比预想的还要糟糕。这是一个容易犯的错误，但是 dotTrace 可以帮助我们…

[![](img/92d1e8fe3e5f8bb09f62a67f6fdfed57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nB6QyPlh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5w4mefamx72l4eskns9o.JPG)

为每一行的每一部分构建一个`StringBuilder`是非常昂贵的。幸运的是这是一个快速修复，我们在`V05`的构造上构造了一个单独的`StringBuilder`，并在每次使用之前清除它。`V05`现在有以下统计:-

```
Took: 9,125 ms
Allocated: 3,199,195 kb
Peak Working Set: 16,636 kb
Gen 0 collections: 781
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

唷，我们又回到了下降趋势。我们从 7.5GB 开始，现在降至 3.2GB

## 列表并不总是好的

在这一点上，dotTrace 成为优化过程的一个重要部分。查看`V05`点迹输出:-

[![](img/80e686c3a2f72eebb350ac24696b2bb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4DMevUmm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w0bpuhbbaq0pvjvqb70u.JPG)

构建逗号位置的短期索引是昂贵的。因为在任何`List<T>`下面都只是一个标准的`T[]`数组。该框架负责在添加元素时调整底层数组的大小。这在典型的场景中非常有用和方便。然而，我们知道有六个部分需要处理(但是我们只对其中的五个部分感兴趣)，因此至少有七个逗号需要索引。我们可以为此进行优化:-

```
private int[] FindCommasInLine(string line)
{
    var nums = new int[7];
    var counter = 0;

    for (var index = 0; index < line.Length; index++)
    {
        if (line[index] == ',')
        {
            nums[counter++] = index;
        }
    }

    return nums;
} 
```

Enter fullscreen mode Exit fullscreen mode

`V06`统计:-

```
Took: 8,047 ms
Allocated: 2,650,318 kb
Peak Working Set: 16,560 kb
Gen 0 collections: 647
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

2.6GB 已经很不错了，但是如果我们强迫编译器使用`byte`而不是编译器默认使用`int` :-
会怎么样呢

```
private byte[] FindCommasInLine(string line)
{
    byte[] nums = new byte[7];
    byte counter = 0;

    for (byte index = 0; index < line.Length; index++)
    {
        if (line[index] == ',')
        {
            nums[counter++] = index;
        }
    }

    return nums;
} 
```

Enter fullscreen mode Exit fullscreen mode

重新运行`V06` :-

```
Took: 8,078 ms
Allocated: 2,454,297 kb
Peak Working Set: 16,548 kb
Gen 0 collections: 599
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

2.6GB 已经很不错了，2.4GB 更好。这是因为一个`int`比一个`byte`具有大得多的[范围](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/integral-types-table)。

## 共享字节数组

`V06`现在有一个`byte[]`数组，保存每行每个逗号的索引。这是一个短暂的数组，但它被创建了很多次。我们可以通过使用最近添加到的来消除为每一行创建新的`byte[]`的成本。NET 生态系统；`Systems.Buffers`。Adam Sitnik 对如何使用它以及为什么应该使用它进行了详细的分析。使用`ArrayPool<T>.Shared`时要记住的重要事情是，你必须在使用完租用的缓冲区后归还它，否则你会在应用程序中引入内存泄漏。

这就是`V07`的样子:-

```
public void ParseLine(string line)
{
    if (line.StartsWith("MNO"))
    {
        var tempBuffer = _arrayPool.Rent(7);

        try
        {
            var findCommasInLine = FindCommasInLine(line, tempBuffer);
            // truncated for brevity
        }
        finally
        {
            _arrayPool.Return(tempBuffer, true);
        }
    }
}

private byte[] FindCommasInLine(string line, byte[] nums)
{
    byte counter = 0;

    for (byte index = 0; index < line.Length; index++)
    {
        if (line[index] == ',')
        {
            nums[counter++] = index;
        }
    }

    return nums;
} 
```

Enter fullscreen mode Exit fullscreen mode

并且`V07`有如下统计:-

```
Took: 8,891 ms
Allocated: 2,258,272 kb
Peak Working Set: 16,752 kb
Gen 0 collections: 551
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

从 7.5GB 降至 2.2GB。这相当不错，但我们还没有完成。

## 再见了 StringBuilder

剖析揭示了下一个问题

[![](img/4229caef0111b083ead355083387fa1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYyh3-Tp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pff4ihm0tji7w9ym7j6o.JPG)

在`decimal`和`int`解析器内部调用`StringBuilder.ToString()`是非常昂贵的。是时候弃用`StringBuilder`了，不依赖字符串，不调用`int.parse()` / `decimal.parse()`，自己写 <sup>1</sup> `int`和`decimal`解析器。根据评测器，这应该会减少大约 1GB。写完我们自己的`int`和`decimal`解析器`V08`之后，现在的时钟在:-

```
Took: 6,047 ms
Allocated: 1,160,856 kb
Peak Working Set: 16,816 kb
Gen 0 collections: 283
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

1.1GB 比我们上次的水平(2.2GB)有了巨大的提高，甚至好于基准水平(7.5GB)。

<sup>1</sup> 代码可以在[这里找到](https://github.com/indy-singh/StringsAreEvil/blob/master/StringsAreEvil/LineParserV08.cs)

## 跳过逗号

在`V08`之前，我们的策略是找到每行上每个逗号的索引，然后使用该信息创建一个子字符串，然后通过调用`int.parse()` / `decimal.parse()`解析该子字符串。`V08`不赞成使用子字符串，但仍然使用逗号位置的短期索引。

另一种策略是，通过计算前面逗号的数量，跳到我们感兴趣的部分，然后解析所需数量的逗号之后的内容，并在遇到下一个逗号时返回。

我们之前保证:-

*   每一部分前面都有一个逗号。
*   并且一行中每个部分的位置不会改变。

这也意味着我们可以放弃租用的`byte[]`阵列，因为我们不再构建短期索引:-

```
public sealed class LineParserV09 : ILineParser
{
    public void ParseLine(string line)
    {
        if (line.StartsWith("MNO"))
        {
            int elementId = ParseSectionAsInt(line, 1); // equal to parts[1] - element id
            int vehicleId = ParseSectionAsInt(line, 2); // equal to parts[2] - vehicle id
            int term = ParseSectionAsInt(line, 3); // equal to parts[3] - term
            int mileage = ParseSectionAsInt(line, 4); // equal to parts[4] - mileage
            decimal value = ParseSectionAsDecimal(line, 5); // equal to parts[5] - value
            var valueHolder = new ValueHolder(elementId, vehicleId, term, mileage, value);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是`V09`并没有为我们节省任何内存，但是它确实减少了花费的时间:-

```
Took: 5,703 ms
Allocated: 1,160,856 kb
Peak Working Set: 16,572 kb
Gen 0 collections: 283
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

`V09`的另一个好处是它读起来更接近原始实现。

## 类和 struts 之间的战争

这篇博文不打算讨论类和结构的区别和利弊。那个话题已经被[覆盖了](https://softwareengineering.stackexchange.com/questions/92339/when-do-you-use-a-struct-instead-of-a-class) [很多次](https://stackoverflow.com/questions/13049/whats-the-difference-between-struct-and-class-in-net)。在这个特定的上下文中，使用`struct`是有益的。将`V10`中的`ValueHolder`改为`struct`有如下统计:-

```
Took: 5,594 ms
Allocated: 768,803 kb
Peak Working Set: 16,512 kb
Gen 0 collections: 187
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们低于 1GB 的限制。另外，警告你不要盲目使用`struct`，总是测试你的代码，确保用例是正确的。

## 再见 StreamReader

从`V10`开始，行解析器本身实际上是无分配的。dotTrace 揭示了剩余分配发生的位置

[![](img/53a2691783cc8b8a991d72e7eef97329.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XgkablqE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61kx4blefg2x8xy0nuj1.JPG)

这很尴尬，这个框架消耗了我们的内存分配。我们可以在比`StreamReader` :-
更低的层次上与文件交互

```
private static void ViaRawStream(ILineParser lineParser)
{
    var sb = new StringBuilder();

    using (var reader = File.OpenRead(@"..\..\example-input.csv"))
    {
        try
        {
            bool endOfFile = false;
            while (reader.CanRead)
            {
                sb.Clear();

                while (endOfFile == false)
                {
                    var readByte = reader.ReadByte();

                    // -1 means end of file
                    if (readByte == -1)
                    {
                        endOfFile = true;
                        break;
                    }

                    var character = (char)readByte;

                    // this means the line is about to end so we skip
                    if (character == '\r')
                    {
                        continue;
                    }

                    // this line has ended
                    if (character == '\n')
                    {
                        break;
                    }

                    sb.Append(character);
                }

                if (endOfFile)
                {
                    break;
                }

                var buffer = new char[sb.Length];

                for (int index = 0; index < sb.Length; index++)
                {
                    buffer[index] = sb[index];
                }

                lineParser.ParseLine(buffer);
            }
        }
        catch (Exception exception)
        {
            throw new Exception("File could not be parsed", exception);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`V11`统计:-

```
Took: 5,594 ms
Allocated: 695,545 kb
Peak Working Set: 16,452 kb
Gen 0 collections: 169
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

嗯，695MB 还是比 768MB 好。好吧，这不是我期待的改进(而且相当反气候)。直到，我们记得我们以前曾见过并解决过这个问题。在`V07`中，我们使用`ArrayPool<T>.Shared`来防止许多小的`byte[]`。我们可以在这里做同样的事情:-

```
private static void ViaRawStream(ILineParser lineParser)
{
    var sb = new StringBuilder();
    var charPool = ArrayPool<char>.Shared;

    using (var reader = File.OpenRead(@"..\..\example-input.csv"))
    {
        try
        {
            bool endOfFile = false;
            while (reader.CanRead)
            {
                // truncated for brevity

                char[] rentedCharBuffer = charPool.Rent(sb.Length);

                try
                {
                    for (int index = 0; index < sb.Length; index++)
                    {
                        rentedCharBuffer[index] = sb[index];
                    }

                    lineParser.ParseLine(rentedCharBuffer);
                }
                finally
                {
                    charPool.Return(rentedCharBuffer, true);
                }
            }
        }
        catch (Exception exception)
        {
            throw new Exception("File could not be parsed", exception);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`V11`的最终版本有以下统计数据:-

```
Took: 6,781 ms
Allocated: 32 kb
Peak Working Set: 12,620 kb
Gen 0 collections: 0
Gen 1 collections: 0
Gen 2 collections: 0 
```

Enter fullscreen mode Exit fullscreen mode

是的，只有 32kb 的内存分配。那个 ***就是我要找的*** 高潮。

找我 [Twitter](https://twitter.com/indy_singh_uk) ， [LinkedIn](https://uk.linkedin.com/in/indy-singh-uk) ，或者 [GitHub](https://github.com/indy-singh) 。

## TLDR -给我一张桌子

| 版本 | 耗时(毫秒) | 已分配(kb) | 峰值工作集(kb) | 第 0 代集合 |
| --- | --- | --- | --- | --- |
| 01 | Eight thousand seven hundred and fifty | Seven million four hundred and twelve thousand three hundred and three | Sixteen thousand seven hundred and twenty | One thousand eight hundred and nine |
| 02 | Six thousand nine hundred and twenty-two | Four million two hundred and eighty-eight thousand two hundred and eighty-nine | Sixteen thousand seven hundred and sixteen | One thousand and forty-six |
| 03 | Eight thousand three hundred and seventy-five | Four million two hundred and eighty-four thousand eight hundred and seventy-three | Sixteen thousand seven hundred and forty-four | One thousand and forty-six |
| 04 | Nine thousand eight hundred and thirteen | Six million seven hundred and twenty-seven thousand six hundred and sixty-four | Sixteen thousand eight hundred and seventy-two | One thousand six hundred and forty-two |
| 05 | Eight thousand one hundred and twenty-five | Three million one hundred and ninety-nine thousand one hundred and ninety-five | Sixteen thousand six hundred and thirty-six | Seven hundred and eighty-one |
| 06 | Eight thousand and seventy-eight | Two million four hundred and fifty-four thousand two hundred and ninety-seven | Sixteen thousand five hundred and forty-eight | Five hundred and ninety-nine |
| 07 | Eight thousand eight hundred and ninety-one | Two million two hundred and fifty-eight thousand two hundred and seventy-two | Sixteen thousand seven hundred and fifty-two | Five hundred and fifty-one |
| 08 | Six thousand and forty-seven | One million one hundred and sixty thousand eight hundred and fifty-six | Sixteen thousand eight hundred and sixteen | Two hundred and eighty-three |
| 09 | Five thousand seven hundred and three | One million one hundred and sixty thousand eight hundred and fifty-six | Sixteen thousand five hundred and seventy-two | Two hundred and eighty-three |
| Ten | Five thousand five hundred and ninety-four | Seven hundred and sixty-eight thousand eight hundred and three | Sixteen thousand five hundred and twelve | One hundred and eighty-seven |
| Eleven | Six thousand seven hundred and eighty-one | Thirty-two | Twelve thousand six hundred and twenty | Zero |