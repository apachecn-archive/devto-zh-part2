# Swift 结果类型。他们没用吗？

> 原文：<https://dev.to/hugh_jeremy/swift-result-types-are-they-useless-2hgp>

新面孔，业余，易受影响:斯威夫特不是我的主要果酱。当开始写 Amatino Swift 时，我渴望得到最佳实践和好的模式。Amatino Swift 涉及到对 [Amatino API](https://amatino.io) 的大量异步 HTTP 请求。

异步编程需要在响应接收点进行分支。也就是说，异步操作可能产生成功状态或失败状态。结果类型是一种广泛支持的模式，作为处理这种分叉的模式。

我想就 Swift 中是否应该使用结果类型展开一场辩论。经过一番思考，我觉得它们毫无用处。我建议，我们最好鼓励新手利用现有的 Swift 功能，而不是学习和构建结果类型模式。

出于讨论的目的，让我们假设我们的代码包含两个函数，每个函数处理一个分叉状态:

```
func handleSuccess(data: Data) {
    // Do stuff with data
}

func handleFailure(error: Error) {
    // Do stuff with error
} 
```

Enter fullscreen mode Exit fullscreen mode

在这些函数中，我们可以实现独立于分叉模式的代码。例如，我们可以`case-switch` on Error type，以便向用户呈现有意义的消息。

现在谈谈分歧本身。一个简单的模式可能是:

```
// This is bad code. Do not copy it!
func asynchronousCallback(error: Error?, data: Data?) -> Void {
    if (error != nil) {
        handleFailure(error!)
        return
    }
    handleSuccess(data!)
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有无数的问题。我们没有超过`data`的类型安全。我们不控制程序员错误产生的状态`data`和`error`都是`nil`的情况。太丑了。更多。

结果类型建议通过定义如下类型来改进这种模式:

```
enum Result<Value> {
    case success(Value)
    case failure(Error)
} 
```

Enter fullscreen mode Exit fullscreen mode

可以这样使用:

```
func asynchronousCallback(result: Result<Data>) {
    switch result {
    case .success(let data):
        handleSuccess(data)
    case .failure(let error):
        handleError(error)
    }
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

这种模式给`error`和`data`都引入了类型安全。我认为，与使用内置的 Swift 功能相比，这样做的代价太大了。现在，每个异步分支都需要一个`switch-case`语句和一个结果类型的使用。

将结果类型模式与使用 Swift `guard`语句的模式进行比较:

```
func asynchronousCallback(error: Error?, data: Data?) {
    guard let data = data else { handleError(error ?? TrainWreck()) }; return
    handleSuccess(data)
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们在`error`和`data`上有类型安全。我们处理过一个案例，一个程序员没有使用零合并操作符`??`提供一个`Error`。我们只用了不到 80 个字符的两行代码就完成了这一切。合适的错误类型可以在别处定义为:

```
struct TrainWreck: Error { let description = "No error provided" } 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，通过`guard`语句进行分支比结果类型有几个优点:

*   **简洁**。处理异步回调的函数在处理类型安全的结果之前只需要实现一个单行模式。
*   **认知负荷降低**。使用用`guard`模式编写的库的开发人员不需要了解库的结果类型如何表现。
*   **澄明**。在我看来，`guard`语句比`case-switch`语句更具可读性。这当然是主观的。

你怎么想呢?我不是 Swift 专家。我错过了什么明显的东西吗？为什么选择使用结果类型而不是`guard`语句？

*封面图片——一只蜜蜂在[索恩利农场](https://thornleighfarm.com)采集薰衣草花粉*