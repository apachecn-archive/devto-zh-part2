# Dagger-Dot-Android 第 4 部分:后期绑定值

> 原文：<https://dev.to/autonomousapps/dagger-dot-android-part-4-late-binding-values-2de0>

1.  [第 1 部分—基本设置](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-33kj)
2.  [第二部分—视图模型和视图模型工厂](https://dev.to/autonomousapps/dagger-dot-android-part-2-viewmodels-and-viewmodel-factories-10ii)
3.  [第 3 部分—片段](https://dev.to/autonomousapps/the-daggerandroid-missing-documentation-part-3-fragments-12go)
4.  这部分

如果你还没有读过这篇教程的其他部分，请考虑看看。

在这一部分，我将简要描述一个将对象后期绑定到图中的巧妙技巧。

# 用例

假设您有一对活动，单击其中一个按钮将启动第二个。在第一个屏幕上有一系列按钮，每个按钮代表您存储在数据库中的数据。第二个活动如何知道您按了哪个按钮，以及要显示哪些数据？很简单，对吧？你只需在你的`Intent`中传递一个参数。但是如果你是一个现代的 T2 开发人员，你关心关注点分离和单一责任原则，并且你不能直接在你的活动中将按钮 ID 交给一些 DAO，那该怎么办呢？

在本文中，我们将假设您的`Activity`类有一个`ViewModel`来协调对数据库层的访问。您希望将该 ID 放入 viewmodel，但是您不希望在其上调用 setter。您真正想要的是让 VM“知道”它需要哪些数据来正确地填充视图，并且越早越好。让我们看看如何能做到这一点。

# 客户端活动

```
class ClientActivity : AppCompatActivity {

  companion object {
    fun getStartIntent(context: Context, id: Long): Intent {
      return Intent(context, ClientActivity::class.java).apply {
        putExtra(EXTRA_ID, id)
      }
    }
  }

  internal val id by lazy { intent.extras.getLong(EXTRA_ID) }

  @Inject lateinit var factory: ClientActivityViewModelFactory

  // Our goal is for this view model to know about `id` at construction time
  private val viewModel by lazy { 
    ViewModelProviders.of(this, factory).get(ClientActivityViewModel::class.java)
  }

  override fun onCreate(savedInstanceState: Bundle?) {
    AndroidInjection.inject(this)
    super.onCreate(savedInstanceState)

    viewModel.stuff().observe(this, Observer {
      // presumably, `stuff()` returns LiveData that has something to do with our ID
    })
  }
}

@Module object ClientActivityModule {
  // Waaaaat. Now our ID, which is only know at activity.onCreate, is bound
  @JvmStatic @Provides @Id fun provideId(activity: ClientActivity): Long = activity.id
}

// This is only necessary if you bind other Longs in your Dagger graph
@Qualifier
@Target(AnnotationTarget.FUNCTION, AnnotationTarget.VALUE_PARAMETER)
@Retention(AnnotationRetention.RUNTIME)
annotation class Id 
```

# 视图模型和工厂

```
class ClientActivityViewModel(
  private val id: Long // our late-bound ID!
) : ViewModel() { 

  // interact with persistence layer using the injected ID
}

class ClientActivityViewModelFactory @Inject constructor(
  private val id: Long
) : ViewModelProvider.Factory {

  @Suppress("UNCHECKED_CAST")
  override fun <T : ViewModel> create(modelClass: Class<T>): T = when {
    modelClass.isAssignableFrom(ClientActivityViewModel::class.java) -> {
      ClientActivityViewModel(id) as T
    }
    else -> {
      throw IllegalArgumentException("${modelClass.simpleName} is an unknown type")
    }
  }
} 
```

这里没有魔法。我们只是做了一些有点不标准的事情，将一个活动的属性公开暴露给我们的依赖图。另一种选择可能是类似于`viewModel.setId(id)`的东西，确切地说，它并没有*差*，但也没有那么有趣；-)