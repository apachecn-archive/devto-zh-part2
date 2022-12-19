# 感谢 Rx 和你好箭头机器人

> 原文：<https://dev.to/daneko/thanks-rx-and-hello-arrow-for-androider-49j9>

兼作备忘录你好
标题太夸张了，放在**异步处理中用一下[Arrow](https://arrow-kt.io/docs/) 也说不定**左右。

[完整的样品在这里](https://github.com/daneko/HelloArrow/tree/a43d6f3932013525e1cd84baf1d28f11025ef882)

## 先用用看

```
@Throws(Exception::class)
fun tooSlowMethod():Result = ...

Single.create<Result> { emitter ->
    try {
        emitter.onSuccess(tooSlowMethod())
    } catch (e: Exception) {
        emitter.onError(e)
    }
}
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({
            ..
        }, {
            ..
        }) 
```

我们把这个重写下去吧。

首先只是试着用了 coroutine。

```
launch(UI) {
    try {
        val res = withContext(DefaultDispatcher) { tooSlowMethod() }
        ..
    } catch (e: Exception) {
        ..
    }
} 
```

arrow 版(而且这个还没有完成)

```
DeferredK { tooSlowMethod() } // or async { tooSlowMethod() }.k() etc...
    .unsafeRunAsync{ either ->
        either.fold(
            ..
        },{
            ..
        }) 
```

写法有很多种，总之是这样的吧？ ？

但是在以下条件下会一下子变弱…

```
fun isMainThread() = Looper.myLooper() == Looper.getMainLooper()

fun successProcess(result: Result) {
    if (!isMainThread()) throw RuntimeException("＼(^o^)／")
    Log.d("test", "success $result")
}

fun errorProcess(e: Throwable) {
    if (!isMainThread()) throw RuntimeException("＼(^o^)／")
    Log.d("test", "error", e)
}

Single.create<Result> { emitter ->
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({
            successProcess(it)
        }, {
            errorProcess(it)
        })

launch(UI) {
    try {
        val res = withContext(DefaultDispatcher) { tooSlowMethod() }
        successProcess(res)
    } catch (e: Exception) {
        errorProcess(e)
    }
}

// ＼(^o^)／
DeferredK{...}
    .unsafeRunAsync({successProcess(it)}, {errorProcess(it)}) 
```

有安卓吧。

```
/**
 * 実装はunsafeAsyncRunとほぼ同じだが、Callback 時のRunContextを選択できるようにしている
 * どうせ今回のケースだとEitherに対してfoldする(偏見)し、そこも展開してみた
 * Eitherに対してパターンマッチで…の場合もあるかもなので、お好みで
 */
fun <A> DeferredKOf<A>.unsafeRunAsyncWrapper(runContext: CoroutineContext, ifError: (Throwable) -> Unit, ifSuccess: (A) -> Unit) {
    async(Unconfined, CoroutineStart.DEFAULT) {
        Try { await() }.fold(
                { withContext(runContext) { ifError(it) } },
                { withContext(runContext) { ifSuccess(it) } })
    }.let {
        it.invokeOnCompletion { a: Throwable? ->
            if (a != null) throw a
        }
    }
}

DeferredK {
    tooSlowMethod()
}.unsafeRunAsyncWrapper(UI, { errorProcess(it) }, { successProcess(it) }) 
```

总觉得很像 Rx (？ )变成了呢。

倒不如说你不觉得 async 和 Rx 的优点太做作吗(感觉不到啊)

## 再用用看

基本上现实很严峻，让我们再现实一点吧。

```
fun slowMethodA(): ResultA {
    if (isMainThread()) throw RuntimeException("＼(^o^)／")
    ..
}

fun slowMethodB(): ResultB {
    if (isMainThread()) throw RuntimeException("＼(^o^)／")
    ..
}

fun makeResult(a: ResultA, b: ResultB): Result {
    ..
}

// DeferredKによって作られたThread 上で動く
fun run1(): DeferredK<Result> {
    return DeferredK {
        Log.d("test", "pre a running thread is ${Thread.currentThread()}")
        val a = slowMethodA()

        Log.d("test", "pre b running thread is ${Thread.currentThread()}")
        val b = slowMethodB()

        Log.d("test", "pre makeResult running thread is ${Thread.currentThread()}")
        makeResult(a, b)
    }
}

// 呼び出し側で作られたThreadで始まり、DeferredKで作られたThreadに切り替わりながら動く
fun run2(): DeferredK<Result> =
        ForDeferredK extensions {
            binding {
                Log.d("test", "pre a running thread is ${Thread.currentThread()}")
                val a = DeferredK { slowMethodA() }.bind()

                Log.d("test", "pre b running thread is ${Thread.currentThread()}")
                val b = DeferredK { slowMethodB() }.bind()

                Log.d("test", "pre makeResult running thread is ${Thread.currentThread()}")
                makeResult(a, b)
            }.fix()
        }

// 呼び出し側がmain threadだと死ぬ
fun run3(): DeferredK<Result> =
        ForDeferredK extensions {
            binding {
                Log.d("test", "pre a running thread is ${Thread.currentThread()}")
                val a = DeferredK.just(slowMethodA()).bind()

                Log.d("test", "pre b running thread is ${Thread.currentThread()}")
                val b = DeferredK.just(slowMethodB()).bind()

                Log.d("test", "pre makeResult running thread is ${Thread.currentThread()}")
                makeResult(a, b)
            }.fix()
        }

// bindingの中身は呼び出し側だが、各 slowMethod 内はCommonPoolを利用したものになる
fun run4(): DeferredK<Result> =
        ForDeferredK extensions {
            binding {
                Log.d("test", "pre a running thread is ${Thread.currentThread()}")
                val a = { DeferredK.just(slowMethodA()) }.bindIn(CommonPool).bind()

                Log.d("test", "pre b running thread is ${Thread.currentThread()}")
                val b = { DeferredK.just(slowMethodB()) }.bindIn(CommonPool).bind()

                Log.d("test", "pre makeResult running thread is ${Thread.currentThread()}")
                makeResult(a, b)
            }.fix()
        } 
```

突然出现了 For 句法，但我不会解释任何事情。

就是那个，就是那个。

如果让我写点什么的话，我会考虑的，所以请随便联系。

那么，各自的差异正如各方法的注释中所写的那样。

这些处理的共同点是

*   全部串联工作

是

还有，makeResult 的结果(如果没有副作用的话)也全部一样。

根据写法的不同，动作的 Thread 也不同，所以一边认真考虑这一点…

我觉得有必要这样说。

嗯，不仅仅是串联，当然也想并联移动吧…？

```
fun runParallels1(): DeferredK<Result> {
    val a = DeferredK { slowMethodA() }
    val b = DeferredK { slowMethodB() }
    return a.flatMap { aa -> b.map { makeResult(aa, it) } }
}

fun runParallels2(): DeferredK<Result> =
        ForDeferredK extensions {
            binding {
                val a: DeferredK<ResultA> = DeferredK { slowMethodA() }
                val b: DeferredK<ResultB> = DeferredK { slowMethodB() }
                makeResult(a.bind(), b.bind())
            }.fix()
        } 
```

如果是 slowMethodA/B 返回 throw 的模式的话，Handling 可能会变得很复杂。

另外，虽然特意明示了类型，但是我想绑定后更能直接看到 ResultA/B，感受到 For 句法上的喜悦。

并行动作版在调用方法单独运行且不会失败的时候进行研究不是很好吗？

(并不仅限于 DeferredK )

## 最后

如果只是这样的话，作为把 Arrow 冲进安卓的动机我觉得很弱。

只是，

*   kotlin 的 Deferred 想要地图/平板地图之类的
*   如果只是异步处理的话，Rx 很厚重(你别说了)啊……

如果想拍个照的话，我觉得值得摸一下。

如果你愿意的话，我也会写一个关于 EitherTransformer 的例子。

等不及了……这样的人，我觉得[官方 Document](https://arrow-kt.io/docs/datatypes/eithert/) 比较好。

话说回来，官方文档很好。

我们还接受其他 Arrow 的 APP 导入咨询等，请随意。 (有那样的家伙吗？ ）

## 参考

*   [Arrow 的正式文档的 kotlinxcoroutines 的项目](https://arrow-kt.io/docs/integrations/kotlinxcoroutines/)
*   [面向对 For 句法的 bind/bindIn 等感兴趣的人](https://github.com/arrow-kt/arrow/blob/0.7.2/modules/core/arrow-typeclasses/src/main/kotlin/arrow/typeclasses/MonadContinuations.kt#L50-L52)
    *   顺便说一下，For 句法本身就像是因为这个 MonadContinuations 很努力才形成的(虽然我只读得很潦草)

*   [完整样本](https://github.com/daneko/HelloArrow/tree/a43d6f3932013525e1cd84baf1d28f11025ef882)