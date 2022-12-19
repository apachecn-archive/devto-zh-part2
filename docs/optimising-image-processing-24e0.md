# 优化图像处理

> 原文：<https://dev.to/indy_singh_uk/optimising-image-processing-24e0>

将内存消耗和时间减少 80%

## 目录

1.  [调查旧代码](#1)
2.  [流媒体正在胜出](#2)
3.  [汇集你的溪流](#3)
4.  [再见系统。画画，你不会错过](#4)
5.  [为了透明起见](#5)
6.  [TLDR——告诉我该怎么做](#6)

## 调查旧代码

在我的[上一篇文章](https://dev.to/indy_singh_uk/its-not-your-code-vol-i-17n0)中，我们报道了如何发现并解决在 AWS S3 上使用`PutObject`的固定成本。NET 客户端；还提到了我们在图像上传到 AWS S3 之前对其进行转换——这是一个每天至少执行 400，000 次的过程。考虑到这一点，我决定探索整个代码路径是值得的。像往常一样，你可以在这里找到所有关于[的代码，我们将在测试中使用](https://github.com/indy-singh/OptimiseImageProcessing)[这张图片](https://commons.wikimedia.org/wiki/File:Pizigani_1367_Chart_1MB.jpg)。让我们看看现有的实现。在我们转换图像之前，我们需要得到它:-

```
private static byte[] GetImageFromUrl(string url)
{
    byte[] data;
    var request = (HttpWebRequest)WebRequest.Create(url);
    request.Timeout = 10000;
    request.ReadWriteTimeout = 10000;

    using (var response = request.GetResponse())
    using (var responseStream = response.GetResponseStream())
    using (var memoryStream = new MemoryStream())
    {
        int count;
        do
        {
            var buf = new byte[1024];
            count = responseStream.Read(buf, 0, 1024);
            memoryStream.Write(buf, 0, count);
        } while (responseStream.CanRead && count > 0);

        data = memoryStream.ToArray();
    }

    return data;
} 
```

Enter fullscreen mode Exit fullscreen mode

你看到那个`.ToArray()`了吗？它是*邪恶的*。我手头没有统计数据，但我知道一个事实，我见过大于 4 兆字节的图像通过这个代码路径。这不是一个好兆头——在[实现](https://referencesource.microsoft.com/#mscorlib/system/io/memorystream.cs,550)时的快速峰值向我们展示了原因:-

```
public virtual byte[] ToArray()
{
    BCLDebug.Perf(_exposable, "MemoryStream::GetBuffer will let you avoid a copy.");
    byte[] copy = new byte[_length - _origin];
    Buffer.InternalBlockCopy(_buffer, _origin, copy, 0, _length - _origin);
    return copy;
} 
```

Enter fullscreen mode Exit fullscreen mode

LOH 阈值是`85,000`字节这意味着任何大于阈值的图像将直接进入 LOH——这是一个问题，我在我的[上一篇文章](https://dev.to/indy_singh_uk/its-not-your-code-vol-i-17n0#2)中解释了为什么这是一个问题。在这一点上，好奇心战胜了我，我真的想知道进入这项服务的图像的大小。

该服务每天处理成千上万的图像，我需要的只是一个快速的样本和输入数据的大小和形状的想法。我们有一项工作，每天 14:00 运行，从我们的一个客户那里获取最新图像。

手动点击他们的服务返回要处理的`14,630`图像。旋转一个快速控制台应用程序来执行一个`HEAD`请求并获取`Content-Length`头:-

```
Total Images: 14,630
Images Above LOH Threshold: 14,276
Average Image Size: 253,818 bytes
Largest Image Size: 693,842 bytes
Smallest Image Size: 10,370 bytes
Standard Deviation of Sizes: 101,184 
```

Enter fullscreen mode Exit fullscreen mode

哎呀，这是一个巨大的标准差，97.58%的图像都在 LOH 阈值(`85,000`字节)以上。现在我们知道了图像大小的分布，我们可以继续查看当前实现的其余部分:-

```
public void Transform(string url)
{
    var bytes = GetImageFromUrl(url);

    if (CanCreateImageFrom(bytes))
    {
        using (var stream = new MemoryStream(bytes))
        using (var originalImage = Image.FromStream(stream))
        using (var scaledImage = ImageHelper.Scale(originalImage, 320, 240))
        using (var graphics = Graphics.FromImage(scaledImage))
        {
            ImageHelper.TransformImage(graphics, scaledImage, originalImage);

            // upload scaledImage to AWS S3 in production, in the test harness write to disk
            using (var fileStream = File.Create(@"..\..\v1.jpg"))
            {
                scaledImage.Save(fileStream, ImageFormat.Jpeg);
            }
        }
    }
}

private static bool CanCreateImageFrom(byte[] bytes)
{
    try
    {
        using (var stream = new MemoryStream(bytes))
        {
            Image.FromStream(stream);
        }
    }
    catch (ArgumentException)
    {
        return false;
    }

    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

我怀疑这两个人在附近游荡，但多特雷斯可以证实或否认我的怀疑。使用样本图像运行`V1`一百次:-

|  | 第五颅神经的眼支 |
| --- | --- |
| 耗时(毫秒) | Ten thousand two hundred and ninety-seven |
| 已分配(kb) | Eight hundred and fifty-one thousand eight hundred and ninety-four |
| 峰值工作集(kb) | Ninety-six thousand two hundred and seventy-six |
| 第 0 代集合 | One hundred and eighty-four |
| 第一代系列 | One hundred and one |
| 第二代系列 | One hundred and one |
|  | - |
| dotTrace 总内存(MB) | Nine hundred and one |
| 多迹线 SOH (MB) | Four hundred and ten |
| dotTrace LOH (MB) | Four hundred and ninety-one |

使用 dotTrace，我们可以看到最大的成本在哪里

[![image-01](../Images/818897ae7b0a88b6726aaebf0ec74e15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3fMii8Mg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ulo5bhw2oltjs2aorio.jpg)

现在我们已经建立了一个基线——让我们开始吧！

## 流媒体正在胜出

如果我们内联`GetImageFromUrl`并移除`CanCreateImageFrom`——这是不需要的，因为我们在代码路径的前面检查了`Content-Type`，我们可以直接对传入流进行操作。

```
public class ImageTransformerV2 : IImageTransformer
{
    public void Transform(string url)
    {
        var request = WebRequest.CreateHttp(url);
        request.Timeout = 10000;
        request.ReadWriteTimeout = 10000;

        using (var response = request.GetResponse())
        using (var responseStream = response.GetResponseStream())
        using (var originalImage = Image.FromStream(responseStream))
        using (var scaledImage = ImageHelper.Scale(originalImage, 320, 240))
        using (var graphics = Graphics.FromImage(scaledImage))
        {
            ImageHelper.TransformImage(graphics, scaledImage, originalImage);

            // upload scaledImage to AWS S3 in production, in the test harness write to disk
            using (var fileStream = File.Create(@"..\..\v2.jpg"))
            {
                scaledImage.Save(fileStream, ImageFormat.Jpeg);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`V2`的统计数据:-

|  | 第五颅神经的眼支 | V2 | % |
| --- | --- | --- | --- |
| 耗时(毫秒) | Ten thousand two hundred and ninety-seven | Seven thousand eight hundred and forty-four | -23.82% |
| 已分配(kb) | Eight hundred and fifty-one thousand eight hundred and ninety-four | Five hundred and twenty-seven thousand two hundred and forty-six | -38.10% |
| 峰值工作集(kb) | Ninety-six thousand two hundred and seventy-six | Sixty-nine thousand four hundred and thirty-six | -27.87% |
| 第 0 代集合 | One hundred and eighty-four | One hundred | -45.65% |
| 第一代系列 | One hundred and one | One hundred | -00.99% |
| 第二代系列 | One hundred and one | One hundred | -00.99% |
|  | - |  |  |
| dotTrace 总内存(MB) | Nine hundred and one | Five hundred and fifty | -38.95% |
| 多迹线 SOH (MB) | Four hundred and ten | One hundred and sixty-two | -60.48% |
| dotTrace LOH (MB) | Four hundred and ninety-one | Three hundred and eighty-eight | -20.97% |

太棒了，一些小的调整和所有的指标都下降了。

## 汇集你的溪流

再次使用 dotTrace，我们可以看到下一个最大的成本:-

[![image-02](../Images/f3e632fd42ea54670495cd5a4633c346.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WYju81T6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzqur2yor8fs9ms0yo5q.jpg)

在`GPStream`的构造函数内部发生了一些事情，让我们付出了沉重的代价。幸运的是，dotTrace 可以向我们展示反编译后的源代码，这样我们就不用去查阅参考资料了:-

```
internal GPStream(Stream stream)
{
    if (!stream.CanSeek)
    {
        byte[] buffer = new byte[256];
        int offset = 0;
        int num;

        do
        {
            if (buffer.Length < offset + 256)
            {
                byte[] numArray = new byte[buffer.Length * 2];
                Array.Copy((Array) buffer, (Array) numArray, buffer.Length);
                buffer = numArray;
            }
            num = stream.Read(buffer, offset, 256);
            offset += num;
        } while (num != 0);

        this.dataStream = (Stream) new MemoryStream(buffer);
    }
    else
        this.dataStream = stream;
} 
```

Enter fullscreen mode Exit fullscreen mode

你知道，当你大声问自己一个问题，你可以马上回答它？

> “什么？！我不能在传入的 HTTP 流上搜索吗？”

不，你不能。仔细想想，这完全有道理，因为传入的`Stream`是不可查找的`GPStream`必须复制它。

好的，第一件事——我们能把成本从*框架*代码转移到*我们的*代码吗？它并不漂亮，但类似这样的作品:-

```
public void Transform(string url)
{
    var request = WebRequest.CreateHttp(url);
    request.Timeout = 10000;
    request.ReadWriteTimeout = 10000;

    var memoryStream = new MemoryStream();

    using (var response = request.GetResponse())
    using (var responseStream = response.GetResponseStream())
    {
        responseStream.CopyTo(memoryStream);
    }

    using (var originalImage = Image.FromStream(memoryStream))
    using (var scaledImage = ImageHelper.Scale(originalImage, 320, 240))
    using (var graphics = Graphics.FromImage(scaledImage))
    {
        ImageHelper.TransformImage(graphics, scaledImage, originalImage);

        // upload scaledImage to AWS S3 in production, in the test harness write to disk

        using (var fileStream = File.Create(@"..\..\v2.jpg"))
        {
            scaledImage.Save(fileStream, ImageFormat.Jpeg);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

dotTrace 向我们展示了我们确实已经将成本从框架代码转移到了我们的代码中:-

[![image-04](../Images/f460ce8b2de0df1096df16f4834a3d1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o5uvnaW6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwf9vije0zwdhota4rds.jpg)

现在我们*可以*利用我们的老朋友`System.Buffers`租一辆`byte[]`然后把它交给`MemoryStream`的建造者。这是可行的，除了两件事。首先，`Content-Length`是[不保证](https://docs.microsoft.com/en-us/dotnet/api/system.net.httpwebresponse.contentlength?view=netframework-4.7.2#remarks)被置位。其次，几周前我在逛[。当我遇到`Microsoft.IO.RecyclableMemoryStream`时，我觉得这正是我在这里需要的。如果你想了解更多关于`RecyclableMemoryStream`的信息，本·沃特森有一个](https://github.com/datastax/csharp-driver/blob/d8bb178b06c4c075fdeebae44edff8972d6a5a76/src/Cassandra/TcpSocket.cs#L437)[很棒的帖子](http://www.philosophicalgeek.com/2015/02/06/announcing-microsoft-io-recycablememorystream/)关于它是什么以及它的各种用例。直接跳到`V3` :-

```
public class ImageTransformerV3 : IImageTransformer
{
    private readonly RecyclableMemoryStreamManager _streamManager;

    public ImageTransformerV3()
    {
        _streamManager = new RecyclableMemoryStreamManager();
    }

    public void Transform(string url)
    {
        var request = WebRequest.CreateHttp(url);
        request.Timeout = 10000;
        request.ReadWriteTimeout = 10000;
        request.AllowReadStreamBuffering = false;
        request.AllowWriteStreamBuffering = false;

        MemoryStream borrowedStream;

        using (var response = request.GetResponse())
        {
            if (response.ContentLength == -1) // Means that content length is NOT sent back by the third party server
            {
                borrowedStream = _streamManager.GetStream(url); // then we let the stream manager had this 
            }
            else
            {
                borrowedStream = _streamManager.GetStream(url, (int)response.ContentLength); // otherwise we borrow a stream with the exact size
            }

            int bufferSize;

            if (response.ContentLength == -1 || response.ContentLength > 81920)
            {
                bufferSize = 81920;
            }
            else
            {
                bufferSize = (int) response.ContentLength;
            }

            // close the http response stream asap, we only need the contents, we don't need to keep it open
            using (var responseStream = response.GetResponseStream())
            {
                responseStream.CopyTo(borrowedStream, bufferSize);
            }
        }

        using (borrowedStream)
        using (var originalImage = Image.FromStream(borrowedStream))
        using (var scaledImage = ImageHelper.Scale(originalImage, 320, 240))
        using (var graphics = Graphics.FromImage(scaledImage))
        {
            ImageHelper.TransformImage(graphics, scaledImage, originalImage);
            // upload scaledImage to AWS S3 in production, in the test harness write to disk

            using (var fileStream = File.Create(@"..\..\v3.jpg"))
            {
                scaledImage.Save(fileStream, ImageFormat.Jpeg);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢`RecyclableMemoryStream`的一点是，它是`MemoryStream`的替代产品。从`System.Buffers`开始的`ArrayPool`要求你先`Rent`，再`Return`。而`RecyclableMemoryStream`在实现`IDisposable`时为您处理一切。反正够我佩服`RecyclableMemoryStream`；`V3`统计数据:-

|  | 第五颅神经的眼支 | V2 | V3 | % (V3 对 V1) |
| --- | --- | --- | --- | --- |
| 耗时(毫秒) | Ten thousand two hundred and ninety-seven | Seven thousand eight hundred and forty-four | Seven thousand six hundred and eighty-eight | -25.33% |
| 已分配(kb) | Eight hundred and fifty-one thousand eight hundred and ninety-four | Five hundred and twenty-seven thousand two hundred and forty-six | One hundred and twenty-five thousand seven hundred and thirty-nine | -85.24% |
| 峰值工作集(kb) | Ninety-six thousand two hundred and seventy-six | Sixty-nine thousand four hundred and thirty-six | Seventy-one thousand one hundred and forty | -26.10% |
| 第 0 代集合 | One hundred and eighty-four | One hundred | Twenty-nine | -84.23% |
| 第一代系列 | One hundred and one | One hundred | Two | -98.01% |
| 第二代系列 | One hundred and one | One hundred | one | -99.00% |
| - |  |  |  |  |
| dotTrace 总内存(MB) | Nine hundred and one | Five hundred and fifty | One hundred and fifty-two | -83.12% |
| 多迹线 SOH (MB) | Four hundred and ten | One hundred and sixty-two | One hundred and fifty | -63.41% |
| dotTrace LOH (MB) | Four hundred and ninety-one | Three hundred and eighty-eight | one point six | -99.67% |

这是一个*难以置信的*对`V1`的改进。这是`V3`在 dotTrace 中的样子:-

[![image-05](../Images/ccd6621c4a1344ed57f9ff8030a6c772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u-f5Zx_U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w6nyssdrikkdfw61ot8b.jpg)

## 再见系统。画画，你不会被错过

我对`System.Drawing`的了解非常粗略，但是一个下午的阅读让我得出结论:如果你能避免使用`System.Drawing`，那么你会过得更好。在寻找《T2》的替代品时，我看到了奥马尔·沙欣写的这篇文章。嗯，我从来没有考虑过超载。花两秒钟来试试这个，所以我们不妨；禁用`useEmbeddedColorManagement`和关闭`validateImageData`的 v3 统计数据如下

|  | 第五颅神经的眼支 | V2 | V3 | % (V3 对 V1) |
| --- | --- | --- | --- | --- |
| 耗时(毫秒) | Ten thousand two hundred and ninety-seven | Seven thousand eight hundred and forty-four | Seven thousand five hundred and sixty-three | -26.55% |
| 已分配(kb) | Eight hundred and fifty-one thousand eight hundred and ninety-four | Five hundred and twenty-seven thousand two hundred and forty-six | One hundred and twenty-eight thousand one hundred and sixty-six | -84.95% |
| 峰值工作集(kb) | Ninety-six thousand two hundred and seventy-six | Sixty-nine thousand four hundred and thirty-six | Fifty-three thousand six hundred and eighty-eight | -44.23% |
| 第 0 代集合 | One hundred and eighty-four | One hundred | Thirty | -83.69% |
| 第一代系列 | One hundred and one | One hundred | Two | -98.01% |
| 第二代系列 | One hundred and one | One hundred | one | -99.00% |
| - |  |  |  |  |
| dotTrace 总内存(MB) | Nine hundred and one | Five hundred and fifty | One hundred and fifty-four | -82.90% |
| 多迹线 SOH (MB) | Four hundred and ten | One hundred and sixty-two | One hundred and fifty-three | -62.68% |
| dotTrace LOH (MB) | Four hundred and ninety-one | Three hundred and eighty-eight | one point six | -99.67% |

在某些地区略有增加，但`Peak Working Set`明显下降——太好了！Bertrand Le Roy 最近的一篇关于[的文章](https://blogs.msdn.microsoft.com/dotnet/2017/01/19/net-core-image-processing/)介绍了`System.Drawing`的各种替代方案。令人欣慰的是，底部有一个漂亮的小图表，显示了所用时间范围内的性能。

根据那篇文章 [PhotoSauce。MagicScaler](https://www.nuget.org/packages/PhotoSauce.MagicScaler) 相当神奇——警告这个库只适用于 Windows。我想知道它到底有多神奇？旋转`V4`向上:-

```
public class ImageTransformerV4 : IImageTransformer
{
    public void Transform(string url)
    {
        // truncated for brevity 

        MagicImageProcessor.EnableSimd = false;
        MagicImageProcessor.EnablePlanarPipeline = true;

        using (borrowedStream)
        {
            // upload scaledImage to AWS S3 in production, in the test harness write to disk

            using (var fileStream = File.Create(@"..\..\v4.jpg"))
            {
                MagicImageProcessor.ProcessImage(borrowedStream, fileStream, new ProcessImageSettings()
                {
                    Width = 320,
                    Height = 240,
                    ResizeMode = CropScaleMode.Max,
                    SaveFormat = FileFormat.Jpeg,
                    JpegQuality = 70,
                    HybridMode = HybridScaleMode.Turbo
                });
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

统计数据是

|  | 第五颅神经的眼支 | V2 | V3 | V4 | % (V4 对 V1) |
| --- | --- | --- | --- | --- | --- |
| 耗时(毫秒) | Ten thousand two hundred and ninety-seven | Seven thousand eight hundred and forty-four | Seven thousand five hundred and sixty-three | One thousand six hundred and seventy-two | -83.76% |
| 已分配(kb) | Eight hundred and fifty-one thousand eight hundred and ninety-four | Five hundred and twenty-seven thousand two hundred and forty-six | One hundred and twenty-eight thousand one hundred and sixty-six | One hundred and thirty-five thousand eight hundred and seventy-six | -84.05% |
| 峰值工作集(kb) | Ninety-six thousand two hundred and seventy-six | Sixty-nine thousand four hundred and thirty-six | Fifty-three thousand six hundred and eighty-eight | Thirty-five thousand five hundred and ninety-six | -63.02% |
| 第 0 代集合 | One hundred and eighty-four | One hundred | Thirty | Thirty-two | -82.60% |
| 第一代系列 | One hundred and one | One hundred | Two | Two | -98.01% |
| 第二代系列 | One hundred and one | One hundred | one | one | -99.00% |
| - |  |  |  |  |  |
| dotTrace 总内存(MB) | Nine hundred and one | Five hundred and fifty | One hundred and fifty-four | One hundred and sixty-five | -81.68% |
| 多迹线 SOH (MB) | Four hundred and ten | One hundred and sixty-two | One hundred and fifty-three | One hundred and sixty-two | -60.48% |
| dotTrace LOH (MB) | Four hundred and ninety-one | Three hundred and eighty-eight | one point six | one point six | -99.67% |

时间的疯狂下降和健康的下降！

那*就是*魔法。

## 为了透明起见

无意双关——就在这篇文章即将发表之前，我看到了这条有趣的推文

> ![JimBobSquarePants profile image](../Images/42dce12617f7c9b952730ec88f82533d.png)JimBobSquarePants@ James _ m _ south![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)awwww Yeaaaaaaah😎[twitter.com/1Marcos2Cobena…](https://t.co/E4wzeQA3MC)2018 年 6 月 29 日上午 09:43马科斯·科贝尼亚·莫里安(好样的！) @1Marcos2Cobena出于好奇，我们@WaveEngineTeam 发布了我们对@ SixLabors 的 ImageSharp 与 native approaches 在一系列平台上的对比统计数据 https://t.co/4sN0yZnG2t # WaveEngine[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1012632551486205952)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1012632551486205952)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1012632551486205952)

我自然很感兴趣，除了花费的时间、峰值工作集和 LOH 分配显著增加之外，旋转`V5`给了我与`V4`相似的结果。在他们的 [Gitter](https://gitter.im/ImageSharp/General?at=5b3635af7da8cd7c8c786c53) 上的一次对话后，我了解到他们是:-

1.  仍然在改进他们的 JPEG 解码器，这是相关的，因为我们也处理其他图像格式
2.  意识到他们的[调整大小算法](https://github.com/SixLabors/ImageSharp/issues/642)并不尽如人意——这是相关的，因为转换的主要部分是调整大小
3.  很高兴成为这篇文章的主角(这说明了很多)

## TLDR -告诉我该怎么做

如果您的图像转换过程主要是调整大小，并且您托管在 Windows 上，那么考虑将`PhotoSauce.MagicScaler`与`Microsoft.IO.RecyclableMemoryStream`一起使用，您会看到各种性能相关指标降低了 60%到 99 %!

找我 [Twitter](https://twitter.com/indy_singh_uk) ， [LinkedIn](https://uk.linkedin.com/in/indy-singh-uk) ，或者 [GitHub](https://github.com/indy-singh) 。