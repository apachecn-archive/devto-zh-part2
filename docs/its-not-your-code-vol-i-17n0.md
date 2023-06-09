# AWS S3。NET 客户端高内存使用率

> 原文：<https://dev.to/indy_singh_uk/its-not-your-code-vol-i-17n0>

减少自动气象站 S3。98%的净客户 LOH 分配

## 目录

1.  [问题发现](#1)
2.  [为什么这是个问题？](#2)
3.  [介绍最佳魔术数字-81920](#3)
4.  [闲手](#4)
5.  [还有一件事](#5)
6.  TLDR -给我好东西
7.  [脚注](#7)

## 问题发现

我们在 [Codeweavers](https://codeweavers.net/) 做的事情之一就是帮助人们找到他们的下一辆车。这通常涉及到消费者看到他们正在购买的汽车-我的意思是，你会在没有看到它的样子的情况下购买一辆汽车吗？承担这一责任的应用程序是*在分配数量、花费在 GC 上的时间以及像饼干怪兽一样吃 RAM 方面最糟糕的罪犯...饼干。*

 *我们时常喜欢从生产环境中获取该应用程序的内存转储。我们已经做了足够多的次数，我们已经将最常见的诊断步骤自动化，并将它们捆绑到一个名为 ADA(自动转储分析)的小工具中。如果你感兴趣，你可以在这里找到工具和本文中谈到的所有代码[这里](https://github.com/indy-singh/ItsNotYourCode-AWS-S3)。

我们运行的分析器之一是转储在大对象堆(LOH)上找到的所有`byte[]`数组。在对我们的 8gb 内存转储运行该分析器后，我们发现了几百个长度为`131,096`或`131,186`的`byte[]`数组。这太奇怪了。打开[记事本++](https://notepad-plus-plus.org/) 中的一些文件，呈现给我们的只是大量随机字符。

暂时抛开[科学方法](https://en.wikipedia.org/wiki/Scientific_method)，我决定将所有被转储的`byte[]`数组重新命名为`*.jpg`——嘿，很快一些文件开始显示缩略图了！仔细观察，大约 50%的文件是图像。另外 50%根本无法作为图像打开。在 Notepad++中打开一些非图像文件，可以看到在文件的开头都有一行类似的文字:-

```
0;chunk-signature=48ebf1394fcc452801d4ccebf0598177c7b31876e3fbcb7f6156213f931b261d 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这开始有点说得通了。长度为`131,096`的`byte[]`数组是纯图像。不是图像的`byte[]`数组的长度为`131,186`，在其余内容之前有一条`chunk-signature`线。我猜这个签名是内容的 SHA256 散列。

在我们继续之前，有必要了解一下这个应用程序在图像处理方面有多忙。我们所有的图像处理都是通过 AWS [SNS](https://aws.amazon.com/sns/) 和 [SQS](https://aws.amazon.com/sqs/) 分布在农场各处。使用[云观察指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)我们可以很容易地看到:-

[![6](img/f7c1b090906d23cad4f6ab08003146bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mysU3o8F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4nmtxgxmi40fvb23wii2.png)

好吧，所以*相当*忙。值得注意的是，在*执行任何*以性能为中心的工作之前，总是要确定代码被击中的频率和当前的成本。如果代码路径具有高成本(例如，花费 20 秒)，但是每天仅命中一次，则不值得调查。然而，如果同一个代码路径被大量访问(例如，一天一百万次)，那么它绝对值得调查。

在这一点上，我心中有两个罪犯。我们已经建立了问题中的应用程序做了大量的图像处理。但是有一些移动部件和两种启动图像处理的方式

1.  图像被推给我们
2.  我们从 SFTP 提取图像

之后我们转换图像，然后上传到 S3 的自动气象站。在这个阶段，我倾向于 SFTP，因为它可能需要验证从服务器接收到的每个数据块。但是跟随我的直觉让我徒劳无功，所以我无视我的直觉，把`chunk-signature`输入谷歌，然后点击回车。谷歌指出 AWS S3 是罪魁祸首。但那只是理论，我们需要证明。

```
public class UploaderV1 : IUploader
{
    private readonly AWSCredentials _credentials;
    private readonly RegionEndpoint _region;
    private readonly string _bucketName;

    public UploaderV1(AWSCredentials credentials, RegionEndpoint region, string bucketName)
    {
        _credentials = credentials;
        _region = region;
        _bucketName = bucketName;
    }

    public void Upload(Stream objectToUpload, int iteration)
    {
        try
        {
            using (var client = new AmazonS3Client(_credentials, _region))
            {
                var putObjectRequest = new PutObjectRequest
                {
                    BucketName = _bucketName,
                    Timeout = TimeSpan.FromSeconds(5),
                    InputStream = objectToUpload,
                    Key = "v1/" + iteration + ".png",
                    CannedACL = S3CannedACL.PublicRead,
                    StorageClass = S3StorageClass.Standard,
                    ServerSideEncryptionMethod = ServerSideEncryptionMethod.None,
                };

                client.PutObject(putObjectRequest);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine(exception);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将[相同的图像](https://a0.awsstatic.com/maimg/logos/aws_logo_smile_1200x630.png)上传十次，并使用[点迹](https://www.jetbrains.com/profiler/)查看杂合性缺失，我们会看到一个有趣的模式

[![7](img/08dc0638fdbddf608bd586c8d2efce49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9WQZ5rBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fduybqnqias4aj9b96d8.png)

看起来每次我们在 AWS 上给 S3 打电话。NET 客户端有一个固定成本`0.3 MB`。这是一个问题，因为这意味着你每次使用`PutObject`，每次上传都要支付`0.3 MB`的高额费用。只是为了确认一下。如果我们把上传的次数从十次增加到一百次会怎么样？

[![32](img/4e654525f4bd7bc7dc5f94edc8283cdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mcMlgQ5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/72kjmu21billvx46b7ig.png)

是的，我们可以肯定地说，每次调用`PutObject`都会对`0.3 MB`进行代价高昂的分配。更进一步，使用 [ProcDump](https://docs.microsoft.com/en-us/sysinternals/downloads/procdump) :-
转储进程

```
procdump64.exe -ma -64 AWS-S3.exe 
```

Enter fullscreen mode Exit fullscreen mode

通过 ADA 运行转储文件，我们看到两组`byte[]`数组具有完全相同的特征；50%具有长度`131,096`，另外 50%具有长度`131,186`。重命名时一半文件是图像，一半文件有`chunk-signature`起跑线。在这一点上，我们可以肯定的是 AWS S3。NET 客户端将`byte[]`数组直接分配到 LOH 上；而那个*就是*一个问题。

## 为什么会有问题？

LOH 是一个被*收集*但从未*压缩*的内存区域——尽管截至。NET v4.5.1 [压缩现在是可能的](https://docs.microsoft.com/en-gb/dotnet/api/system.runtime.gclargeobjectheapcompactionmode)——警告 LOH 的压缩是昂贵的；大约每兆字节 2.3 毫秒。一个很好的经验法则是，短命对象应该*永远不要*进入 LOH。

等于或大于 85，000 字节的对象直接进入 LOH。LOH 的运作与记忆的其他区域非常不同。内存的其他区域会被定期收集和压缩，这意味着在垃圾收集器运行后，您可以在末尾添加新的对象。而 LOH 试图将新分配的对象放入废弃对象被丢弃后留下的空闲空间中。如果新分配的对象与可用空间的大小完全相同或更小，这种方法就可以很好地工作。如果找不到一个空间，那么 LOH 必须增长以容纳那个对象。

把它想象成一个书架会有所帮助；在内存的其他区域，不再使用的书被简单地扔掉，剩下的书被推到一起，任何新书都放在书架的末端。

在 LOH 中，这是不可能的，相反，书籍(对象)被扔掉，并且曾经在该空间中的页数(字节)被记录，并且下一次一本书被分配到该书架(LOH)时，它试图找到可以容纳那么多页(字节)的空白空间。如果书架不能容纳新分配的书(对象),那么书架必须扩展以容纳新的书(对象)。

垃圾收集器将从 LOH 中收集死对象，同时将新对象分配给 LOH。这可能导致在长时间运行的应用程序的生命周期中，LOH 大小增长到几千兆字节(因为新的对象不适合现有的空白空间)，但实际上只包含几个活动的对象。这就是所谓的 LOH 碎片化。在这种情况下，我们*非常*幸运，因为进入 LOH 的`byte[]`数组有两种大小；`131,186`和`131,096`。这意味着，随着旧对象的死亡和被收集，新分配的对象的大小正好可以放入空闲空间。

好吧，回到有趣的事情上。

## 介绍最佳魔术数字— 81，920

多亏了 dotTrace，我们能够准确地确定是什么导致了 LOH 片段化。它还向我们展示了每次调用`PutObject`的固定成本`0.3 MB`发生在`ChunkedUploadWrapperStream`的构造函数内部

[![29](img/812be127b18477468ab00580eef84f09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S-eIaCKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dxfexic4hzs0naxbmsun.JPG)

快速访问 aws-sdk-net 存储库中的文件[。显示创建了两个长度至少为`131,072` :-
的`byte[]`数组](https://github.com/aws/aws-sdk-net/blob/9814e245e0cd9c4435a17185e866813869d427da/sdk/src/Core/Amazon.Runtime/Internal/Util/ChunkedUploadWrapperStream.cs#L73)

```
public class ChunkedUploadWrapperStream : WrapperStream
{
    public static readonly int DefaultChunkSize = 128*1024;
    private byte[] _inputBuffer;
    private readonly byte[] _outputBuffer;

    // truncated for brevity

    internal ChunkedUploadWrapperStream(Stream stream, int wrappedStreamBufferSize, AWS4SigningResult headerSigningResult) : base(stream)
    {
        // truncated for brevity
        _inputBuffer = new byte[DefaultChunkSize];
        _outputBuffer = new byte[CalculateChunkHeaderLength(DefaultChunkSize)];
    }

    // truncated for brevity
} 
```

Enter fullscreen mode Exit fullscreen mode

这正是为什么这些`byte[]`数组被直接分配给 LOH，它们高于 LOH 阈值(`85,000`字节)。在这一点上，有几种可能的解决方案:-

1.  使用[系统。缓冲区](http://adamsitnik.com/Array-Pool/)从`byte[]`阵列池中租用两个`byte[]`阵列
2.  用[微软。IO.RecycableMemoryStream](http://www.philosophicalgeek.com/2015/02/06/announcing-microsoft-io-recycablememorystream/) ，并使用一个`Stream`池直接对传入流进行操作
3.  公开`DefaultChunkSize`以便 API 的消费者可以自己设置它
4.  将`DefaultChunkSize`降低到低于 LOH 阈值的数字(`85,000`字节)

第一个和第二个解决方案可能是最大的赢家，但是这将需要一个大的拉请求，并引入一个库维护者可能不希望有的依赖关系。第三个解决方案意味着库的消费者必须知道这个问题，并将其设置为一个合理的数字，以避免 LOH 分配。不，看起来第四个解决方案最有可能被接受，并且破坏现有功能的可能性最小。

我们所需要的是一个比`85,000`小的数字，通常像`84,000`这样的数字是非常合适的。然而，在发现这个问题的几个星期前，我在闲逛[参考资料来源](https://referencesource.microsoft.com/)(调查一个不同的问题)时，我偶然发现了[这个宝石](https://referencesource.microsoft.com/#mscorlib/system/io/stream.cs,53) :-

```
// We pick a value that is the largest multiple of 4096 that is still smaller than the large object heap threshold (85K).
// The CopyTo/CopyToAsync buffer is short-lived and is likely to be collected at Gen0, and it offers a significant
// improvement in Copy performance.
private const int _DefaultCopyBufferSize = 81920; 
```

Enter fullscreen mode Exit fullscreen mode

Windows 内存页面的大小为[4096 字节](https://blogs.msdn.microsoft.com/tims/2010/10/28/pdc10-mysteries-of-windows-memory-management-revealed-part-one/)，因此选择低于 LOH 阈值的倍数(`85,000`字节)是完全有意义的。到了分叉、分支的时间，[创建一个问题](https://github.com/aws/aws-sdk-net/issues/894)，并做出一个[拉动请求](https://github.com/aws/aws-sdk-net/pull/899)。

幸运的是，我们可以在当地做出改变 <sup>3</sup> ，看看有什么好处。通过`PutObject`上传同一张图片一百次的统计数据:-

| 版本 | 总内存分配 | 段开销 | 亨利袢 |
| --- | --- | --- | --- |
| 1(默认值 ChunkSize 为 131，072) | 59 兆字节 | 33md | 25 兆字节 |
| 1(默认值 ChunkSize 为 81，920) | 51 兆字节 | 51 兆字节 | 0.4 兆字节 |

## 闲散的手

在等待审查我的 pull 请求时，我决定浏览 AWS S3 文档，无意中发现了[预签名 URL](https://docs.aws.amazon.com/AmazonS3/latest/dev/PresignedUrlUploadObject.html)的概念。听起来很有趣！创建上传者的`V2`:-

```
public class UploaderV2 : IUploader
{
    private readonly AWSCredentials _credentials;
    private readonly RegionEndpoint _region;
    private readonly string _bucketName;

    public UploaderV2(AWSCredentials credentials, RegionEndpoint region, string bucketName)
    {
        _credentials = credentials;
        _region = region;
        _bucketName = bucketName;
    }

    public void Upload(Stream objectToUpload, int iteration)
    {
        try
        {
            using (var client = new AmazonS3Client(_credentials, _region))
            {
                var request = new GetPreSignedUrlRequest
                {
                    BucketName = _bucketName,
                    Verb = HttpVerb.PUT,
                    Expires = DateTime.Now.AddMinutes(5),
                    ServerSideEncryptionMethod = ServerSideEncryptionMethod.None,
                    Key = "v2/" + iteration + ".png",
                    Headers = { [HeaderKeys.XAmzAclHeader] = S3CannedACL.PublicRead.Value }
                };

                var preSignedUrl = client.GetPreSignedURL(request);
                var webRequest = WebRequest.CreateHttp(preSignedUrl);
                webRequest.Method = WebRequestMethods.Http.Put;
                webRequest.ContentType = "image/*";
                webRequest.AllowWriteStreamBuffering = false;
                webRequest.ContentLength = objectToUpload.Length;
                webRequest.Headers[HeaderKeys.XAmzAclHeader] = S3CannedACL.PublicRead.Value;

                using (var dataStream = webRequest.GetRequestStream())
                using (objectToUpload)
                {
                    objectToUpload.CopyTo(dataStream, objectToUpload.Length > 81920 ? 81920 : (int)objectToUpload.Length);
                }

                using (webRequest.GetResponse())
                {

                }
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine(exception);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们看到，当上传同一个文件一百次时，它有以下统计数据:-

| 版本 | 总内存分配 | 段开销 | 亨利袢 |
| --- | --- | --- | --- |
| 1(默认值 ChunkSize 为 131，072) | 59 兆字节 | 33md | 25 兆字节 |
| 1(默认值 ChunkSize 为 81，920) | 51 兆字节 | 51 兆字节 | 0.4 兆字节 |
| 2(使用预签名的 URL) | 17 兆字节 | 17 兆字节 | 0.4 兆字节 |

这真是太棒了，我们实际上所要做的就是阅读文档！不是这样的，你可以阅读一篇有很多有趣内容的总结文章。你在这里看到的工作发生在一周的时间里，在客户工作之间。

使用`GetPreSignedURL`有一个小缺点，如果`GetPreSignedUrlRequest`被修改而`WebRequest`没有被相应修改，那么 AWS 将返回 [HTTP 403 禁止](https://en.wikipedia.org/wiki/HTTP_403)(例如删除`WebRequest`上的`XAmzAclHeader`)。这是因为客户端哈希和服务器端哈希不再匹配。

## 还有一件事

感谢我的[上一篇文章](https://dev.to/indy_singh_uk/strings-are-evil-9f9)，我已经知道了什么是[书呆子狙击](https://xkcd.com/356/)——我对自己做了很多。在这个阶段，我对还有什么可以剪掉感到头晕目眩，我完全看着剩下的 LOH。同样，dotTrace 为我们指出了导致`0.4 MB`分配给 LOH 的代码路径的方向

[![31](img/1f97916270e2a621b6e97365aaf60150.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UPH4ZiiX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0p87n361mu9tucdx6gwo.JPG)

呀，那看起来相当严重。悄悄后退，尝试不同的策略；我们知道一个预先签名的网址看起来像这样:-

```
https://##bucket_name##.s3.##region_name##.amazonaws.com/##path##/##file_name##?X-Amz-Expires=300&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=##access_key##/20180613/##region_name##/s3/aws4_request&X-Amz-Date=20180613T233349Z&X-Amz-SignedHeaders=host;x-amz-acl&X-Amz-Signature=6bbcb0f802ad86022674e827d574b7a34a00ba76cd1411016c3581ba27fa5450 
```

Enter fullscreen mode Exit fullscreen mode

我们应该能够自己生成那个 URL，因为 AWS 已经非常友好地公布了他们的[签名过程](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html#signing-request-intro)。在这一点上，我将承认我已经准备好接受失败，只是让失败继续下去。我真的不觉得我将要写给*的大量代码可能*删除剩余的`0.4 MB`是值得的。

直到我发现[一个我想要的例子](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-examples-using-sdks.html#sig-v4-examples-using-sdk-dotnet)。对我来说需要的努力要少得多；`V3`出生于:-

| 版本 | 总内存分配 | 段开销 | 亨利袢 |
| --- | --- | --- | --- |
| 1(默认值 ChunkSize 为 131，072) | 59 兆字节 | 33md | 25 兆字节 |
| 1(默认值 ChunkSize 为 81，920) | 51 兆字节 | 51 兆字节 | 0.4 兆字节 |
| 2(使用预签名的 URL) | 17 兆字节 | 17 兆字节 | 0.4 兆字节 |
| 3(使用手动预签名的 URL) | 13 兆字节 | 13 兆字节 | 0 MB |

这只是一个实验，看看有什么是可能的，考虑到收益有多小，以及维护它需要多少代码，这不是我们在生产代码中实际使用的东西。预签名 URL 的发现是这里的主要胜利；-

| 版本 | 总内存分配 | 段开销 | 亨利袢 |
| --- | --- | --- | --- |
| 1(默认值 ChunkSize 为 131，072) | 59 兆字节 | 33md | 25 兆字节 |
| 2(使用预签名的 URL) | 17 兆字节 | 17 兆字节 | 0.4 兆字节 |
| 百分率 | -71.18% | -48.48% | -98.4% |

同时我的拉取请求已经被[合并](https://github.com/aws/aws-sdk-net/pull/899#event-1548404894)并在 [`AWSSDK.Core`](https://www.nuget.org/packages/AWSSDK.Core/) 的版本`3.3.21.19`中发布。时间表的快速概述:-

1.  2018-03-07 -在 aws-sdk-net 存储库上创建的问题
2.  2018-03-13 -拉请求已发送
3.  2018-03-29 -拉请求已合并
4.  2018-03-29 -新版`AWSSDK.Core`发布到 NuGet

我喜欢开源。

## TLDR -给我好东西

在 AWS S3 上，低于`3.3.21.19`的`AWSSDK.Core`版本导致了每次调用`PutObject`的`0.3 MB`的固定成本。NET 客户端。这在`3.3.21.19`及更高版本中得到了纠正。对于特别热的代码路径，值得探索在 AWS S3 上使用`GetPreSignedURL`。在我们的上下文和用例中，这减少了 98%的 LOH 分配。

找我 [Twitter](https://twitter.com/indy_singh_uk) ， [LinkedIn](https://uk.linkedin.com/in/indy-singh-uk) ，或者 [GitHub](https://github.com/indy-singh) 。

## 脚注

<sup>1</sup> 另一个原因*可能*是我仍然害怕风言风语。

<sup>2</sup> 话虽如此一场[最近的对话已经开始](https://github.com/aws/aws-sdk-net/issues/971)加以利用。净核心价值

<sup>3</sup> 确保在发布中构建，不像*某个*某人——好吧，是我*