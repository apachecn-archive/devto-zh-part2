# 重新审视 Android 视图模型注入

> 原文：<https://dev.to/brightdevs/android-viewmodel-injections-revisited-4efi>

在我们之前的一篇文章中，我们已经描述了如何实现一个 ViewModel 工厂，它能够提供注入了依赖关系的 ViewModel，例如一个 API 客户端，这在当时对我来说已经足够好了。后来，多亏了 [Piotr](https://miensol.pl/) ，我们找到了更好更简单的方法，还可以将活动或片段相关的数据注入视图模型。

[![Vaccine](img/82637e7e7150bf7990ab193b212a1cc6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YwnPzEXQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mdhj3sbe21dxobzwi6mm.jpg)

# 更简单的工厂

之前，我们已经创建了一个 singleton factory，它提供了一个基于`ViewModel`的类和它们各自的`Provider`的映射。它要求我们创建一个自定义的`ViewModelKey`注释，并使用 Dagger 通过`IntoMap`绑定来生成映射。与我当时看到的一些其他解决方案相比，它不需要很多样板代码，但也不是完美的。

相反，新的解决方案基于一个通用的 ViewModel 工厂类，它的实例是为每个活动或片段实例创建的。

```
import android.arch.lifecycle.ViewModel
import android.arch.lifecycle.ViewModelProvider
import dagger.Lazy
import javax.inject.Inject

class ViewModelFactory<VM : ViewModel> @Inject constructor(
    private val viewModel: Lazy<VM>
) : ViewModelProvider.Factory {

    @Suppress("UNCHECKED_CAST")
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return viewModel.get() as T
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如(完整代码见[此处](https://github.com/azabost/simple-mvvm-example) ):

```
class MainViewModel @Inject constructor(
    private val apiClient: ApiClient
) : ViewModel() {
    // ...
}

class MainActivity : BaseActivity() {

    @Inject
    lateinit var vmFactory: ViewModelFactory<MainViewModel>

    lateinit var vm: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        vm = ViewModelProviders.of(this, vmFactory)[MainViewModel::class.java]

        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，代码少了很多，我个人认为它也更容易理解。为了更加简洁，我们可以在`BaseActivity`类中添加一个扩展函数，如下所示:

```
abstract class BaseActivity : AppCompatActivity() {
    // ...

    inline fun <reified T : ViewModel> ViewModelFactory<T>.get(): T =
        ViewModelProviders.of(this@BaseActivity, this)[T::class.java]
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以得到一个 ViewModel，只需:`vm = vmFactory.get()`

类似地，我们可以为片段添加一个类似的函数。

# 更多可能性

我们遇到的一个问题是，持有 ViewModel 提供者映射的 singleton factory 范围很广，因此它不允许我们注入来自更窄范围的任何内容，例如 Activity 的 extras 或 Fragment 的参数。

每次创建一个新工厂都使之成为可能。为了实现这一点，我们需要一个额外的模块，知道如何获得依赖关系。例如:

```
import com.azabost.simplemvvm.net.response.RepoResponse
import dagger.Module
import dagger.Provides

@Module
class RepoActivityIntentModule {
    @Provides
    fun providesRepoResponse(activity: RepoActivity): RepoResponse {
        return activity.intent.getSerializableExtra(RepoActivity.REPO_RESPONSE_EXTRA) as RepoResponse
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个模块必须添加到由`ContributesAndroidInjector`注释:
生成的相应的`RepoActivity`子组件中

```
import com.azabost.simplemvvm.ui.main.MainActivity
import com.azabost.simplemvvm.ui.repo.RepoActivity
import com.azabost.simplemvvm.ui.repo.RepoActivityIntentModule
import dagger.Module
import dagger.android.ContributesAndroidInjector

@Module
abstract class AndroidInjectorsModule {
    @ContributesAndroidInjector
    abstract fun contributeMainActivity(): MainActivity

    @ContributesAndroidInjector(modules = [RepoActivityIntentModule::class])
    abstract fun contributeRepoActivity(): RepoActivity
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，当我们在`RepoActivity`中得到我们的`RepoViewModel`时，它有来自已经注入的意图的数据:

```
class RepoViewModel @Inject constructor(
    val repoResponse: RepoResponse
) : ViewModel()

class RepoActivity : BaseActivity() {

    @Inject
    lateinit var vmFactory: ViewModelFactory<RepoViewModel>

    lateinit var vm: RepoViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_repo)

        vm = ViewModelProviders.of(this, vmFactory)[RepoViewModel::class.java]

        repoData.text = vm.repoResponse.id.toString()
    }

    companion object {
        const val REPO_RESPONSE_EXTRA = "REPO_RESPONSE_EXTRA"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Andrzej Zabost，光明发明
[博客](https://azabost.com/)，[电子邮件](//andrzej.zabost@brightinventions.pl)的安卓开发者