# 通过编写测试了解 Go:同步异步流程

> 原文：<https://dev.to/quii/learn-go-by-writing-tests-synchronising-asynchronous-processes-1bo7>

这是 WIP 项目[的第 8 篇文章，通过编写测试](https://github.com/quii/learn-go-with-tests)来学习 Go，其目的是熟悉 Go 并学习 TDD 的相关技术

*   [第一篇文章让你了解了 TDD](https://dev.to/quii/learn-go-by-writing-tests--m63)
*   [第二篇文章讨论了数组和切片](https://dev.to/quii/learn-go-by-writing-tests-arrays-and-slices-ahm)
*   [第三篇文章讲授结构、方法、接口&表驱动测试](https://dev.to/quii/learn-go-by-writing-tests-structs-methods-interfaces--table-driven-tests-1p01)
*   [第四篇文章展示了如何出错以及为什么指针有用](https://dev.to/quii/learn-go-by-writing-tests-pointers-and-errors-2kp6)
*   [第五篇文章向您展示了如何以及为什么要进行依赖注入](https://dev.to/quii/learn-go-by-writing-tests-dependency-injection-n7j)
*   [第六篇文章介绍了并发性](https://dev.to/gypsydave5/learn-go-by-writing-tests-concurrency--2ebk)
*   [第七篇文章展示了如何以及为什么嘲笑](https://dev.to/quii/learn-go-by-writing-tests-mocking-fl4)

本章是关于用`select`同步异步过程

# 选择

**[你可以在这里找到本章的所有代码](https://github.com/quii/learn-go-with-tests/tree/master/select)**

您被要求创建一个名为`WebsiteRacer`的函数，该函数接受两个 URL，并通过 HTTP GET 访问它们并返回先返回的 URL 来“比赛”它们。如果在 10 秒内没有一个返回，那么它应该返回一个`error`

为此，我们将使用

*   `net/http`进行 HTTP 调用。
*   来帮助我们测试它们。
*   高楼大厦。
*   同步进程。

## 先写测试

让我们从一些简单的事情开始。

```
func TestRacer(t *testing.T) {
    slowURL := "http://www.facebook.com"
    fastURL := "http://www.quii.co.uk"

    want := fastURL
    got := Racer(slowURL, fastURL)

    if got != want{
        t.Errorf("got '%s', want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们知道这并不完美，也有问题，但它会让我们继续前进。重要的是不要太执着于第一次就把事情做得完美。

## 试运行测试

`./racer_test.go:14:9: undefined: Racer`

## 编写运行测试所需的最少代码，并检查失败的测试输出

```
func Racer(a, b string) (winner string) {
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

`racer_test.go:25: got '', want 'http://www.quii.co.uk'`

## 写足够的代码让它通过

```
func Racer(a, b string) (winner string) {
    startA := time.Now()
    http.Get(a)
    aDuration := time.Since(startA)

    startB := time.Now()
    http.Get(b)
    bDuration := time.Since(startB)

    if aDuration < bDuration {
        return a
    }

    return b
} 
```

Enter fullscreen mode Exit fullscreen mode

对于每个 url:

1.  我们在尝试获取`URL`之前使用`time.Now()`进行记录
2.  然后我们使用 [`http.Get`](https://golang.org/pkg/net/http/#Client.Get) 来尝试获取`URL`的内容。这个函数返回一个 [`http.Response`](https://golang.org/pkg/net/http/#Response) 和一个`error`但是到目前为止我们对这些值不感兴趣
3.  `time.Since`取开始时间并返回差值的`time.Duration`。

一旦我们这样做了，我们简单地比较持续时间，看看哪一个是最快的。

### 问题

这可能会也可能不会让你通过测试。问题是我们正在接触真实的网站来测试我们自己的逻辑。

测试使用 HTTP 的代码是如此普遍，以至于 Go 在标准库中有工具来帮助你测试它。

在模仿和依赖注入章节中，我们介绍了我们不希望依赖外部服务来测试我们的代码，因为它们可能是

*   慢的
*   薄片的
*   无法测试边缘案例

在标准库中有一个包 [`net/http/httptest`](https://golang.org/pkg/net/http/httptest/) ，在这里你可以很容易地创建一个模拟的 HTTP 服务器。

让我们将测试改为使用模拟，这样我们就有可靠的服务器来测试我们可以控制的服务器。

```
func TestRacer(t *testing.T) {

    slowServer := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        time.Sleep(20 * time.Millisecond)
        w.WriteHeader(http.StatusOK)
    }))

    fastServer := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusOK)
    }))

    slowURL := slowServer.URL
    fastURL := fastServer.URL

    want := fastURL
    got := Racer(slowURL, fastURL)

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }

    slowServer.Close()
    fastServer.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

语法可能看起来有点复杂，但请慢慢来。

`httptest.NewServer`接受我们通过*匿名函数*发送的`http.HandlerFunc`。

`http.HandlerFunc`是一个看起来像这样的类型:`type HandlerFunc func(ResponseWriter, *Request)`

它真正要说的是，它需要一个带有一个`ResponseWriter`和一个`Request`的函数，这对于 HTTP 服务器来说并不奇怪

事实证明这里真的没有额外的魔力，**这也是你如何在 Go** 中编写*真正的* HTTP 服务器。唯一的区别是我们将它包装在一个`httptest.NewServer`中，这使得它更容易用于测试，因为它会找到一个打开的端口来监听，然后当您完成测试时可以关闭它。

在我们的两个服务器中，当我们收到一个请求要让慢的服务器比另一个慢时，我们让慢的服务器有一个短的`time.Sleep`。然后两个服务器都写一个带有`w.WriteHeader(http.StatusOK)`的`OK`响应给调用者。

如果您重新运行测试，它现在肯定会通过，应该会更快。玩这些睡眠故意破考。

## 重构

我们在产品代码和测试代码中都有一些重复。

```
func Racer(a, b string) (winner string) {
    aDuration := measureResponseTime(a)
    bDuration := measureResponseTime(b)

    if aDuration < bDuration {
        return a
    }

    return b
}

func measureResponseTime(url string) time.Duration {
    start := time.Now()
    http.Get(url)
    return time.Since(start)
} 
```

Enter fullscreen mode Exit fullscreen mode

这种干涸使得我们的`Racer`代码更容易阅读。

```
func TestRacer(t *testing.T) {

    slowServer := makeDelayedServer(20 * time.Millisecond)
    fastServer := makeDelayedServer(0 * time.Millisecond)

    defer slowServer.Close()
    defer fastServer.Close()

    slowURL := slowServer.URL
    fastURL := fastServer.URL

    want := fastURL
    got := Racer(slowURL, fastURL)

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }
}

func makeDelayedServer(delay time.Duration) *httptest.Server {
    return httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        time.Sleep(delay)
        w.WriteHeader(http.StatusOK)
    }))
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建的假服务器重构到一个名为`makeDelayedServer`的函数中，将一些不感兴趣的代码移出测试，减少重复。

#### `defer`

通过在函数调用前加上前缀`defer`，它将在包含函数的末尾调用函数*。*

有时您需要清理资源，比如关闭一个文件，或者在我们的例子中关闭一个服务器，这样它就不会继续监听端口。

您希望它在函数的末尾执行，但是为了将来代码的读者的利益，请将指令放在创建服务器的位置附近。

我们的重构是一个改进，是一个合理的解决方案，考虑到目前为止所涉及的 Go 特性，但是我们可以使解决方案更简单。

### 同步流程

*   当 Go 在并发性方面表现出色时，为什么我们要一个接一个地测试网站的速度？我们应该能够同时检查两者
*   我们并不真正关心请求的确切响应时间，我们只想知道哪个请求先返回。

为了做到这一点，我们将引入一个名为`select`的新构造，它可以帮助我们非常容易和清晰地同步进程。

```
func Racer(a, b string) (winner string) {
    select {
    case <-ping(a):
        return a
    case <-ping(b):
        return b
    }
}

func ping(url string) chan bool {
    ch := make(chan bool)
    go func() {
        http.Get(url)
        ch <- true
    }()
    return ch
} 
```

Enter fullscreen mode Exit fullscreen mode

#### `ping`

我们已经定义了一个函数`ping`，它创建一个`chan bool`并返回它。

在我们的例子中，我们并不真的*关心*在通道中发送了什么类型，*我们只是想发送一个信号*说我们已经完成了，所以布尔值是好的。

在同一个函数中，我们启动一个 goroutine，一旦我们完成`http.Get(url)`,它将向该通道发送一个信号

#### `select`

如果您还记得并发性一章，您可以使用`myVar := <-ch`等待值被发送到通道。这是一个*阻塞*调用，因为你在等待一个值。

`select`让你做的是等待*多个*频道。第一个发送值的人“获胜”,执行`case`下面的代码。

我们在我们的`select`中使用`ping`来为我们的每个`URL`建立两个通道。无论哪一个先写入其通道，都将在`select`中执行其代码，这导致其`URL`被返回(并成为获胜者)。

经过这些改变后，我们代码背后的意图变得非常清晰，实现也变得更加简单。

### 超时

我们最后的要求是如果`Racer`超过 10 秒就返回一个错误。

## 先写测试

```
t.Run("returns an error if a server doesn't respond within 10s", func(t *testing.T) {
    serverA := makeDelayedServer(11 * time.Second)
    serverB := makeDelayedServer(12 * time.Second)

    defer serverA.Close()
    defer serverB.Close()

    _, err := Racer(serverA.URL, serverB.URL)

    if err == nil {
        t.Error("expected an error but didn't get one")
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们已经让我们的测试服务器花了超过 10 秒的时间来测试这个场景，我们期望`Racer`现在返回两个值，获胜的 URL(在这个测试中我们用`_`忽略了它)和一个`error`。

## 试运行测试

`./racer_test.go:37:10: assignment mismatch: 2 variables but 1 values`

## 编写运行测试所需的最少代码，并检查失败的测试输出

```
func Racer(a, b string) (winner string, error error) {
    select {
    case <-ping(a):
        return a, nil
    case <-ping(b):
        return b, nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

更改`Racer`的签名以返回获胜者和一个`error`。返回`nil`寻找我们的幸福案例。

编译器会抱怨你的*第一次测试*只寻找一个值，所以把那行改成`got, _ := Racer(slowURL, fastURL)`，知道我们应该检查我们的*没有*在我们高兴的场景中得到一个错误。

如果 11 秒后你现在运行它，它将失败

```
-------- FAIL: TestRacer (12.00s)
    --- FAIL: TestRacer/returns_an_error_if_a_server_doesn't_respond_within_10s (12.00s)
        racer_test.go:40: expected an error but didn't get one 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func Racer(a, b string) (winner string, error error) {
    select {
    case <-ping(a):
        return a, nil
    case <-ping(b):
        return b, nil
    case <-time.After(10 * time.Second):
        return "", fmt.Errorf("timed out waiting for %s and %s", a, b)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`select`时`time.After`是一个非常方便的功能。虽然在我们的例子中没有发生这种情况，但是如果您正在监听的通道从不返回值，您可能会编写永远阻塞的代码。`time.After`返回一个`chan`(像`ping`)并在你定义的时间后发送一个信号。

对我们来说，这是完美的；如果`a`或`b`设法返回，他们就赢了，但是如果我们到达 10 秒，那么我们的`time.After`将发送一个信号，我们将返回一个`error`

### 慢速测试

我们的问题是这个测试需要 10 秒钟来运行。对于这样一个简单的逻辑来说，这感觉并不好。

我们能做的是使超时可配置，因此在我们的测试中，我们可以有一个非常短的超时，然后当代码在现实世界中使用时，它可以设置为 10 秒。

```
func Racer(a, b string, timeout time.Duration) (winner string, error error) {
    select {
    case <-ping(a):
        return a, nil
    case <-ping(b):
        return b, nil
    case <-time.After(timeout):
        return "", fmt.Errorf("timed out waiting for %s and %s", a, b)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试现在不会编译，因为我们没有提供超时

在匆忙将这个默认值添加到我们的两个测试中之前，让我们*听听它们*。

*   我们在乎“快乐”测试中的超时吗？
*   超时的要求很明确

有了这些知识，让我们做一点重构来同情我们的测试和代码的用户

```
var tenSecondTimeout = 10 * time.Second

func Racer(a, b string) (winner string, error error) {
    return ConfigurableRacer(a, b, tenSecondTimeout)
}

func ConfigurableRacer(a, b string, timeout time.Duration) (winner string, error error) {
    select {
    case <-ping(a):
        return a, nil
    case <-ping(b):
        return b, nil
    case <-time.After(timeout):
        return "", fmt.Errorf("timed out waiting for %s and %s", a, b)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的用户和我们的第一次测试可以使用`Racer`(它在引擎盖下使用`ConfigurableRacer`)，我们的 sad path 测试可以使用`ConfigurableRacer`。

```
func TestRacer(t *testing.T) {

    t.Run("compares speeds of servers, returning the url of the fastest one", func(t *testing.T) {
        slowServer := makeDelayedServer(20 * time.Millisecond)
        fastServer := makeDelayedServer(0 * time.Millisecond)

        defer slowServer.Close()
        defer fastServer.Close()

        slowURL := slowServer.URL
        fastURL := fastServer.URL

        want := fastURL
        got, err := Racer(slowURL, fastURL)

        if err != nil {
            t.Fatalf("did not expect an error but got one %v", err)
        }

        if got != want {
            t.Errorf("got '%s', want '%s'", got, want)
        }
    })

    t.Run("returns an error if a server doesn't respond within 10s", func(t *testing.T) {
        server := makeDelayedServer(25 * time.Millisecond)

        defer server.Close()

        _, err := ConfigurableRacer(server.URL, server.URL, 20*time.Millisecond)

        if err == nil {
            t.Error("expected an error but didn't get one")
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我在第一个测试中添加了一个最终检查，以验证我们没有得到`error`

## 包装完毕

### `select`

*   帮助您在多个频道等待。
*   有时你会想在你的一个`cases`中包含`time.After`来防止你的系统永远阻塞。

### `httptest`

*   创建测试服务器的便捷方式，这样您就可以进行可靠和可控的测试。
*   使用与“真实的”`net/http`服务器相同的接口，这是一致的，并且您需要学习的内容更少