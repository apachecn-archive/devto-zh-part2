# 对了，放 ARROW 试试

> 原文：<https://dev.to/daneko/arrow-3ag9>

想摸摸 ARROW，但是周围没有人在摸 arrow…面向那样的(什么样的)人

## Conclusion

官方文档的 Beginner 标记(？ )读一下附带的东西什么的。

[选项](https://arrow-kt.io/docs/datatypes/option/)
[要么](https://arrow-kt.io/docs/datatypes/either/)
[试试](https://arrow-kt.io/docs/datatypes/try/)

剩下的试着用一下……没关系，看吧，如果只是一次的话……

也就是说，如果一开始就知道这一带的话可能会很开心？ 潦草地介绍了类似的东西。

## 选项

kotlin 的 NullSafety (或者说 Nullable )虽然很方便……但是为了
的你。

“NullSafety (或者说 Nullable )不行吗？ ？ ”的人在“Nullable Option”之类的网站上发牢骚的话，会出现各种各样的报道，
原来如此！ ！ 的情况下特别推荐(这里的商户费率好像很高)

与其说途径不同……但是，你看，如果只是试着用一次的话…

```
fun <A> convertSample(src:A?) {
    val a:Option<A> = Option.fromNullable(src)
    val b:Option<A> = src.toOption()
}

fun <A> convertSampleFromNonNull(src:A) {
    convertSample(src)
    val a:Option<A> = Option(src)
    val b:Option<A> = Option.just(src)
    val c:Option<A> = src.some()
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然有很多，但是用 toOption ( )统一一下不就好了吗？

肖特特例？
[面向在 FunctionalJava 上使用过 Option#iif](https://github.com/functionaljava/functionaljava/blob/v4.8/core/src/main/java/fj/data/Option.java#L726) 之类的人
哎呀，那种人看了这个博客之后会得到什么呢…

```
fun condition(...):Boolean = ...

fun returnValue():A

val a:Option<A> = condition(...).maybe{ returnValue() } 
```

Enter fullscreen mode Exit fullscreen mode

[在布尔中生长了名为 maybe 的扩展函数](https://github.com/arrow-kt/arrow/blob/0.7.3/modules/core/arrow-core/src/main/kotlin/arrow/core/Option.kt#L204)，在那边

### Example of

```
fun <A, B, C> nonErrorMethod(a: A, b: B, c: C): Result {
    // エラーも副作用もないような処理
}

fun <A, B, C> kotlinSample(a: A?, b: B?, c: C?) {

    val result1: Result? = if (a != null && b != null && c != null) {
        nonErrorMethod(a, b, c)
    } else {
        null
    }

    val result2: Result? = a?.let { nonNullA ->
        b?.let { nonNullB ->
            c?.let { nonNullC ->
                nonErrorMethod(nonNullA, nonNullB, nonNullC)
            }
        }
    }
}

fun <A, B, C> useArrowSample(a: A?, b: B?, c: C?) {
    val optionA = a.toOption()
    val optionB = b.toOption()
    val optionC = c.toOption()

    val result1:Option<Result> = optionA.flatMap { nonNullA ->
        optionB.flatMap { nonNullB ->
            optionC.map{ nonNullC ->
                nonErrorMethod(nonNullA, nonNullB, nonNullC)
            }
        }
    }

    val result2: Option<Result> = Option.monad().binding {
        val a:A = optionA.bind()
        val b:B = optionB.bind()
        val c:C = optionC.bind()

        nonErrorMethod(a, b, c)
    }.fix()

    val result3: Option<Result> = Option.applicative()
        .map(optionA, optionB, optionC) { (a, b, c) -> nonErrorMethod(a, b, c) }.fix()
} 
```

Enter fullscreen mode Exit fullscreen mode

大概是这样吧。
无论哪种情况，如果任何参数为 Null/None，则结果也为 Null/None。

虽然出现了 monad、applicative 等，但是
我认为只要是能做出类似↑的样本行为的方便的句法…就可以了。
特别是前者，比如说使用 Scala 的人，比如说 for 式的性感家伙……

具体为

```
Option.monad().binding{...}.fix()でくくると、
... の中でOption<X>に対して.bind()ってメソッドが生える
呼ぶとOptionがNoneだったら処理を抜けてNoneを返し
SomeだったらXを返して次へ進む

Option.applicative()(aOption,bOption,cOption,..., {(a,b,c,...) -> ... }).fix()
で、aOption,bOptino,cOption,... の全部がSomeだった場合だけ、最後の引数で渡したメソッドが実行される 
```

Enter fullscreen mode Exit fullscreen mode

像是。

虽然可能会被可怕的人骂，但是我觉得那样就可以了。

### 引用

下記にもっと書 https://arrow-kt.io/docs/datatypes/option/
[いてある](https://arrow-kt.io/docs/datatypes/option/)

## 要么

虽然是个人的印象，但是如果使用 LiveData 或者 Observable 各种各样的话，
很多情况下会自己安装(想做)这种东西。
好像不仅想在成功时传播，在失败时也想传播……

例如[这一带的滑动](https://speakerdeck.com/ryugoo/either-dexiang-ding-nei-falseeratoxiang-ding-wai-falseerawobie-mofalsenisitai)

为了那样的你，已经准备好了。

### Example of

```
 sealed class MyError(val msg:String)
object MyError1 : MyError("error 1")
object MyError2 : MyError("error 2")

val liveData:LiveData<Either<MyError, Result>> = MutableLiveData()

liveData.observe(..., Observer{ either ->
    handlingSample(either)
})

fun handlingSample(either: Either<MyError, Result>) {
    val result1: String = either.fold(
        {
            error ->    
            error.msg
        },
        {
            res ->
            "success"
        }
    )

    val result2: String = when (either) {
        is Either.Left -> when (either.a) {
            MyError1 -> "MyError1 ${either.a}"
            MyError2 -> "MyError2 ${either.a}"
        }
        is Either.Right -> "success"
    }
}

fun aEither():Either<MyError, A>
fun bEither():Either<MyError, B>
fun cEither():Either<MyError, C>

fun useArrowSample() {

    val result1 = aEither().flatMap { a ->
        bEither().flatMap { b ->
            cEither().map { c ->
                nonErrorMethod(a, b, c)
            }
        }
    }

    val result2: Either<MyError, Result> = Either.monad<MyError>().binding {
        val a = aEither().bind()
        val b = bEither().bind()
        val c = cEither().bind()
        nonErrorMethod(a, b, c)
    }.fix()

    val result3: Either<MyError, Result> =
            Either.applicative<MyError>().map(aEither(), bEither(), cEither()) { (a, b, c) -> nonErrorMethod(a, b, c) }.fix()
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的人在 LiveData 上出错的时候也想做点什么(这种情况下只是免费收集在 String 里…)之类的粗糙的样本

后半部分总觉得和 Option 的时候很相似呢？

如果是用坦率的 Kotlin 写的话会怎么写呢…
因为我觉得会给人一种在 LiveData 上建立传播用的班级的感觉，所以[GoogleSamples 之类的…？](https://github.com/googlesamples/android-architecture-components/blob/724cc1bd6ed11171a0bbf4a3a29977fac053777e/GithubBrowserSample/app/src/main/java/com/android/example/github/api/ApiResponse.kt)

### 引用

下面写得更多
[https://arrow-kt.io/docs/data types/either /](https://arrow-kt.io/docs/datatypes/either/)

## 试试

因为很相似(？ )[在 1.3-M1 中，虽然有 SuccessOrFailure 之类的](https://blog.jetbrains.com/kotlin/2018/07/see-whats-coming-in-kotlin-1-3-m1/)，但是总觉得这个……好像是
`we discourage using SuccessOrFailure as return type of functions`，所以
[就像这个人的评论中说的那样理解比较好](https://github.com/Kotlin/KEEP/issues/127#issuecomment-406789929)
`This SuccessOrFailure class SHOULD NOT be used directly as a return type only in case the failure is handled by the caller locally. Where nullable type and sealed class hierarchy could be used instead.`
[可以说这样的话就不知道这一带的句法了...](https://github.com/Kotlin/KEEP/blob/master/proposals/stdlib/success-or-failure.md#api-details)

哎呀，这个用起来很容易理解吗……

嘛 1.3 的话就知道了吧，长的东西会缠上的。
[顺便说一下，我更在意 M2 里提到的 Contracts，所以更想要。](https://blog.jetbrains.com/kotlin/2018/08/kotlin-1-3-m2/)

Contracts 语法不是很棒吗？
在 Internal 上是从 1.1 或 2 左右开始使用的，给我接下来。

### Example of

大概已经能想象到了

```
 sealed class MyError(val msg:String): Throwable(msg)
object MyError1 : MyError("error 1")
object MyError2 : MyError("error 2")

val liveData:LiveData<Try<Result>> = MutableLiveData()

liveData.observe(..., Observer{ _try ->
    handlingSample(_try)
})

fun handlingSample(src: Try<Result>) {
    val result: String = src.fold(
            { error ->
                when (error) {
                    is MyError1 -> error.msg
                    is MyError2 -> error.msg
                    else -> "unknown error"
                }
            },
            { res ->
                "success"
            }
    )
}

fun aSometimeThrow():A
fun bSometimeThrow():B
fun cSometimeThrow():C
fun aTry():Try<A> = Try{ aSometimeThrow() }
fun bTry():Try<B> = Try{ bSometimeThrow() }
fun cTry():Try<C> = Try{ cSometimeThrow() }

fun kotlinSample() {
    try {
        val a = aSometimeThrow()
        val b = bSometimeThrow()
        val c = cSometimeThrow()
        nonErrorMethod(a, b, c)
    } catch (e:MyError) {
        // ...
    } catch (e:Throwable) {
        // ...
    }
}

fun useArrowSample() {

    val result1:Try<Result> = aTry().flatMap { a ->
        bTry().flatMap { b ->
            cTry().map { c ->
                nonErrorMethod(a, b, c)
            }
        }
    }

    val result2: Try<Result> = Try.monad().binding {
        val a = aTry().bind()
        val b = bTry().bind()
        val c = cTry().bind()
        nonErrorMethod(a, b, c)
    }.fix()

    val result3: Try<Result> =
            Try.applicative().map(aTry(), bTry(), cTry()) { (a, b, c) -> nonErrorMethod(a, b, c) }.fix()

    // call handlineSample etc... 
} 
```

Enter fullscreen mode Exit fullscreen mode

怎么说呢，是令人吃惊的 Option 版的复制品呢！

### 引用

关于 Try，有各种各样的 Handling 方法和向 Either 的变换什么的，所以不太好看。即使在
`Either<Throwable,...>`中增加便利方法也可以读作(杂)

[https://arrow-kt.io/docs/datatypes/try/](https://arrow-kt.io/docs/datatypes/try/)

# 中间总结

## 毕竟文档很旧

说实话，官方 Document 充实到不知道写这篇文章有什么意义。
，但是要注意的地方也是…

科特林·slackの#arrowで
[我们反对 ForXX 扩展，支持直接在类型](https://kotlinlang.slack.com/archives/C5UPMM0A0/p1533658414000125)
と上生成扩展函数言っていますので、

```
ForOption extensions {
    binding {
        ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
Option.monad().binding {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

好像有必要重新读一读。

## He

Option/Either/Try 的使用案例与其说相似，不如说是一样吧。
也就是说，其实其他准备的东西也差不多可以用了。

只是界面，对吧？

实际上，如果写了那是 m 型的那个……之类的话，好像会被啪嗒啪嗒地刺伤…
[读了这里的说明之后，应该会接受吧](https://arrow-kt.io/docs/patterns/monads/)

话虽如此，看这里的人大概是英语很辣的派系吧，所以
上面的链接的“只要慢慢读样本代码”就会有这样的感觉吧？ ？

# 因为累了，所以潦草地吃了几个

## 中途没有用 Either 之类的包起来，希望你能帮我包起来

```
val res1 = Either.monadError<Throwable>().bindingCatch {
    val a = Either.right(1).bind()
    val b = throwError() // ここでLeftになって返る
    a + b
}.fix()

val res2 = Try.monadError().bindingCatch {
    val a = Try.just(1).bind()
    val b = throwError() // ここでFailureになって返る
    a + b
}.fix() 
```

Enter fullscreen mode Exit fullscreen mode

`monad().binding{...}`→置换为`monadError().bindingCatch{...}`的话哎呀不可思议

但是，并不是什么都能叫上绑定 catch…
我想你会注意到 IDE 的补充。

对于 Either，仅限于左可旋转

[在文档上是这一带](https://arrow-kt.io/docs/patterns/monad_comprehensions/#error-propagation-in-comprehensions)

## 想切换 Thread 到 rxjava 方格

```
Single.just(...)
    .map(...)
    .observeOn(Schedulers...)
    ...
    .subscribeOn(AndroidSchedulers...)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

自私的家伙

[这一带](https://arrow-kt.io/docs/patterns/monad_comprehensions/#non-blocking-thread-jumps)
[这一带](https://arrow-kt.io/docs/effects/async/#continueon)

从根本上来说，作为免费的异步处理使用时的比较这一点没有错。

顺便说一下，用方法链不连接也可以做

```
val (res1, disposable1) = IO.async().run {
    bindingCancellable {
        continueOn(Unconfined)
        val a = IO.just(1).bind()
        val b = throwError()
        a + b
    }
}

res1.fix().attempt().unsafeRunSync().fold(
        {
        },
        {
            fail()
        }
)

val (res2, disposable2) = DeferredK.async().run {
    bindingCancellable {
        continueOn(Unconfined)
        val a = DeferredK.just(1).bind()
        val b = throwError()
        a + b
    }
}

res2.fix().unsafeRunAsync {
    it.fold(
            {
            },
            {
                fail()
            }
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样之类的。
好像是刚才的绑定 catch 的更高级版(？ )那家伙有个叫 Cancellable 的。
这样，返回值为 Tuple，第二个返回值为 Cancel 用的。

写法有很多是好是坏……

以上示例会被`disposable1()`等取消

[这一带](https://arrow-kt.io/docs/patterns/monad_comprehensions/#cancellation-and-cleanup-of-resources)

## `List<Option<Int>>`这样的会不会变成`Option<List<Int>>`？

这个，在 SuccessOrFailure 的报道中也出现了类似`List<SuccessOrFailure<String>>`的东西，我想起来了。
想变得像`SuccessOrFailure<List<String>>`一样！ 感觉好像有什么事…
(嘛，期待有多个 Error 时的处理的时候就是那个)

如果最后被 Return 的东西是`List<Option<Int>>`的话，感觉要仔细检查一下里面的东西，但是在
处理的过程中，如果是`Option<...>`的话，明明会被 flatMap 吃掉……是那种时候的用途吗？

```
val src = listOf(1, 2, 3, 4)
val target:List<Option<Int>> = src.map { (it % 2 == 0).maybe { it } }

val actual:Option<List<Int>> = target.k().sequence(Option.applicative()).fix()

assertEquals(actual, None)

val target2:List<Try<Int>> = src.map {
    Try {
        if (it % 2 == 0) it
        else throw Exception("$it")
    }
}

val actual2:Try<List<Int>> = target2.k().sequence(Try.applicative()).fix()

actual2.fold(
        {
            assertTrue(it.message == "1")
        },
        {
            fail()
        }) 
```

Enter fullscreen mode Exit fullscreen mode

以上的例子本来就是奇数过滤的案件，但是这一点嘛……因为是例子……记住
`sequence`和`applicative`这样的字可能会比较好。

这个，如果是官方 Document 的话，会在哪里呢…
Traverse 附近写呢？
[https://arrow-kt.io/docs/type classes/traverse/# traverse](https://arrow-kt.io/docs/typeclasses/traverse/#traverse)

## 没有更有力的使用方法吗？ ？

`Option<Either<X,Y>>`想漂亮地(无嵌套)使用像这样的东西！
之类的，你可能会想知道
[OptionT](https://arrow-kt.io/docs/datatypes/optiont/) 或者
[EitherT](https://arrow-kt.io/docs/datatypes/eithert/) 之类的。

还有就是用 Android 在这样的上下文中，虽然无法想象有多少机会使用，但是明白了
的意思！ ！ 我想应该是[切换 Validated 和 Either 的样本](https://arrow-kt.io/docs/datatypes/validated/#alternative-validation-strategies-to-validated-using-applicativeerror)。

在这个示例中，我们会一并检查 Form 字段的输入，标签和地址两者。
大概 Android (或者说是前台)的话，输入表单本身(从输入的一端)会显示 Validation

那么，我想是不是只有通过了那个 Validation (们)的东西才能传播……

总觉得很难想象特意使用这个(特别是 Validated )的情况吧。 (你说到底是安卓大脑？ )
[另外，这里是样品。](https://github.com/dcampogiani/AndroidFunctionalValidation)

不过，我觉得从这个样品中可以学到很多东西，所以推荐。

算了，放进去的话必须使用全功能！ 没有什么规则，从
Option/Either 左右开始，如果可以的话，我想试试其他的也可以。

# 结束

总觉得错了，希望你能写得更多一些！ 如果有什么的话，请草率地联系我。
`.k()`或者`Kind<F,A>`之类的说明会很受欢迎吧？ ？ ？
如果你愿意的话就写。