# 不要返回关联数组！

> 原文：<https://dev.to/aleksikauppila/dont-return-associative-arrays-28il>

当我写客户端代码时，我讨厌处理关联数组。数组的问题是没有上下文。没有什么专门的知识。它们通常只是以不方便的格式打包的数据。最糟糕的是，他们把我和一个特定的实现捆绑在一起。

现在，我并不是说你不应该返回具有统一数据的数组。返回某种类型对象的数组是可以的。尽管您仍然不能通过您 API 保证它将只包含某些类型的数据。

数组可能作为某个类的数据的内部表示，但这是它应该保持内部的方式。

我说的是那些讨厌的以硬编码字符串为键的多维数组。

考虑一下这个:

```
class AcmeFileSender implements FileSender
{
    public function sendFiles(FileCollection $files): array
    {
        $sent = 0;
        $errors = [];
        foreach ($files as $file) {
            try {
                $this->send($file);
                $sent++;
            } catch (SenderException $exception) {
                $errors[] = $exception->getMessage();
            }
        }
        return [
            "sent" => $sent,
            "failed" => count($errors),
            "errors" => $errors
        ];
    }

    public function send(File $file): void {//...}
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来没那么糟吧？它只是一个关联数组中的三个键值。

这种情况下的问题是，数组不能向我们保证它的结构。这意味着当我们编写客户端代码时，我们依赖于`sendFiles()`的实现。我们必须确切地知道这些键是什么，以及这些键后面是什么样的数据。

在 OOP 中，我们不应该关心其他类的实现细节。我们只关心接口。

最近我碰到了一些可能更严重的事情

```
public function processStuff($stuff): array
{
    //...
    return [$someArray, $someObject];
}

//... in client code
list($someArray, $someObject) = $this->processor->processStuff($stuff); 
```

Enter fullscreen mode Exit fullscreen mode

现在，从私有方法返回这种令人厌恶的东西是一回事。但是当你开始像这样设计你的公共 API 时，所有的希望都失去了。这几乎是一个死亡的礼物，你正在处理一个过程化的代码库。在过程代码库中，一切都是数据，可以(也将会)被操纵。

在上面的例子中，问题是缺乏上下文。把`someArray`和`someObject`接到一起是什么意思？为什么他们一起回来了？很明显，`processStuff()`的实现知道如何使用它。因此，与其说它是真正的设计，不如说它是用一些客户端代码粘在一起的。客户端代码与`processStuff()`的实现紧密耦合。

在许多情况下，对于如何定义责任存在一些困惑。代码执行一些操作，并向客户端返回一些成功的指示。客户端和服务器之间没有太多的信任。客户对服务器的工作质量有所怀疑。

我们能做些什么来使第一个例子更好呢？值对象。使用值对象会更有表现力。

```
interface SenderReport
{
    public function sent(): int;
    public function failures(): int;
    public function errors(): Iterator;
    public function print(): string;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将更新 FileSender 及其实现。

```
class AcmeFileSender implements FileSender
{
    //...
    public function sendFiles(FileCollection $files): SenderReport
    {
        //...
        return new FileSenderReport($sent, count($errors), $errors);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以依靠`SenderReport`的接口，而不是痛苦地意识到`FileSender::sendFiles()`的实现。现在我们有了背景。此外，我们可以自由地改变`FileSender`的实现。

-

不要从公共 API 返回关联数组。评估你是否真的需要返回值。如果必须的话，返回一些你和你的代码的用户可以依赖的接口。