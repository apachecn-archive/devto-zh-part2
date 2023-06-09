# 开源故事:从缓存到通用存储

> 原文：<https://dev.to/onmyway133/open-source-stories-from-cachable-to-generic-storage-in-cache-59od>

[![](img/af0276158bba9a6ec0e27be6ae0cf1cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5m4IsQgM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AX6nuMWx4FffMbdY1.jpg)

我们做[开源](https://cocoapods.org/owners/6020)已经有一段时间了，你可能已经见过我们在 [GitHub](https://github.com/hyperoslo/) 上的一些作品或者看过一些[我们的](https://medium.com/hyperoslo/getting-paid-to-do-open-source-26a97a852738) [故事](https://medium.com/hyperoslo/hitting-the-sweet-spot-of-inspiration-637d387bc629)。我们不想重新发明轮子，但我们的工作流程需要很多组件，或者需要为我们正在构建的应用程序定制的东西。所以我们建立了很多框架和应用。当我们在生产应用中使用它们时，我们认为与世界分享它们可能是个好主意。这是一个双赢的局面，因为我们回馈了社区，同时得到了很多反馈和建议。作为一个小型的 iOS 团队，全职做客户端项目，同时试图获得一点空闲时间来开发开源是非常具有挑战性的。

开源就是构建抽象概念。通过分离职责和制作可重用的框架，我们学习了 Swift 的大部分内容，并掌握了一些关于我们正在使用的 API 的漂亮的细节。但是我们从来没有讲过我们是如何做事的。所以会有一系列我们开源的故事，详细介绍我们工作背后的技术方面只要开源体验。

首先，我们来谈谈 [Cache](https://github.com/hyperoslo/Cache) ，一个持久化对象的框架。在这里，我们学习如何发展 API 以支持 Swift 语言和 iOS、tvOS 平台的新功能，同时确保它们足够灵活和可维护。

[![](img/40f043fad1b71f3fe44a00f764b25891.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aMdIlJuf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AGqpjqi4MQmidyfVw.png)

## 缓存

> Cache 在这方面并不自称是独一无二的，但它并不是另一个给你一种神的力量的怪物库。它除了缓存什么也不做，但它做得很好。它提供了一个良好的公共 API，具有开箱即用的实现和极大的定制可能性

iOS 平台中的缓存有许多解决方案，如 SQLite、CoreData 或 Realm 或其他第三方库。我们想从缓存中得到的是一种简单的方式来存储一些 JSON 数据到磁盘，到期管理和一个我们熟悉的 API。

从用户的角度来看，我希望使用一个键可靠地保存和加载一个对象，并且能够同步或异步地完成这项工作。下面是我们要实现的 API。

## 可缓存

在[的第一个版本](https://github.com/hyperoslo/Cache/tree/3.0.0)中，我们引入了 Cachable 协议，因为事实上对象应该被序列化和解序列化为磁盘存储的数据。我们还让大多数原始类型符合 Cachable，这样用户就不必自己去做了。对于内存存储，我们使用 NSCache，因此对象将按原样保存。

> 缓存基于前端和后端缓存的概念。对前端缓存的请求应该消耗较少的时间和内存(这里默认使用 NSCache)。前端缓存和后端缓存的区别在于，后端缓存用于比应用程序生命周期更长的内容。它更像是一种存储用户信息的便捷方式，这些信息应该在应用程序启动时保持不变。磁盘缓存是这里最可靠的选择

HybridCache 具有带可缓存类型约束的泛型函数，因此它对于所有可缓存的一致性都是类型安全的

即使对于自定义类型和 UIImage

### 异步

默认情况下，缓存是同步的，这意味着所有方法都被阻塞。要以异步方式访问缓存，有一种方便的异步方式可以实现异步混合缓存。所有对象共享相同的 CacheManager，所以所有对象都保持不变，它们只是不同的接口。

### JSON

因为我们大部分时间都在处理 JSON，所以有一个 JSON enum，它封装了顶级 JSON 对象或 JSON 数组，使用 JSON 序列化来转换为数据，以实现可缓存的一致性

## Swift 4 中的可编码

Swift 4 最重要的特性之一是可编码的。符合 Codable 的类型可以在 JSON 之间进行映射。虽然有 JSONSerialization，但我们所需要关心的是使我们的类型符合 Codable，并确保我们的模型属性匹配 JSON 数据中的那些键。只需声明一个模型，从 JSON 中解码并保存到缓存中，没有任何麻烦，这多酷啊？所以在 [Cache 4.0](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Storage/Storage.swift) 中，我们重构了公共 API 以更好地支持 Codable。

鉴于有传言称 NSCache 将被重命名为 Cache，为了避免我们的 struct Cache 窃取 Cache [命名空间](https://github.com/hyperoslo/Cache/issues/37)，我们将我们的 Cache 类重命名为 Storage。有了更好的磁盘和内存存储封装配置对象，声明自己的存储就很容易了。

### 责任链

> 缓存是基于责任链模式构建的，其中有许多处理对象，每个处理对象知道如何完成一个任务，并委托给下一个任务。但这只是实现细节。你只需要知道存储，它保存和加载可编码的对象。

存储是在头脑中设计的[责任链模式](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)，其中每个存储充当一个处理对象。我们只处理存储，但有一个引擎盖下的链

存储->同步存储-> TypeWrapperStorage ->混合存储->磁盘存储和内存存储

> 每个处理对象包含定义它可以处理的命令对象类型的逻辑；其余的被传递给链中的下一个处理对象

存储处理基于传入的配置构建内部存储。SyncStorage 负责管理异步访问的串行队列。混合存储协调内存存储和磁盘存储，…

### 什么是 TypeWrapper？

像 Int、String、Bool、…这样的原语类型符合 Codable，所以调用 storage.save("a string "，forKey: "myKey ")完全没问题，因为编译器很高兴。但是当我们在幕后使用 JSONEncoder 和 JSONDecoder 时，简单地使用原始类型可能会导致运行时异常，如“顶级 T 编码为数字 JSON 片段”或“期望解码 T，但却找到了字典。”，这就是[原始存储](https://github.com/hyperoslo/Cache/pull/134/files#diff-d021bec863df93c151d30b6b1add3192L12)的原因

在这里，我们需要捕捉这些错误，并在出现错误时使用 PrimitiveWrapper，这样我们就始终拥有一个顶级对象，可以序列化 JSON 数据或从 JSON 数据序列化。

其中 PrimitiveWrapper 是具有可编码约束的简单泛型结构

后来我想，如果我们总是执行包装，代码会更少，这导致了我的[Add TypeWrapperStorage](https://github.com/hyperoslo/Cache/pull/134)pull 请求。这样代码很容易推理，但是开销是存在的。

### 存储软件协议

为了使所有存储易于“链接”，它们都符合[存储软件](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Storage/StorageAware.swift)协议，该协议定义了一组存储必须支持的最小功能。

最棒的是，我们可以利用 Swift 中的协议扩展来为 StorageAware conformers 提供默认实施。从条目信息中，我们可以推断出对象以及它是否存在。

所有的函数都有可编码的约束，所以我们有一个非常类型安全的体验。

### 同步和异步

默认情况下，存储是同步的。您可能已经注意到，所有的同步函数都标记了错误类型为 [StorageError](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Library/StorageError.swift) 的抛出。我们设计了 try catch 用于同步，而[结果](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Library/Result.swift)用于异步。对于异步，我们不能做 try catch，因为结果将在以后交付。所以我们使用完成闭包来异步调用关于结果的调用者。

所有异步存储都符合[async StorageAware](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Storage/AsyncStorageAware.swift)协议，就像我们对 Storage ware 一样。为了保证没有读和写同时发生，我们使用串行 DispatchQueue 来按顺序调度操作

由于我们希望在同一存储上支持同步和异步操作，我们最初在 SyncStorage 和 AsyncStorage 之间共享一个串行队列，因此无论执行多少操作，它们都是安全有序的。但是，由于我们还对 SyncStorage 使用 serialQueue.sync 来获得阻塞行为，对 AsyncStorage 使用 serialQueue.async，这可能会导致[死锁](https://stackoverflow.com/questions/18297118/deadlock-with-dispatch-sync)！！！因此，最终我们对 SyncStorage 和 AsyncStorage 使用了不同的 DispatchQueue，这样可以在死锁和临界区访问冲突的机会之间进行权衡，如果用户互换调用 sync 和 async 的话。

### 但图像不符合编码

UIImage 和 n image[与](https://stackoverflow.com/questions/46197785/how-to-conform-uiimage-to-codable)不一致。因为我们设计的 API 专门支持 Codable，所以处理图像是很棘手的。简单地使 UIImage 符合 Codable 是行不通的，这样做也没有意义。

本质上，对于图像，用户应该将它们作为数据保存到磁盘，而不是将它们的文件 URL 保存在存储器中。但是为了支持可编码的统一体验，我们引入了 [ImageWrapper](https://github.com/hyperoslo/Cache/blob/4.0.0/Source/Shared/Library/ImageWrapper.swift) 。如果像 UIImage 这样的现有类型不能符合 Codable，那么包装器可以

然后，您所要做的就是将 UIImage 包装在 ImageWrapper 中，并获得与 Codable 相同的 API 支持

但是这个漂亮的 API 对[开销](https://github.com/hyperoslo/Cache/issues/174)有一个警告。这可能没什么大不了的，但是对于像[假想](https://github.com/hyperoslo/Imaginary)那样依赖缓存的图书馆来说，存储和获取大量图像可能是个大问题。这是对象在磁盘上的样子。

```
{“object”:{“image”:”\/9j\/4AAQSkZJRgABAQAASABIAAD\/4QBYRXhpZ… 
```

顶级 JSON 对象和图像到字符串的转换导致了开销。理想情况下，图像应该只保存为数据

## 四处工作

### 怎么样呢？

支持 UIImage 的一种方法是移除可编码约束，并使用如下所示的任何约束

这不编译，至于 Codable 工作，类型需要在编译时知道。就是因为协议不能符合自身，读[用 JSON 编码器编码一个变量，用 Codable 作为类型](https://stackoverflow.com/questions/45053060/using-json-encoder-to-encode-a-variable-with-codable-as-type)和[协议不符合自身？](https://stackoverflow.com/questions/33112559/protocol-doesnt-conform-to-itself)了解更多详细解释。

所以我们不采用这种方法。

### 数据可转换怎么样？

UIImage 和 Codable 的区别在于它们如何被转换成数据，我们需要数据可转换对象用于磁盘存储。所以我们需要封装这个需求，从 DataConvertible 协议开始，让 UIImage 和 Codable 符合它

然而，这并不容易。我们不能让现有的协议代码符合我们的协议。

这种方法不可行，我们不同意。

### 数据生产者怎么样？

Codable 的协议扩展根本不起作用。让我们回到使用类 DataProducer 的对象组合，它有通用的可编码约束，同时存储 UIImage 或可编码。因此，当要求它产生数据时，它会检查它是否有 Codable 或 UIImage。

这种方法是可行的，并且编译良好。对于磁盘存储，我们调用 toData 来产生数据，对于内存存储，我们只需将内部对象设置为 NSCache。但是将 DataProducer 指定为包装器的需求通常不会让用户满意。我们需要一种不同的方法。

## 通用存储器

从 [Cache 5.0](https://github.com/hyperoslo/Cache/tree/5.0.0) 开始，我们解决了这些开销和纯 UIImage 支持，同时允许缓存灵活且易于定制。

### 变换类型

支持 UIImage 和 Codable 的一个更好的方法是拥有一个通用的存储，我们可以在其中转换类型。这样，如果我们愿意，我们可以转换以支持其他自定义类型。

这种存储方式是非常类型安全的，一次可以保存和加载相同类型的对象。但是存储是可转换的，底层存储机制保持不变，只是公共 API 支持不同的类型。这适用于用户希望将 Codable 和 UIImage 保存到同一个存储中的情况。但是，我们仍然建议对每种类型使用不同的存储。

### 存储软件

所有存储仍然符合存储软件，因此我们在[存储软件](https://github.com/hyperoslo/Cache/blob/master/Source/Shared/Storage/StorageAware.swift)协议扩展中有一些不错的默认实现。请注意，由于存储是通用的，我们的存储软件现在已经关联了类型 T 来反映存储中的通用值类型。

由于我们不能用变量来定义带有通用约束的协议，我们可以像以前一样自由地链接所有存储。现在，在缓存中我们有了一个固定的依赖关系，这意味着 SyncStorage 现在明确指定了 HybridStorage。但是，我们确实将所有存储公开，因此您可以按照自己的方式组合它们。但是默认的存储组合在大多数情况下应该是好的。

### 变压器

当用户指定存储类型时，他们还需要指定一个[转换器](https://github.com/hyperoslo/Cache/blob/master/Source/Shared/Library/Transformer.swift)。它是一个数据结构，包含两个函数 fromData 和 toData。这是 DiskStorage 所需要的，因为我们必须支持一种方法，使泛型类型可以进行数据转换。

由于 Codable、Data 和 UIImage 是我们保存和加载到存储器中的最常见的格式，我们提供了默认的 [TransformerFactory](https://github.com/hyperoslo/Cache/blob/master/Source/Shared/Library/TransformerFactory.swift)

### 变换函数

要将存储转换为新的类型，我们只需将存储中的所有内部对象移动到新的存储中，它们都是引用类型，因此存在开销。我们还需要为新类型指定转换器。每个存储器都有转换功能

您绝对应该看看关于存储转型有多强大的[测试](https://github.com/hyperoslo/Cache/blob/master/Tests/iOS/Tests/Storage/StorageSupportTests.swift)。每当存储被转换时，它被约束到一个新的类型，因此所有的操作都是类型安全的，但是所有的对象都被保存到相同的位置。

## 何去何从

新的通用存储 API 是类型安全且灵活的。它还减少了所有的工作区开销。我们已经在我们的图像提取器 [Imaginary](https://github.com/hyperoslo/Imaginary) 中使用它来提高性能。

唯一的“不变”就是“变”。苹果平台和 Swift 编程语言的发展速度比你想象的要快，我们的框架利用了所有的新特性，这很好。希望您发现缓存和我们的故事在这个重构之旅有用。

最后但同样重要的是，感谢所有促成缓存的[贡献者](https://github.com/hyperoslo/Cache/graphs/contributors)。你不仅仅是令人敬畏的❤️

下一个故事再见。

原创故事[https://medium . com/hyperoslo/open-source-stories-from-cachable-to-generic-storage-in-cache-418 d9 a 230d 51](https://medium.com/hyperoslo/open-source-stories-from-cachable-to-generic-storage-in-cache-418d9a230d51)