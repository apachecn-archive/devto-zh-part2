# Android 第 2 部分:视图模型和视图模型工厂

> 原文：<https://dev.to/autonomousapps/dagger-dot-android-part-2-viewmodels-and-viewmodel-factories-10ii>

*(不，不是那种工厂)*

1.  [第 1 部分—基本设置](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-33kj)
2.  这部分
3.  [第 3 部分—片段](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-part-3-fragments-12go)

如果你阅读了本教程的第一部分，那么你已经知道如何建立一个使用 [dagger.android](https://google.github.io/dagger/android) 包的项目的基本知识，这是谷歌(相对)对 dagger 和 android 的新看法。您知道注入一个`Activity`的基本知识，并且您知道如何用测试替身替换您的应用程序的生产对象，以使仪器测试变得轻而易举(相对而言...).

现在在第 2 部分中，我们将学习如何使用来自 [Android 架构组件](https://developer.android.com/topic/libraries/architecture/)的[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel)和 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) ，来管理我们 Dagger 提供的对象的生命周期。

# 设置

将以下内容添加到您的`app/build.gradle`文件中:

```
dependencies {
  // ...all the libs...
  implementation "android.arch.lifecycle:extensions:1.1.1"
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们只是添加了一个依赖项。这个给了我们访问`ViewModel`和`LiveData`(以及朋友)的权限。现在，让我们使用这些类来管理我们的“视图”的状态，由我们的活动来表示。

# `MainActivity`

这和我们在[第一部](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-33kj)里看到的很像。新部分被标注出注释。

```
class MainActivity : AppCompatActivity() {
  // New!
  @Inject lateinit var viewModelFactory: MainActivityViewModelFactory
  private val viewModel by lazy(mode = LazyThreadSafetyMode.NONE) {
    ViewModelProviders.of(this, viewModelFactory)
      .get(MainActivityViewModel::class.java)
  }

  override fun onCreate(savedInstanceState: Bundle?) {
    AndroidInjection.inject(this)
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    // New!
    viewModel.counter().observe(this, Observer {
      // Assuming the existence of a TextView with the id "counterText"
      counterText.text = it.toString()
    })
    // Assuming the existence of a Button with the id "counterButton"
    counterButton.setOnClickListener {
      viewModel.onClickCounter()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是我们与上次相比的变化:

1.  我们正在注入一个“视图模型工厂”，它实际上是一个[视图模型提供者。工厂](https://developer.android.com/reference/android/arch/lifecycle/ViewModelProvider.Factory)。只有当您的`ViewModel`有一个非默认的构造函数时，这才是必要的。换句话说，只要你想做任何有趣的事情，这是绝对必要的。
2.  我们使用视图模型工厂来提供我们实际的视图模型，这是我们真正关心的事情。
3.  我们使用`by lazy`是因为我们真的喜欢不变性，这是拥有`val`视图模型的唯一方式。(参见本文底部的注释，了解我设置该模式的原因。)
4.  然后，我们将视图模型与几个小部件配合使用。

你们中眼尖的人会注意到我们正在注入一个新对象(`MainActivityViewModelFactory`)，但是我还没有展示如何注入这个新对象的任何代码。在一个`@Module`里面不需要一个`@Provides`或者`@Binds`吗？没有那么多。Dagger 支持两种绑定，我称之为“显式”和“隐式”。

## 显式绑定

```
@Module abstract class MyModule {
  @Binds abstract fun bindThing(impl: ThingImpl): IThing
  @Provides @JvmStatic fun provideThingama(bob: Bob): Thingama = Thingama(bob)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们明确地告诉 Dagger 它如何创建`IThing` s 和`Thingama` s 的实例。在第一种情况下，我们将`IThing`接口绑定到它的具体实现`ThingImpl`，因此我们使用`@Bind`。在第二种情况下，`Thingama`类(可悲的是)没有实现接口，所以我们必须直接提供它的实例，因此我们使用了`@Provides`。

但即使在这里我们也看到我作弊了。`ThingImpl`从哪里来？那`Bob`呢？

## 隐式绑定

```
class ThingImpl @Inject constructor()

class Bob @Inject constructor() 
```

Enter fullscreen mode Exit fullscreen mode

这些是隐式绑定，它们只需要我们注释我们的一个构造函数(即使是默认的！)同`@Inject`。

*当然*，我们可以指定带有任意数量参数的非默认构造函数，并且仍然依赖 DAG 来实例化这些对象——只要我们的 **D** 定向 **A** 循环 **G** 依赖图包含了 DAG ger 提供每个所需对象所需的信息。

# `MainActivityViewModelFactory`

对于*琐碎的* `ViewModel` s，我们可以遵循文档中的基本示例代码，这样做:

```
// `this` can be a variety of things, but in our example, it is an `Activity`
private val viewModel = ViewModelProviders.of(this).get(MyViewModel::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，连一家工厂都看不到。

但是如果我们的视图模型很有趣的话，它肯定需要合作者；也就是说，非默认构造函数。比如:

```
class MutableObject(var counter: Int = 0)

class MainActivityViewModel(
  private val mutableObject: MutableObject
) : ViewModel() {
  // We need our counter to be mutable...
  private val counterLiveData = MutableLiveData<Int>()
  // ...but we only want to expose it to observers as an immutable object
  fun counter(): LiveData<Int> = counterLiveData

  init {
    // initialize it to something, else `value` will be `null`, which is annoying
    counterLiveData.value = mutableObject.counter
  }

  // Clicking increments the counter. Nothing simpler!
  fun onClickCounter() {
    counterLiveData.value = ++mutableObject.counter
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们试图用上面的例子来获取对这样一个视图模型的引用，那将会失败！有一个*默认* `ViewModelProvider.Factory`知道如何用*默认*(无参数)构造函数创建`ViewModel`，但它不可能知道如何创建我们的自定义视图模型。

# 视图模型提供者。工厂

你肯定会注意到以下内容:

```
ViewModelProviders.of(this, viewModelFactory).get(MainActivityViewModel::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

这是对接受自定义工厂的`ViewModelProviders.of()`方法的重载。其中一个看起来像什么？这里有一种方法:

```
class MutableObjectFactory @Inject constructor() {
  fun newMutableObject() = MutableObject()
}

class MainActivityViewModelFactory @Inject constructor(
  private val mutableObjectFactory: MutableObjectFactory
) : ViewModelProvider.Factory {

  @Suppress("UNCHECKED_CAST")
  override fun <T : ViewModel> create(modelClass: Class<T>): T {
    return when {
      modelClass.isAssignableFrom(MainActivityViewModel::class.java) -> {
        MainActivityViewModel(mutableObjectFactory.newMutableObject()) as T
      }
    else -> throw IllegalArgumentException(
        "${modelClass.simpleName} is an unknown type of view model"
      )
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里定义了两个新的类。先说视图模型工厂。

## 视图模型提供者。工厂，已定义

这是一个声明单个方法`create()`的接口。它接受对它必须创建的类的引用，并返回该类的一个实例。如果我们把它拆开，这就是我们得到的:

```
class MyViewModelFactory : ViewModelProvider.Factory {
  @Suppress("UNCHECKED_CAST")
  override fun <T : ViewModel> create(modelClass: Class<T>): T {
    return MyViewModel() as T
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这基本上就是默认工厂所做的。

另一个类`MutableObjectFactory`，比较琐碎。我声明它是为了展示一个重要的观点。

# 我们到底为什么要关心 s 呢？

我们关心他人，因为他们

1.  *活在*活动/片段生命周期之外
2.  *了解*活动/片段生命周期。

这是*非常有用的*。因为视图模型存在于生命周期之外，所以它们不会在每次你的活动被破坏时都被破坏(哈哈，旋转，走开)。但是因为他们也*知道*生命周期，所以您可以在`Activity.onCreate()`中开始观察一个`LiveData`对象，并且您将仅在活动处于“已开始”状态(在`onStart`和`onStop`之间)时接收关于该对象的更新。

在 Dagger 的上下文中，这有点像拥有一个自定义的`@Scope`，而不必声明或管理它。我们在`Activity.onCreate()`中创建我们的对象，改变那些对象，旋转屏幕，*再次获得对那些相同对象的引用*，然后继续下去，一点问题都没有。

不过，有一个小问题。这是活动代码，再说一遍:

```
class MainActivity : AppCompatActivity() {

  // Hmmm
  @Inject lateinit var viewModelFactory: MainActivityViewModelFactory

  private val viewModel by lazy {
    ViewModelProviders.of(this, viewModelFactory).get(MainActivityViewModel::class.java)
  }

  override fun onCreate(savedInstanceState: Bundle?) {
    // Hmmmmm
    AndroidInjection.inject(this)

    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    viewModel.counter().observe(this, Observer {
      counterText.text = it.toString()
    })
    counterButton.setOnClickListener {
      viewModel.onClickCounter()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*每次*旋转我们的设备，活动都会经历`onDestroy` / `onCreate`。因此，*每次*我们旋转我们的设备，我们就注入一个**新的**视图模型工厂！*然而*，即使*工厂*是新的，对`ViewModelProviders.of(this, factory).get(MyViewModel::class.java)`的调用也会返回*相同的*视图模型！Android 架构组件框架维护了一个独立于 Dagger 的类到视图模型的独立映射。因此，每次您试图获取对已经存在的视图模型的引用时，它都会返回现存的实例。

## 重物和`MutableObjectFactory`

这让我们回到了`MutableObjectFactory`。如果我们不是向我们的视图模型工厂提供那个工厂，而是提供一个`MutableObject`本身的实例，那么每当我们实例化一个视图模型工厂时，我们就会实例化一个新的`MutableObject`,顺便说一下，就是每次我们在活动中注入一个视图模型工厂时——也就是每次我们轮换时。如果`MutableObject`非常重，那么我们希望避免创建永远不会用到的新实例。所以，我们提供了一个工厂。用代码说明:

```
// New instances of this class are created each time we inject our activity
class MainActivityViewModelFactory @Inject constructor(
  // that means new instances of this factory are created each time
  private val mutableObjectFactory: MutableObjectFactory
) : ViewModelProvider.Factory {
  @Suppress("UNCHECKED_CAST")
  override fun <T : ViewModel> create(modelClass: Class<T>): T {
    return when {
      // HOWEVER, this code will ONLY BE CALLED ONCE
      modelClass.isAssignableFrom(MainActivityViewModel::class.java) -> {
        MainActivityViewModel(mutableObjectFactory.newMutableObject()) as T
      }
      else -> throw IllegalArgumentException(
        "${modelClass.simpleName} is an unknown type of view model"
      )
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想(和我一样)“恶心。创建工厂比创建重物本身更好，但我甚至可以避免创建那个工厂吗？”良好的...是的但是。你可以声明并管理一个自定义的 Dagger `@Scope`来处理这个问题。你可以使用静态实例。你可以在编程技术上变得非常聪明。或者您可以分析这段代码，自己决定是否值得这样做。(以后的帖子将讨论自定义范围。)

那是给模特看的吗？不完全是。我们仍然需要在片段的上下文中讨论它们，相信我，这里有一些有趣的问题。但是...下次见。

# 备注

1.  科特林`by lazy`的同步模式。我们使用`mode = LazyThreadSafetyMode.NONE`是因为默认模式是`LazyThreadSafetyMode.SYNCHRONIZED`，它使用[双重检查锁定模式](https://en.wikipedia.org/wiki/Double-checked_locking)来确保只创建这个对象的一个实例。这增加了我们不想要的开销，因为我们想最小化在主线程上完成的工作。我们实现了“只有一个实例”的目标，但是只在主线程上访问我们的视图模型；因为我们的编程模型是单线程的，所以不可能以并发的方式调用我们的`by lazy`，因此也不可能意外地创建我们的视图模型的多个实例。