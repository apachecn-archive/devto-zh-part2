# [P1]用 C#编写序列化库:ion 的例子

> 原文：<https://dev.to/goaty92/p1-writing-a-serialization-library-in-c-the-case-for-ion--24p0>

在我的暑期实习期间，我有机会使用亚马逊的一种叫做 [Ion](http://amzn.github.io/ion-docs/) 的开源数据序列化格式。Amazon 将 Ion 描述为“一种丰富类型、自描述、分层的格式，提供可互换的二进制和文本表示”，目前提供 Java、Python、C 和 Javascript 的库。这篇文章是一系列博客中的第一篇，在这些博客中，我~~宣传~~用 C#实现这种格式，这种格式可以(希望)适用于所有人。NET 平台。

## 那么我为什么要用这个离子的东西呢？

Ion 提供了一种类型丰富、紧凑的二进制格式，可以有效地进行解析，并且还提供了一种可读的文本格式来支持原型/开发。

**在 github 上试用** [IonDotnet](https://github.com/goaty92/IonDotnet) 。请不要对我的代码尖叫😂。

亚马逊有一个[整页](http://amzn.github.io/ion-docs/guides/why.html)谈论 Ion 相对于其他类似格式的优势，如果你感兴趣，当然可以阅读。在这篇文章中，我将主要从开发者的角度进行讨论。

如果您曾经使用过涉及多台计算机的软件，那么您可能曾经使用过序列化。它是将数据对象转换成字节序列的过程，然后可以存储或发送到其他进程/机器。今天最著名的两种序列化格式是(当然)JSON 和 XML，您肯定听说过这两种格式。

一般来说，现在的序列化软件有 2 种。第一种是我称之为*静态*序列化:你声明你的模型，然后提前生成序列化该模型的代码。例如，谷歌的[协议缓冲区](https://developers.google.com/protocol-buffers/)和[平面缓冲区](https://google.github.io/flatbuffers/)就是这样做的。这种方法产生了非常紧凑和高效的输出:序列化的对象基本上不包含元数据，解析字节只是将内存布局“投射”到运行时对象中。然而，这是有代价的:由于格式是静态的，更新对象模型意味着重新生成序列化代码，这可能会导致对现有消费者的破坏性更改。对于期望快速变化和发展的系统来说，这是一个不希望的结果。

另一方面，我们有像 JSON 这样更动态的格式*:数据的布局是在运行时根据你输入的数据类型生成的。作为一种基于文本的格式，JSON 可读性很强，这也是它受欢迎的原因之一(除了它是 Javascript 的原生特性之外)。也就是说，即使作为一种文本格式，JSON 也有很多缺点。*

 *### 系统类型

比方说，你正在编写一个实验室软件，管理实验和处理这样的对象模型。

```
enum ExperimentResult
{
    Success,
    Failure,
    Unknown
}

class Experiment
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTimeOffset StartDate { get; set; }
    public TimeSpan Duration { get; set; }
    public bool IsActive { get; set; }
    public byte[] SampleData { get; set; }
    public decimal Budget { get; set; }
    public ExperimentResult Result { get; set; }
}

var experiment = new Experiment
{
    Id = 233,
    Name = "Measure performance impact of boxing",
    Duration = TimeSpan.FromSeconds(90),
    StartDate = new DateTimeOffset(2018, 07, 21, 11, 11, 11, TimeSpan.Zero),
    IsActive = true,
    Result = ExperimentResult.Failure,
    SampleData = new byte[100],
    Budget = decimal.Parse("12345.01234567890123456789")
}; 
```

Enter fullscreen mode Exit fullscreen mode

使用[JSON.NET](https://www.newtonsoft.com/json)，如果我们做`JsonConvert.SerializeObject(experiment )`，我们得到

```
{  "Id":  233,  "Name":  "Measure performance impact of boxing",  "StartDate":  "2018-07-21T11:11:11+00:00",  "Duration":  "00:01:30",  "IsActive":  true,  "SampleData":  "2e36MMwesekp5vKCjNEZKyEi+mro6HfE6Q1UcxCwzguscpMX0PLV+qAvU7zlXth4+DyKrKUHjfB1Nka/yj7ZeBfm1ho9AlouTQDJuJW73os03HrTJiFlpOSjoZqsFTBiVtuk/g==",  "Budget":  12345.01234567890123456789,  "Result":  1  } 
```

Enter fullscreen mode Exit fullscreen mode

我们马上可以看到，JSON 序列化不能正确表示几种数据类型。例如，`ExperimentResult`枚举给了我们`"Result" : 1`，但是这是有问题的，因为这些数据的消费者将很难理解`1`作为`ExperimentResult`意味着什么。更糟糕的是，如果你更新了`ExperimentResult`枚举并在`Failure`之前添加了一个新的枚举，那么`1`就不再意味着`Failure`。当然，JSON.NET 允许我们将`enum`连载为`string` :

```
[JsonConverter(typeof(StringEnumConverter))]
public ExperimentResult Result { get; set; } 
```

Enter fullscreen mode Exit fullscreen mode

这将给我们`{"Result": "Failure"}`。但是即使这样，仍然有一个问题(除了那个丑陋的属性):`Result`现在是一个`string`，它通常被解释为*文本*，而不是*说明符*。

再比如`Timespan Duration`属性。这里 JSON.NET 以`hh:mm:ss`的格式给了我们字符串表示，但它仍然是一个字符串。失去了表示持续时间的意图。对于`DateTime`、`decimal`和`byte[]`属性也是如此，JSON.NET 会找到一种变通方法，通常是将它们格式化为`string`(比如 Base64 编码字节数组)。这些方法通常会导致值失去意义或增加输出的大小(就像`byte[]`)。

Ion 为这个问题提供了解决方案。首先，它有更多的*原生*类型，包括`decimal`(适合货币计算)`blob`、字节序列`symbol`、编码`Enum`。支持的数据类型的完整列表可以在[这里](http://amzn.github.io/ion-docs/docs/spec.html)找到。类型系统还可以通过使用注释进行扩展，这一点我将在以后的文章中讨论。

上述对象的(正确的)离子文本格式看起来像这样

```
{
  Id: 233,
  Name: "Measure performance impact of boxing",
  StartDate: 2018-07-21T11:11:11+00:00,
  Duration: seconds::90, //a time duration in seconds
  IsActive: true,
  SampleData: {{ 2e36MMwesekp5vKCjNEZKyEi+mro6HfE6Q1UcxCwzguscpMX0PLV+qAvU7zlXth4+DyKrKUHjfB1Nka/yj7ZeBfm1ho9AlouTQDJuJW73os03HrTJiFlpOSjoZqsFTBiVtuk/g== }},
  Budget: 12345.01234567890123456789,
  Result: 'Failure'
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来看上面的格式:字节序列`SampleData`在文本格式中表示为 Base64，但是在二进制格式中会被原样复制。解析二进制数据时不需要额外的编码解码。此外，双块符号`{{ }}`让我们知道它是一个`byte[]`，所以没有任何意义的价值是损失。类似地，枚举`Result`由一种称为*符号*的特殊文本表示，它放在单引号`''`中，与普通文本(`string`)相反，普通文本放在双引号`""`中。是的，Ion-text 支持评论。

#### 在 C#中使用离子

IonDotnet 的构建目标是支持标准 Ion 数据的读写，同时提供一组对。NET 开发人员。因此，使用 IonDotnet 比使用 [Java](https://github.com/amzn/ion-java) 要容易得多。下面的代码将`Experiment`对象序列化为二进制格式作为`byte[]`，然后返回:

```
byte[] ionBytes = IonSerialization.Serialize(experiment);
Experiment deserialized = IonSerialization.Deserialize<Experiment>(ionBytes); 
```

Enter fullscreen mode Exit fullscreen mode

在撰写本文时，IonDotnet 的文本序列化的实现还没有完成。生产系统应该使用二进制格式，因为它紧凑，文本格式是可读的，可以用来支持开发和原型。

### 密实度

一块数据可以被认为包含两个部分:实际信息和*元数据*(数据应该如何被读取/解析)。JSON 作为一种基于文本的格式，浪费了大量空间来存放*元数据*的东西，比如字段名、引号(`""`)和括号(`[],{}`)。JSON 中的数字表示也不是最佳的:一个 4 字节的二进制数在转换成文本时可能需要 10 个字节。

*静态*序列化格式(如协议缓冲区)实质上删除了数据中的所有*元数据*位，这使其非常紧凑，但也很严格，难以更改/更新。Ion 在两者之间寻求平衡:它比 JSON 更紧凑，但本质上仍然是动态的。更改/更新您的 Ion 格式应该不会比使用 JSON 更难。

让我们看看下面的代码，它比较了一个典型 Web API 的 JSON 和 ion 的序列化大小(来自 Foursquare):

```
 private static string GetJson(string api)
{
    using (var httpClient = new HttpClient())
    {
        var str = httpClient.GetStringAsync(api);
        str.Wait();
        return str.Result;
    }
}

var jsonString = GetJson(@"https://api.foursquare.com/v2/venues/explore?near=NYC
                &oauth_token=IRLTRG22CDJ3K2IQLQVR1EP4DP5DLHP343SQFQZJOVILQVKV&v=20180728");

var obj = JsonConvert.DeserializeObject<RootObject>(jsonString);
byte[] jsonBytes = Encoding.UTF8.GetBytes(jsonString);
byte[] ionBytes = IonSerialization.Serialize(obj);

Console.WriteLine($"JSON size: {jsonBytes.Length} bytes");
Console.WriteLine($"ION size: {ionBytes.Length} bytes"); 
```

Enter fullscreen mode Exit fullscreen mode

并且输出为:

```
JSON size: 70920 bytes
ION size: 40675 bytes 
```

Enter fullscreen mode Exit fullscreen mode

这大约节省了 40%的尺寸！这甚至不是最好的情况。由于 Ion 编码数据的方式，您可以通过使传输的两端在编码符号集上达成一致来节省更多的空间，在这种情况下，您可以去掉所有的“字段名”,使其大小非常接近 Protobuf 之类的无模式序列化协议。这对于游戏和实时通信等高性能场景非常有用。*