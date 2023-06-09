# 基于 Kotlin 协同例程的 KafkaProducer 扩展

> 原文：<https://dev.to/viniciusccarvalho/kotlin-coroutine-based-kafkaproducer-extension-3fc2>

# 摆脱 KafkaProducer 中的回调地狱

Kotlin 的[协同程序](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md)提供了一种编写异步代码的好方法。它使得使用一个非常轻量级的模型来编写和组合异步计算变得很容易。

这篇文章不是关于协程是什么的，文档的链接有一个非常深入和易读的解释。相反，我提供了一个使用`KafkaProducer.send`方法的解决方案。

问题是`send()`利用了`Callback`策略，我们都知道地狱里有一个特殊的地方给那些使用回调的人。

幸运的是，Kotlin 协程提供了一个解决方案:`suspendCoroutine` function，它允许我们将回调转换为挂起函数调用。

接收器函数也是 Kotlin 语言的另一个优点。它允许我们用自定义函数来扩充常规类型。

我决定调用新函数`dispatch`而不是`send`,因为我发现当人们决定使用相同的名称扩展原始函数时会有点混乱，并且导入会变得有点混乱。

所以你需要写的扩展函数很简单:

```
suspend inline fun <reified K : Any, reified V : Any> KafkaProducer<K, V>.dispatch(record: ProducerRecord<K, V>) =
    suspendCoroutine<RecordMetadata> { continuation ->
        val callback = Callback { metadata, exception ->
            if (metadata == null) {
                continuation.resumeWithException(exception!!)
            } else {
                continuation.resume(metadata)
            }
        }
        this.send(record, callback)
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以从常规的`KafkaProducer`实例:
中使用它

```
val props = Properties()
props["bootstrap.servers"] = "localhost:9092"
props["key.serializer"] = StringSerializer::class.java
props["value.serializer"] = JacksonSerializer::class.java
val kafkaProducer = KafkaProducer<String, SensorReading>(props)
async {
    kafkaProducer.dispatch(ProducerRecord("sample", SensorReading("Bedroom", 72.0, false)))
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，您只能在协程的边界内调用`suspend`函数，因此需要`async`，同样可以使用`launch`或`runBlocking`进行测试。

编码快乐！