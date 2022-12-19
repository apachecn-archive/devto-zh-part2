# 多租户任务调度程序

> 原文：<https://dev.to/brightdevs/multi-tenancy-task-scheduler-hj5>

[上次我向](https://dev.to%20post_url%202017-12-12-spring-mvc-multi-tenacy%20)展示了如何扩展 Spring 默认请求处理程序适配器，以便我们能够调度或拒绝传入的请求。`TenantTaskCoordinator`的目标是:

*   将请求排队等待处理
*   限制并发处理请求的最大数量
*   达到最大队列大小后拒绝请求
*   在上游预订处置时中断请求的处理

[![Assigning resources](../Images/4c2d6e52d290a93ccce07a47fa10da52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zHRqHMu5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jkcu0qwc2dz2541uns70.jpg)

## 租户任务协调器执行方法

我们进入`TenantTaskCoordinator`的切入点是一个单一的方法`fun <T : Any> execute(job: Callable<T>): Mono<T>` :

```
 fun <T : Any> execute(job: Callable<T>): Mono<T> {
        return Mono.create({ outsideSink ->
            val _workInProgressWasDecremented = AtomicBoolean(false)
            fun decrementOnce() {
                if (_workInProgressWasDecremented.compareAndSet(false, true)) {
                    currentWorkInProgressCounter.decrementAndGet()
                }
            }

            val workInProgress = currentWorkInProgressCounter.incrementAndGet()
            if (workInProgress > maximumWorkInProgress) {
                outsideSink.error(TooManyTasks("Work in progress $workInProgress exceeds $maximumWorkInProgress jobs in $name"))
                decrementOnce()
            } else {
                val singleJob = Mono.fromCallable(job).doAfterTerminate {
                    decrementOnce()
                }

                val delayedTask = Task(name, singleJob as Mono<Any>, outsideSink as MonoSink<Any>)

                outsideSink.onCancel {
                    delayedTask.outsideCancel()
                    decrementOnce()
                }

                taskSink.next(delayedTask)
            }
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

第一步是返回`Mono<T>`，这是用 [`Mono.create`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html#create-java.util.function.Consumer-) 简单完成的。我们得到的`sink`被用来控制从外部观察到的结果。它还允许注册一个 [`onCancel`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/MonoSink.html#onCancel-reactor.core.Disposable-) 回调，当上游取消它的订阅时调用。

`_workInProgressWasDecremented`用于以线程安全的方式保护和递减`currentWorkInProgressCounter`。我们首先检查我们是否立即超过了排队作业的最大数量。如果达到阈值，我们用 [`outsideSink.error`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/MonoSink.html#error-java.lang.Throwable-) 通知观察者错误。

如果我们有足够的容量来执行`job`，我们就用 [`Mono.fromCallable`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html#fromCallable-java.util.concurrent.Callable-) 将其转换成一个反应式世界，并附加一个 [`doAfterTerminate`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html#doAfterTerminate-java.lang.Runnable-) 回调来减少工作进程计数器。`Task`类链接`singleJob`和`outsideSink`，这样在处理时它们都是可访问的。最后，我们对`task`到`taskSink.next(delayedTask)`进行调度。

## 任务协调人状态

让我们来看看任务协调器的状态变量以及它们是如何初始化的:

```
class TenantTaskCoordinator(private val scheduler: Scheduler,
                            val maximumConcurrency: Int = 1,
                            val maximumQueueSize: Int = 50,
                            val name: String = "") : AutoCloseable {

    private val maximumWorkInProgress = maximumQueueSize + maximumConcurrency

    private val maxBufferSize = maximumWorkInProgress * 2

    val currentWorkInProgressCounter = AtomicInteger()

    private lateinit var taskSink: FluxSink<Task>

    private val taskSource = Flux.create<Task>({ taskSink = it }, FluxSink.OverflowStrategy.BUFFER)

    private val processSinkOnErrorResume = processSinkWithLimitedConcurrency()
        .onErrorResume { error: Throwable? ->
            LOG.warn("name={} Error processing sink with limited concurrency", name, error)
            processSinkWithLimitedConcurrency()
        } 
```

Enter fullscreen mode Exit fullscreen mode

第一个有趣的部分是我们如何通过使用 [`Flux.create`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#create-java.util.function.Consumer-reactor.core.publisher.FluxSink.OverflowStrategy-) 来设置`taskSink`。为了清楚起见，我们显式地传递了`FluxSink.OverflowStrategy.BUFFER`,以便在任务超过处理器速度的情况下对其进行缓冲。`name`用于获得更好的日志消息。最后，我们调用`processSinkWithLimitedConcurrency`来使用给定的`scheduler`开始任务处理。有趣的是，如果我们有一个 bug，那么`onErrorResume`会重新开始处理。

## 任务协调器并发处理

最重要和最棘手的部分是正确处理作业。我花了几个来回的步骤，直到我得到了正确的反应 API 调用顺序。

```
 private fun processSinkWithLimitedConcurrency(): Flux<Any> {
        return taskSource
            .filter { !it.isCancelled }
            .flatMap({ task ->
                task.work
                    .doOnError(task::onError)
                    .doOnSuccess(task::onSuccess)
                    .subscribeOn(scheduler)
                    .timeout(task.outsideTimeout)
                    .onErrorReturn(task)
            }, maximumConcurrency, maxBufferSize)
    } 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们过滤掉已经取消的任务。然后，我们使用 [`flatMap`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#flatMap-java.util.function.Function-int-int-) 重载来处理给定最大并发的任务。`flatMap`回调将大部分工作委托给提到的`Task`实例。`onErrorReturn`有效抑制了`task`执行过程中可能出现的任何错误。让我们看看内部的`Task`类是什么样子的:

```
private data class Task(val name: String,
                            private val job: Mono<Any>,
                            val outsideSink: MonoSink<Any>,
                            @field:Volatile var isCancelled: Boolean = false) {

        val work: Mono<Any> get() = if (isCancelled) Mono.empty() else job

        lateinit var outsideTimeoutSink: MonoSink<Task>
        val outsideTimeout = Mono.create<Task> { outsideTimeoutSink = it }

        fun outsideCancel() {
            isCancelled = true
            outsideTimeoutSink.success(this)
        }

        fun onSuccess(result: Any?) {
            outsideSink.success(result)
        }

        fun onError(error: Throwable) {
            LOG.warn("Task.onError {}", this, error)
            outsideSink.error(error)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

`job`参数是传递给`execute`方法的`Callable`。当`task`实例订阅被取消时，`outsideTimeout`发出信号。信号通过 [`Mono.timeout`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html#timeout-org.reactivestreams.Publisher-) 调用在`processSinkWithLimitedConcurrency`内部传播，并中断`task`处理。最后但同样重要的是，`onSuccess`和`onError`简单地将结果或错误推送到`outsideSink`，有效地通知观察者结果。

考虑到帖子请求中提到的要求， [`TenantTaskCoordinator`](https://gist.github.com/miensol/1e2b203a128cdc428f3b0c598e515bd6) 并不简单。我对最终的结果很满意，尽管我必须说，要想出如何组合[反应堆](https://projectreactor.io/)库的所有细节以达到预期的结果并不直观。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)