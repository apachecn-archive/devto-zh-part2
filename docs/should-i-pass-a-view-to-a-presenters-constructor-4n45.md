# 我应该将视图传递给演示者的构造函数吗？

> 原文：<https://dev.to/chrisvasqm/should-i-pass-a-view-to-a-presenters-constructor-4n45>

<center>Cover image by [Ben White](https://unsplash.com/@benwhitephotography) from [Unsplash](https://unsplash.com)</center>

* * *

我已经看到了如何在 Android 上实现 MVP(模型-视图-演示者)模式的多种变化，其中一件引起我注意的事情是演示者视图的设置或初始化方式因作者而异。

以下是我所说的一个例子:

假设我们有一个`LoginActivity`。在它的`onCreate`方法中，我们将必须设置呈现者，并传入对`LoginActivity`的引用，这样它就可以操纵它。

但是，在此之前，我们应该让我们的`LoginActivity`实现一个接口，姑且称之为`LoginView`，它将是我们的`LoginPresenter`将要使用的对象的类型。

## 演示者示例 1

首先，我们创建一个基本的 presenter 接口，它将被其他特定的 presenter 类使用:

```
interface Presenter<V> {
    fun attach(view: V)
    fun detach()
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在我们的`LoginPresenter` :
中实现它

```
class LoginPresenter : Presenter<LoginView> {

    private var view: LoginView? = null

    override fun attach(view: LoginView) {
        this.view = view
    }

    override fun detach() {
        view = null
    }

    // ...

} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们有自己的`attach()`和`detach()`方法，所以我们的`LoginActivity`应该是这样的:

```
 class LoginActivity : AppCompatActivity(), LoginView {

    private lateinit var presenter: Presenter<LoginView>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        presenter = LoginPresenter()
        presenter.attach(this)
    }

    override fun onDestroy() {
        super.onDestroy()
        presenter.detach()
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，因为我在这个例子中使用了 Kotlin，所以我们可以使用`apply`方法，让它从这个:

```
presenter = LoginPresenter()
presenter.attach(this) 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
presenter = LoginPresenter().apply { attach(this@LoginActivity) } 
```

Enter fullscreen mode Exit fullscreen mode

这很有帮助，但是...

我对这种方法的问题是，如果出于某种原因，开发人员忘记使用`attach()`方法，将会面临意想不到的结果，这可能会导致令人沮丧的几分钟/几小时试图找到 bug。

可能是也可能不是几天前的我😅

因此，为了防止这种情况，我认为最好采取以下措施:

## 演示者示例 2

我们可以从`Presenter`接口中移除`attach()`方法，这将留给我们:

```
interface Presenter<V> {
    fun detach()
} 
```

Enter fullscreen mode Exit fullscreen mode

由于`V`没有被使用，我们也可以删除它。

```
interface Presenter {
    fun detach()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我想用`-able`命名约定来重命名这个接口，命名约定是:

```
interface Detachable {
    fun detach()
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这种命名约定的接口示例有:`Runnable`、`Serializable`、`Readable`和`Parceable`。

这种变化也要求我们将
改为`LoginPresenter`

```
class LoginPresenter : Presenter<LoginView> {

    private var view: LoginView? = null

    override fun attach(view: LoginView) {
        this.view = view
    }

    override fun detach() {
        view = null
    }

    // ...

} 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
class LoginPresenter(private var view: LoginView?) : Detachable {

    override fun detach() {
        this.view = null
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在我们剩下要做的就是修改我们的`LoginActivity`代码

```
presenter = LoginPresenter()
presenter.attach(this) 
```

Enter fullscreen mode Exit fullscreen mode

还是这个:

```
presenter = LoginPresenter().apply { attach(this@LoginActivity) } 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
presenter = LoginPresenter(this) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们整个`LoginActivity`类应该是:

```
class LoginActivity : AppCompatActivity(), LoginView {

    private lateinit var presenter: LoginPresenter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        presenter = LoginPresenter(this)
    }

    override fun onDestroy() {
        super.onDestroy()
        presenter.detach()
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不喜欢`lateinit`属性的人:

```
class LoginActivity : AppCompatActivity(), LoginView {

    private val presenter = LoginPresenter(this)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    override fun onDestroy() {
        super.onDestroy()
        presenter.detach()
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你我都可以确保我们的`LoginPresenter`在使用的时候，总是处于*有效状态*。

* * *

我可能忽略了示例 2 方法中的一些东西，这就是为什么我想知道您的想法:)