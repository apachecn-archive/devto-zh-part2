# 不要用 lateinit 修饰符欺骗自己

> 原文：<https://dev.to/brightdevs/dont-fool-yourself-with-lateinit-modifier-mp1>

Kotlin 语言提供了一种简洁的方式来处理代码中的可空变量，因此不容易出错。当然，除非你完全做错了。

# lateinit 修饰符

通常，Kotlin 类中所有不可空的属性都必须正确初始化。你可以用几种方法来做:

*   在主构造函数中，
*   在初始化程序块中，
*   在类体中，
*   用吸气剂*，
*   委托*。

**这本身并不是初始化，但它让编译器意识到这不是`null`*

但是如果你的属性是生命周期驱动的(例如，引用一个在 Android 活动生命周期中膨胀的按钮)或者它是通过注入初始化的，你不能提供一个非空的初始化器，你必须声明它的类型为可空。这反过来将要求您在每次引用该属性时使用空检查，这可能有点不方便，尤其是如果您绝对确定该属性将在您第一次访问它之前的某个时间点被初始化。

对于这种情况，Kotlin 有一个简单的解决方案，允许您用`lateinit`修饰符标记属性。由于这个原因，你可以拥有一个不可空的类型，这样你就不必在引用它的时候检查它是否是`null`。当然，如果您在初始化之前访问该属性，您将获得一个`UninitializedPropertyAccessException`。

# Lateinits vs nullables

尽管所描述的可能性是一个很好的特性，但也可能会在初始化不确定的地方被过度使用(例如，有条件的或只是*太晚了*)，使得代码更不知道空值，更不可预测，更像 Java，因此，自然地，你可能会怀疑某些用法是否正确。所以这是我的经验法则，当我决定一个特定的变量是应该`lateinit`还是可以空的时候，我会用到它。这是非常主观的，它会随着时间的推移而改变，所以请在评论中分享你自己的经历。

## Lateinit #1:生命周期的开始

当类的生命周期开始方法被调用时，你可以初始化属性，例如`Activity.onCreate()`。如果你使用像 [Dagger](https://google.github.io/dagger/) 这样的依赖注入框架，这是很常见的情况。

```
abstract class BaseActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        AndroidInjection.inject(this)
        super.onCreate(savedInstanceState)
    }
}

class LoginActivity : BaseActivity() {

    @Inject
    lateinit var viewModelFactory: ViewModelProvider.Factory

    lateinit var viewModel: LoginViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        viewModel = ViewModelProviders.of(this, viewModelFactory).get(LoginViewModel::class.java)

        setContentView(R.layout.login_activity)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想`getSystemService()` :
也很有用

```
class MainActivity : AppCompatActivity() {

    lateinit var alarmManager: AlarmManager

    // This wouldn't work:
    // val alarmService = getSystemService(AlarmManager::class.java)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        alarmManager = getSystemService(AlarmManager::class.java)
} 
```

Enter fullscreen mode Exit fullscreen mode

否则，您将得到一个`IllegalStateException`，表示系统服务在`onCreate()`之前不可用于活动。

为了线程安全 :
，您还可以在对象构造[期间避免`this`引用转义](https://wiki.sei.cmu.edu/confluence/display/java/TSM01-J.+Do+not+let+the+this+reference+escape+during+object+construction)

```
class MyActivity : AppCompatActivity() {

    lateinit var alien: Alien
    // instead of:
    // val alien = Alien(this)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        alien = Alien(this)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Lateinit #2:快速失效方法

您可能更喜欢编写一个旨在尽快失败并停止正常操作的代码，而不是试图继续一个有缺陷的过程。一个极端的例子可能是让应用崩溃，这样你就可以及早发现故障。在代码简单明了且可预测的情况下，这尤其有用。

例如，让我们想象一个使用`MediaPlayer`播放音乐的活动。可能是这样的:

```
class PlayerActivity : AppCompatActivity() {

    lateinit var mediaPlayer: MediaPlayer
    lateinit var mediaUri: Uri

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_player)
        mediaUri = intent.getParcelableExtra("mediaUri")
    }

    override fun onStart() {
        super.onStart()
        mediaPlayer = MediaPlayer()
        mediaPlayer.setDataSource(this, mediaUri)
        mediaPlayer.prepare()
        mediaPlayer.start()
    }

    override fun onStop() {
        super.onStop()
        mediaPlayer.stop()
        mediaPlayer.release()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

很容易在`onCreate`中发现一个有风险的赋值，因为从传递的 intent 中检索`mediaUri`可能会以一个异常结束，如果 intent 不包含它的话。

`java.lang.IllegalStateException: intent.getParcelableExtra("mediaUri") must not be null`

另一方面，代码清晰简单，测试起来并不困难。但是如果你觉得你的具体情况需要一些解释，那么我鼓励你记录这样的设计决策。

[![Risk inside](img/439f91a0eac85017734369fdb6a1601c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hhIshcT2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c4rgadokwclqmgsfrs75.jpg)

## 可空#1:无崩溃方法

可能会有这样的情况，你想不惜一切代价避免应用程序崩溃，即使有些东西不能正常工作。这并不意味着像前面的“fail-fast”例子中那样使用`lateinit`一定会以崩溃告终，因为你可以捕捉异常，但是我认为在这两个极端之间进行选择是很常见的:要么让应用程序崩溃并且不介意捕捉异常，要么使用可空变量来避免崩溃。

让我们修改前面的例子，将`lateinit`替换为可空类型:

```
class PlayerActivity : AppCompatActivity() {

    var mediaPlayer: MediaPlayer? = null
    var mediaUri: Uri? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_player)
        mediaUri = intent.getParcelableExtra("mediaUri")
    }

    override fun onStart() {
        super.onStart()

        mediaUri?.let {
            val player = MediaPlayer()
            player.setDataSource(this, it)
            player.prepare()
            player.start()
            mediaPlayer = player
        }
    }

    override fun onStop() {
        super.onStop()
        mediaPlayer?.stop()
        mediaPlayer?.release()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这一次，如果我们没有得到任何`mediaUri`，我们就不播放音乐。应用程序不会崩溃，但用户会对这种沉默感到惊讶(他甚至可能会想他的手机是不是坏了)。比崩溃好吗？那是你的选择。但是这种方法的好处是，您有机会使用常规的`if`语句实际测试变量，如果变量没有用非空值初始化，您还可以做其他事情。

*注意:从 Kotlin 1.2 开始，你可以在对`lateinit`属性的引用上使用`.isInitialized`，但是这可能会使你的代码更加不可读，并且它有一些限制(参见[文档](https://kotlinlang.org/docs/reference/properties.html) ):*

> 此检查仅适用于可按词法访问的属性，即在同一类型或外部类型之一中声明的属性，或者在同一文件的顶层声明的属性。

## 可空#2:非常晚的初始化

我见过一段代码，它在一个`lateinit`变量中“记住”一个被点击的列表项数据，用`startActivityForResult()`开始另一个活动，最后，当它完成并且调用了`onActivityResult()`时，“记住的”值被读取和使用(我很惊讶它在此期间没有丢失)。

```
class SomeActivity : AppCompatActivity() {

    lateinit var selectedItemData: ItemData

    // a ton of code ...

    private fun doSomethingWithItem(data: ItemData) {
        // another ton of code ...

        if (...) { // long and complex conditions
            if (...) {
                data?.anotherData?.let { // may not happen and won't let you know
                    selectedItemData = data
                    startActivityForResult(...)
                }
            }
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        doSomethingElse(selectedItemData)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，我避免这样的构造，我称之为“太晚初始化”。在这种情况下，我更喜欢使用可空类型，以清楚地表明变量在大多数时候是`null`。

# 如何不愚弄自己？

如果使属性可为空更有意义并提高了空安全性，则使该属性可为空。如果使用得当，它可能是好的，但也有不好的一面。不要让它成为`NullPointerException`的替代品。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Andrzej Zabost，光明发明
[博客](https://azabost.com/)，[电子邮件](//andrzej.zabost@brightinventions.pl)的安卓开发者