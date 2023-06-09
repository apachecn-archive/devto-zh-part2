# dagger.android 缺失文档

> 原文：<https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-33kj>

# 这里没有热虹吸管！

1.  这部分
2.  [第二部分—视图模型和视图模型工厂](https://dev.to/autonomousapps/dagger-dot-android-part-2-viewmodels-and-viewmodel-factories-10ii)
3.  [第 3 部分—片段](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-part-3-fragments-12go)

所以你想使用 [dagger.android](https://google.github.io/dagger/android) ，Dagger2 的(相对)新包专门针对你，android 开发者，但对官方文档中缺少[热虹吸管和咖啡机](https://i.imgur.com/iP52Th1.png)感到困惑。dev 能做什么？

[![Finding thermosiphons](img/35795345666330f6389d71ff2955c32e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DQnZ-mjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/z3i2qQ4.png)

我掩护你。这是我关于[重写 Chess.com 的 Android 应用](https://dev.to/autonomousapps/rewriting-chesscoms-android-app-4aij)系列的延续。我尽量做到彻底，但如果结果是我略读了一些东西，请在评论中告诉我。

我们开始吧！

# 项目设置

将以下内容添加到您的`app/build.gradle`文件

```
// If you're using Kotlin
apply plugin: 'kotlin-kapt'

dependencies {
  // ...all the libs...

  // Dagger
  def dagger_version = "2.15"
  // Required
  implementation "com.google.dagger:dagger-android:$dagger_version"
  kapt "com.google.dagger:dagger-android-processor:$dagger_version"

  // Required if you use anything prefixed with AppCompat or from the support library
  implementation "com.google.dagger:dagger-android-support:$dagger_version"
  kapt "com.google.dagger:dagger-compiler:$dagger_version"

  // Required if you care about testing, and of course you care about testing. Required.
  kaptAndroidTest "com.google.dagger:dagger-compiler:$dagger_version"
  kaptAndroidTest "com.google.dagger:dagger-android-processor:$dagger_version"
} 
```

# 注入您的自定义应用程序

创建一个自定义的应用程序类，调用(比如)`MainApplication`，使它看起来像这样:

```
// open because we will have a DebugMainApplication for testing
open class MainApplication : Application(), HasActivityInjector {

  // Required by HasActivityInjector, and injected below
  @Inject
  protected lateinit var dispatchingAndroidInjector: DispatchingAndroidInjector<Activity>
  override fun activityInjector() = dispatchingAndroidInjector

  override fun onCreate() {
    super.onCreate()
    initDagger()
  }

  private fun initDagger() {
    DaggerMainApplication_MainApplicationComponent.builder()
      .app(this)
      .build()
      .inject(this)
  }

  // Doesn't need to be a nested class. I could also put this in its own file or,
  // this being Kotlin, in the same file but at the top level.
  @Singleton
  @Component(modules = [
    // provided by dagger.android, necessary for injecting framework classes
    AndroidSupportInjectionModule::class,

    // Defines a series of Subcomponents that bind "screens" (Activities, etc)
    ScreenBindingModule::class,
  ])
  interface MainApplicationComponent {
    fun inject(app: MainApplication)

    @Component.Builder
    interface Builder {
      fun build(): MainApplicationComponent
      @BindsInstance fun app(app: Context): Builder
    }
  }
} 
```

以下是我们所做的:

1.  定义了一个自定义`Application`，
2.  实现了`HasActivityInjector`，
3.  它定义了根/全局/单例/应用程序组件，是应用程序所有子组件的父组件，
4.  这指定它获取应用程序本身的一个实例(`@BindsInstance`)，这意味着我们现在有了可用于这个组件及其所有子组件的`MainApplication`实例。
5.  最后，构建 Dagger 生成的组件契约的实现，然后用它给我们的应用程序注入一个`DispatchingAndroidInjector<Activity>`

## 什么是`DispatchingAndroidInjector`？

[![magic](img/a6da6df0626b830ea7ed4346614680ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zp6bNlaM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/12NUbkX6p4xOO4/giphy.gif)

它最终注入你的框架类(活动、片段、服务等)。稍后，在您的活动中，您将调用`AndroidInjection.inject(this)`，这利用了您的`MainApplication`通过`HasActivityInjector`提供的`DispatchingAndroidInjector`实例。

# 屏幕绑定模块

```
// Don't worry, it'll get bigger!
@Module abstract class ScreenBindingModule {
  @ActivityScoped // optional
  @ContributesAndroidInjector(modules = [MainActivityModule::class])
  abstract fun mainActivity(): MainActivity
} 
```

`ScreenBindingModule`是用`@dagger.Module`标注的抽象类。在其中，我们需要为每个想要注入的活动添加一个用`@ContributesAndroidInjector`注释的抽象函数。这个函数应该返回活动的一个实例(它实际上并不“创建”您的活动；这只是 Dagger 如何知道哪个类被注入)。我们可以选择指定要安装在这个子组件上的模块，并选择指定作用域。这些函数中的每一个实际上都定义了一个`Subcomponent.Builder`用来注入你的活动类；代码本身由 dagger 生成。本质上，每个活动都有一个函数。

(PS: `@ActivityScoped`是一个自定义范围，您必须自己定义。参见下面链接的完整代码示例。)

# 注射你的`Activity` s

```
// We can define this anywhere we like, but it's convenient to include
// in the same file as the class being injected
// An object because I want to provide a static function, and it's Kotlin
@Module object MainActivityModule {
  // static because dagger can call this method like MainActivityModule.provideText(),
  // rather than new MainActivityModule().provideText()
  @Provides @JvmStatic fun provideText() = "Why, hello there!"
}

class MainActivity : AppCompatActivity() {

  @Inject lateinit var text: String

  override fun onCreate(savedInstanceState: Bundle?) {
    AndroidInjection.inject(this)
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    textView.text = text
  }
} 
```

假设我们的布局`activity_main`有一个名为`textView`的`TextView`，结果是一个简单的屏幕显示文本“为什么，你好！”

> 很好，但是现在怎么办？(还有我的咖啡呢？！)

很棒的问题。这让我想到...

# 测试您的活动

让我们假设字符串`"Why, hello there!"`可以是任何东西；它是动态生成的。也许它是由构建脚本提供的，或者是通过 API 调用获取的。我们不想在测试环境中依赖这些，不管怎样，我们已经对 API 代码进行了单元测试(对吗？).假设文本存在，我们只想验证我们的屏幕是否显示了一些文本。这里有一种方法可以实现这一点。

## 先加一个`DebugMainApplication`

```
class DebugMainApplication : MainApplication() {
  fun setTestComponent(component: MainApplicationComponent) {
    component.inject(this)
  }
} 
```

这用我们的定制测试组件替换了 prod 组件(见下文)。

并在`debug/AndroidManifest.xml` :
中创建清单的调试变体

```
<?xml version="1.0" encoding="utf-8"?>
<manifest
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.autonomousapps.daggerdotandroid">

    <application
        android:name="com.autonomousapps.daggerdotandroid.DebugMainApplication"
        android:label="@string/app_name"
        tools:ignore="AllowBackup,GoogleAppIndexingWarning"
        tools:replace="android:name,android:label"/>

</manifest> 
```

我们的测试代码现在将使用我们的特殊产品`DebugMainApplication`而不是我们的产品`MainApplication`。

## 编写你的测试

```
class MainActivityTest {

  // Rules are one place where Kotlin code is uglier and more verbose than Java
  @get:Rule private var activityTestRule = ActivityTestRule(
    MainActivity::class.java,
    false,
    false
  )

  // Here we define a test component that extends our production component
  @Component(modules = [
    // Inheriting components don't inherit annotations, so we need to re-declare
    // that we want to inject framework classes
    AndroidSupportInjectionModule::class,

    // A test module defined below
    TestMainActivityModule::class
  ])
  interface TestMainApplicationComponent : MainApplication.MainApplicationComponent {

    @Component.Builder
    interface Builder {
      @BindsInstance fun app(app: MainApplication): Builder

      // This is the fruit of all our labor. We can now provide our custom text
      // (or anything more interesting!) into our dependency graph
      @BindsInstance fun text(text: String): Builder
      fun build(): TestMainApplicationComponent
    }
  }

  // This is basically the mirror image of ScreenBindingModule, but instead of
  // providing an abstract function for EVERY screen, we only need to provide
  // one for the screens that will get injected in our test
  @Module abstract class TestMainActivityModule {
    @ContributesAndroidInjector abstract fun mainActivity(): MainActivity
  }

  @Before fun setup() {
    val app = InstrumentationRegistry.getTargetContext().applicationContext as DebugMainApplication
    val mainComponent = DaggerMainActivityTest_TestMainApplicationComponent.builder()
      .app(app)

      // Neat!
      .text("I'm a test!")
      .build()
    app.setTestComponent(mainComponent)

    activityTestRule.launchActivity(null)
  }

  @Test fun verifyText() {
    onView(withText("I'm a test!")).check(matches(isDisplayed()))
  }
} 
```

[![passing test](img/db36128ee41fe420603ea617d2d6e9ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V7yFO5xt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/f8pIVu9.png)

酷豆。顺便说一句，如果你对我们有大约一百多行测试设置代码而实际上只有一行感到困惑，嘿，欢迎来到 Android 测试的奇妙世界！加入我们，这很有趣。此外，一个真正的测试类会有不止一个测试，样板文件:测试代码的比率最终会变得合理。

# 碎片呢？

请继续关注这一页，因为还有更多的来了！我们将讨论注入片段，注入*保留的*片段的特殊考虑，我们甚至将编写一个用于注入`View` s 的定制类！(`View`——`dagger.android`不支持开箱即用的注射，原因将在后面说明。)我们还将看到如何合并 [ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel.html) s， [ViewModelProvider。工厂](https://developer.android.com/reference/android/arch/lifecycle/ViewModelProvider.Factory.html) s，甚至可能[定制范围](https://github.com/google/dagger/issues/721)....

# 本系列

1.  基本设置(这篇文章)
2.  [使用 Dagger 查看模型和 LiveData](https://dev.to/autonomousapps/dagger-dot-android-part-2-viewmodels-and-viewmodel-factories-10ii)
3.  *更多精彩即将到来...*

# 资源

1.  这里的所有代码都可以在我的 [Github repo](https://github.com/autonomousapps/DaggerDotAndroid) 上获得
2.  [保持匕首锋利](https://medium.com/square-corner-blog/keeping-the-daggers-sharp-%EF%B8%8F-230b3191c3f)，这教会了我一些关于瞄准镜的知识
3.  Android 与 Pierre-Yves Ricau 在 [Youtube](https://www.youtube.com/watch?v=2ICxQv9Ur60) 上的 5 集对话，解释了`@BindsInstance`、静态供应方法、范围等等。

[![coffee](img/3191ef1b2f2917ef57738efc1be89fb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LT-s4gEF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/j09P3hO.jpg)