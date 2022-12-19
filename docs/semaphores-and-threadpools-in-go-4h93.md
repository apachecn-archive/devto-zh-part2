# Go 中的信号量和线程池

> 原文：<https://dev.to/dnnrly/semaphores-and-threadpools-in-go-4h93>

# Go 中的信号量和线程池

今天我在想一件事。我必须对 API 进行大量的 REST 调用。第一个是返回匹配对象的查询，然后对每个结果进行另一个查询以获得更多细节。第一版一个接一个地发出请求，速度很慢。

让我们看一些代码来说明发生了什么。

```
package main

import (
    "log"
    "time"
)

func main() {
    start := time.Now()

    // Imagine this is the result of a search on a REST API
    results := []int{10, 15, 8, 3, 17, 20, 1, 6, 10, 9, 13, 19}

    for _, d := range results {
        // Imagine this is a long running operation, perhaps another
        // REST API call
        log.Printf("Waiting for %d seconds\n", d)
        time.Sleep(time.Second * time.Duration(d))
    }

    log.Printf("Total time taken: %s\n", time.Now().Sub(start))
} 
```

> 示例 1
> 
> 你可以在本页[这里](https://github.com/dnnrly/sempool/)看到这个示例代码以及所有其他代码。

等待一段时间，然后给出输出:

```
2018/10/03 23:31:19 Waiting for 10 seconds
2018/10/03 23:31:29 Waiting for 15 seconds
2018/10/03 23:31:44 Waiting for 8 seconds
2018/10/03 23:31:52 Waiting for 3 seconds
2018/10/03 23:31:55 Waiting for 17 seconds
2018/10/03 23:32:12 Waiting for 20 seconds
2018/10/03 23:32:32 Waiting for 1 seconds
2018/10/03 23:32:33 Waiting for 6 seconds
2018/10/03 23:32:39 Waiting for 10 seconds
2018/10/03 23:32:49 Waiting for 9 seconds
2018/10/03 23:32:58 Waiting for 13 seconds
2018/10/03 23:33:11 Waiting for 19 seconds
2018/10/03 23:33:30 Total time taken: 2m11.012685s 
```

超过 2 分钟？我们当然可以让它更快？

## 添加 goroutines

最快的方法是什么？

就是同时进行所有的 API 调用。让我们假设我们正在我们的例子中这样做，并使用 go 例程来并行化该工作。

```
for _, d := range results {
    go func() {
        // Imagine this is a long running operation, perhaps another
        // REST API call
        log.Printf("Waiting for %d seconds\n", d)
        time.Sleep(time.Second * time.Duration(d))
    } ()
} 
```

> 示例 2

让我们看看这给了我们什么:

```
2018/10/05 20:53:11 Waiting for 1 seconds
2018/10/05 20:53:11 Waiting for 17 seconds
2018/10/05 20:53:11 Waiting for 19 seconds
2018/10/05 20:53:11 Waiting for 19 seconds
2018/10/05 20:53:11 Waiting for 19 seconds
2018/10/05 20:53:11 Waiting for 19 seconds
2018/10/05 20:53:11 Total time taken: 571µs 
```

呃，这不对！这并不明显，但我认为正在发生的是，当不同的 goroutines 运行时，`d`的值被修改*，所以它们中的几个意外地获得了相同的值，这与每个 on 启动时的值不同。然后所有的 goroutines 结束，程序在`log`包把所有东西写到控制台之前退出。*

## 不要提前结束

我们需要一种方法来等待，直到所有线程都完成。为此，我们用一个 [`sync.WaitGroup`](https://golang.org/pkg/sync/#WaitGroup) 。这将允许我们安全地跟踪已经创建的 goroutines 的数量，然后等待它们完成。

```
var wg sync.WaitGroup

for _, d := range results {
    wg.Add(1)

    go func() {
        defer wg.Done()

        // Imagine this is a long running operation, perhaps another
        // REST API call
        log.Printf("Waiting for %d seconds\n", d)
        time.Sleep(time.Second * time.Duration(d))
    } ()
}

wg.Wait() 
```

> 示例 3

这次我们得到这个:

```
2018/10/05 21:43:57 Waiting for 6 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:43:57 Waiting for 19 seconds
2018/10/05 21:44:16 Total time taken: 19.003385s 
```

好吧，至少我们得到了正确的日志条目数，时间看起来也是正确的。

## 使用正确的数值跨越 goroutines

这个应该简单一点，我们只需要确保将数据传递给我们调用的 goroutine 函数。对吗？

```
for _, d := range results {
    wg.Add(1)

    go func(secs int) {
        defer wg.Done()

        // Imagine this is a long running operation, perhaps another
        // REST API call
        log.Printf("Start waiting for %d seconds\n", secs)
        time.Sleep(time.Second * time.Duration(secs))
        log.Printf("Finished waiting for %d seconds\n", secs)
    } (d)
} 
```

> 实例 4

看啊！我们按顺序运行它们，当它们完成时，它们就完成了！:)

```
2018/10/05 22:21:20 Start waiting for 19 seconds
2018/10/05 22:21:20 Start waiting for 20 seconds
2018/10/05 22:21:20 Start waiting for 3 seconds
2018/10/05 22:21:20 Start waiting for 17 seconds
2018/10/05 22:21:20 Start waiting for 10 seconds
2018/10/05 22:21:20 Start waiting for 1 seconds
2018/10/05 22:21:20 Start waiting for 8 seconds
2018/10/05 22:21:20 Start waiting for 6 seconds
2018/10/05 22:21:20 Start waiting for 15 seconds
2018/10/05 22:21:20 Start waiting for 9 seconds
2018/10/05 22:21:20 Start waiting for 13 seconds
2018/10/05 22:21:20 Start waiting for 10 seconds
2018/10/05 22:21:21 Finished waiting for 1 seconds
2018/10/05 22:21:23 Finished waiting for 3 seconds
2018/10/05 22:21:26 Finished waiting for 6 seconds
2018/10/05 22:21:28 Finished waiting for 8 seconds
2018/10/05 22:21:29 Finished waiting for 9 seconds
2018/10/05 22:21:30 Finished waiting for 10 seconds
2018/10/05 22:21:30 Finished waiting for 10 seconds
2018/10/05 22:21:33 Finished waiting for 13 seconds
2018/10/05 22:21:35 Finished waiting for 15 seconds
2018/10/05 22:21:37 Finished waiting for 17 seconds
2018/10/05 22:21:39 Finished waiting for 19 seconds
2018/10/05 22:21:40 Finished waiting for 20 seconds
2018/10/05 22:21:40 Total time taken: 20.003293s 
```

很好…但是…他们都同时跑。让我们假设您正在尝试调用一个有严重性能问题的微服务，因此同时调用所有这些服务可能会导致问题。我知道，我知道，一次打 12 个电话不算多，但让我们假装一下。好吧，我们说假装，我已经看到生产中的真实生活服务会因为许多不同的原因而挣扎。我不止一次地看到性能呈指数级下降——增加连接或请求的数量会使响应时间呈指数级增长。注意，如果你不小心的话，这很容易做到。

## 限制并发请求的数量

所以在这里，我们进入一些适当的计算机 y 的东西。理想情况下，我们会使用一个[线程池](https://en.wikipedia.org/wiki/Thread_pool)，这样在同一时间只能执行一定数量的操作。但是这是 Go，我们并没有真正使用线程。那么我们还能用什么呢？

这里我们需要的是保持一个计数，记录现在有多少 goroutines 处于活动状态，并阻止更多的 goroutines 运行，这样我们就不会超过这个神奇的数字。当我们没有足够的容量时，我们需要阻塞 goroutines。我们需要使用一个信号量！

有几种使用信号量的方法，但幸运的是我们使用的是 Go，所以我们可以利用通道给我们正确的行为，而不必显式地使用线程池。

为此，我们需要创建一个*缓冲通道*，这样一个通道就有一定数量的槽可以同时被填充。但是我们用什么填充它呢？有关系吗？其实并没有。你知道你可以传递一个空的`struct`到通道吗？这里有很多细节。

让我们看看这是如何工作的:

```
var wg sync.WaitGroup
sem := make(chan struct{}, 3)

for _, d := range results {
        wg.Add(1)

        go func(wait int) {
                defer func() {
                        wg.Done()

                        // Release the semaphore resource
                        <-sem
                }()

                // Aquire a single semaophore resource
                sem <- struct{}{}

                // Imagine this is a long running operation, perhaps another
                // REST API call
                log.Printf("Start waiting for %d seconds\n", wait)
                time.Sleep(time.Second * time.Duration(wait))
                log.Printf("Finished waiting for %d seconds\n", wait)
        }(d)
}

wg.Wait() 
```

那么这看起来像什么？

```
2018/10/06 00:12:09 Start waiting for 19 seconds
2018/10/06 00:12:09 Start waiting for 15 seconds
2018/10/06 00:12:09 Start waiting for 10 seconds
2018/10/06 00:12:19 Finished waiting for 10 seconds
2018/10/06 00:12:19 Start waiting for 3 seconds
2018/10/06 00:12:22 Finished waiting for 3 seconds
2018/10/06 00:12:22 Start waiting for 8 seconds
2018/10/06 00:12:24 Finished waiting for 15 seconds
2018/10/06 00:12:24 Start waiting for 17 seconds
2018/10/06 00:12:28 Finished waiting for 19 seconds
2018/10/06 00:12:28 Start waiting for 10 seconds
2018/10/06 00:12:30 Finished waiting for 8 seconds
2018/10/06 00:12:30 Start waiting for 6 seconds
2018/10/06 00:12:36 Finished waiting for 6 seconds
2018/10/06 00:12:36 Start waiting for 20 seconds
2018/10/06 00:12:38 Finished waiting for 10 seconds
2018/10/06 00:12:38 Start waiting for 9 seconds
2018/10/06 00:12:41 Finished waiting for 17 seconds
2018/10/06 00:12:41 Start waiting for 13 seconds
2018/10/06 00:12:47 Finished waiting for 9 seconds
2018/10/06 00:12:47 Start waiting for 1 seconds
2018/10/06 00:12:48 Finished waiting for 1 seconds
2018/10/06 00:12:54 Finished waiting for 13 seconds
2018/10/06 00:12:56 Finished waiting for 20 seconds
2018/10/06 00:12:56 Total time taken: 47.005534s 
```

> 实例 5

我们做到了！不可否认没有之前那么快，但是我们一次只做 3 件事，因此不会让我们假装脆弱的 API 完全过载。

## 将数据传递到下一步

现在，如果在我们调用这个 API 之后，我们一起处理结果，那不是很好吗？也许我们正试图聚集一些东西，或者使用单个的数据来描绘一幅美丽的图画。

让我们使用另一个渠道来传递这些响应。

```
var wg sync.WaitGroup
sem := make(chan struct{}, 3)
responses := make(chan int)

for _, d := range results {
        wg.Add(1)

        go func(wait int) {
                defer func() {
                        // Release the semaphore resource
                        <-sem
                        wg.Done()
                }()

                // Aquire a single semaophore resource
                sem <- struct{}{}

                // Imagine this is a long running operation, perhaps another
                // REST API call
                log.Printf("Start waiting for %d seconds\n", wait)
                time.Sleep(time.Second * time.Duration(wait))
                log.Printf("Finished waiting for %d seconds\n", wait)

                responses <- wait / 2
        }(d)
}

wg.Wait()

for r := range responses {
        log.Printf("Got result %d", r)
} 
```

> 实例 6

这给出了…

```
2018/10/06 00:37:04 Start waiting for 19 seconds
2018/10/06 00:37:04 Start waiting for 17 seconds
2018/10/06 00:37:04 Start waiting for 15 seconds
2018/10/06 00:37:19 Finished waiting for 15 seconds
2018/10/06 00:37:21 Finished waiting for 17 seconds
2018/10/06 00:37:23 Finished waiting for 19 seconds
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [semacquire]:
sync.runtime_Semacquire(0xc0000120a8)
        /usr/local/go/src/runtime/sema.go:56 +0x39
sync.(*WaitGroup).Wait(0xc0000120a0)
        /usr/local/go/src/sync/waitgroup.go:130 +0x64
main.main()
        /home/dnnrly/projects-host/sempool/example6/main.go:42 +0x184

goroutine 4 [chan send]:
main.main.func1(0xc00009e000, 0xc0000120a0, 0xc00009e060, 0xa)
        /home/dnnrly/projects-host/sempool/example6/main.go:30 +0x6d
created by main.main
        /home/dnnrly/projects-host/sempool/example6/main.go:22 +0x158 
```

> 我已经简化了这个日志输出，它有很多。

嗯，那显然不好！我们有一个[死锁](https://en.wikipedia.org/wiki/Deadlock)，看看显示的代码行，等待`sync.WaitGroup`结束并试图*获取*信号量是个问题。如果您自己运行这个示例，您可以看到它告诉我们阻塞了哪些行。

嗯，我第一次尝试没有成功。你，读者，去做几个小时别的事情，我来解决这个问题。

## 移动到互斥体

所以我在最后一段之后 8 小时写了这篇文章。不可否认，在那 8 个小时里，我还做了其他事情，但还是花了一些时间才弄明白。为了帮助我，我用日志创建了示例 6 的替代方案。如果你感兴趣的话，可以看一下[回购](https://github.com/dnnrly/sempool/)。

看起来问题是，如果我们使用通道来存储我们计算的“结果”,那么我们就有问题了。实际上，通过使用`make(chan int)`，我们正在创建另一个缓冲通道。但是这一次，因为没有槽，所以需要立即读取它，否则它会阻塞。事实就是如此。我们的工作程序 goroutine 立即阻塞，这意味着我们永远不会到达 go routine 的末尾，并且我们会死锁。

看起来我犯了一个经典的错误，试图使用通道来同步而不是交流。在这种特殊情况下，我们真正想要做的是将它附加到一个片上，以便以后可以对它进行处理。要做到这一点，我们必须确保我们不会遭受其他可能搞乱追加操作的竞争条件——`append`操作肯定不是[线程安全的](https://en.wikipedia.org/wiki/Thread_safety)。

为了解决这个问题，我们使用一个`sync.Mutex`来强制所有的 goroutines 一次执行一个附加。

```
 var wg sync.WaitGroup
        sem := make(chan struct{}, 3)
        responses := []int{}
        mutex := &sync.Mutex{}

        for _, d := range results {
                wg.Add(1)

                go func(wait int) {
                        defer func() {
                                // Release the semaphore resource
                                <-sem
                                wg.Done()
                        }()

                        // Aquire a single semaophore resource
                        sem <- struct{}{}

                        // Imagine this is a long running operation, perhaps another
                        // REST API call
                        log.Printf("Start waiting for %d seconds\n", wait)
                        time.Sleep(time.Second * time.Duration(wait))
                        log.Printf("Finished waiting for %d seconds\n", wait)

                        mutex.Lock()
                        responses = append(responses, wait/2)
                        mutex.Unlock()
                }(d)
        }

        wg.Wait()

        for _, r := range responses {
                log.Printf("Got result %d", r)
        } 
```

看看辉煌的成功吧

```
2018/10/06 22:16:48 Start waiting for 8 seconds
2018/10/06 22:16:48 Start waiting for 19 seconds
2018/10/06 22:16:48 Start waiting for 10 seconds
2018/10/06 22:16:56 Finished waiting for 8 seconds
2018/10/06 22:16:56 Start waiting for 17 seconds
2018/10/06 22:16:58 Finished waiting for 10 seconds
2018/10/06 22:16:58 Start waiting for 3 seconds
2018/10/06 22:17:01 Finished waiting for 3 seconds
2018/10/06 22:17:01 Start waiting for 15 seconds
2018/10/06 22:17:07 Finished waiting for 19 seconds
2018/10/06 22:17:07 Start waiting for 10 seconds
2018/10/06 22:17:13 Finished waiting for 17 seconds
2018/10/06 22:17:13 Start waiting for 1 seconds
2018/10/06 22:17:14 Finished waiting for 1 seconds
2018/10/06 22:17:14 Start waiting for 6 seconds
2018/10/06 22:17:16 Finished waiting for 15 seconds
2018/10/06 22:17:16 Start waiting for 9 seconds
2018/10/06 22:17:17 Finished waiting for 10 seconds
2018/10/06 22:17:17 Start waiting for 20 seconds
2018/10/06 22:17:20 Finished waiting for 6 seconds
2018/10/06 22:17:20 Start waiting for 13 seconds
2018/10/06 22:17:25 Finished waiting for 9 seconds
2018/10/06 22:17:33 Finished waiting for 13 seconds
2018/10/06 22:17:37 Finished waiting for 20 seconds
2018/10/06 22:17:37 Got result 4
2018/10/06 22:17:37 Got result 5
2018/10/06 22:17:37 Got result 1
2018/10/06 22:17:37 Got result 9
2018/10/06 22:17:37 Got result 8
2018/10/06 22:17:37 Got result 0
2018/10/06 22:17:37 Got result 7
2018/10/06 22:17:37 Got result 5
2018/10/06 22:17:37 Got result 3
2018/10/06 22:17:37 Got result 4
2018/10/06 22:17:37 Got result 6
2018/10/06 22:17:37 Got result 10
2018/10/06 22:17:37 Total time taken: 49.006433s 
```

你可以说，我对这个结果很满意。

# 结论

> ```
> The 2 hardest things in computing are naming things, concurrency and off-by-one errors. 
> ```
> 
> *围棋界的名人，在 Twitter 上(我忘了是谁)*

因此，我们已经了解了如何工作(发出 API 请求？)并发，以及我们可以做些什么来限制我们想要的并发程度。然后，我们研究了如何将这个并发工作的结果传递到流程的下一步，而不会意外地以错误的方式读取数据或导致死锁。