# 和 MVVM 一起开发安卓应用

> 原文：<https://dev.to/adammc331/building-an-android-app-with-mvvm-539c>

原载于[安卓精华](https://androidessence.com/android/building-an-app-with-mvvm/)。

这是正在进行的系列文章中的第二篇，展示用于 Android 开发的几种不同的架构模式。通过跟踪[这个报告](https://github.com/AdamMc331/todo-monorepo)，你可以找到它们中每一个的代码，这些代码经常出现在博客文章之前。给它一颗星！

在我们的[上一篇文章](https://dev.to/adammc331/building-an-android-app-with-mvp-k2a)中，我们讨论了构建应用的 MVP 架构。这次，我们要去看看 MVVM。

# MVVM

MVVM 代表模型-视图-视图模型。这个名字，就像任何其他架构一样，来自于相关的组件。我将像上一篇文章一样对它们进行分解(事实上，模型和视图已经被复制和粘贴了):

## 型号

在这个上下文中，模型不一定指你写的模型类。我更愿意把它看作是您的应用程序的数据源。这可能是一个数据库、一个远程服务器，甚至只是您提供的虚拟数据。通常，你会看到开发人员将代码放在某种`Repository`类中。这就是我们所说的模型。

## 视图

视图是负责任何 UI 工作的组件。这包括显示数据和处理点击事件。它不包含那些点击事件的业务逻辑，我将在下一段中阐明。在许多情况下，您将拥有一个表示视图的活动或片段。

## viewmode

视图模型是一个棘手的术语。这是因为它可以指两件事。这里有一个视图模型，特指架构模式，还有一个视图模型指的是 [Android 架构组件](https://developer.android.com/topic/libraries/architecture/viewmodel)。然而，它们确实是紧密相连的，我希望我能解释其中的原因。

当我们谈到 MVVM 架构时，视图模型是负责维护状态、与模型交互以及任何相关业务逻辑的组件。

Android 类与此直接相关。这是一个在整个定向过程中维护状态的类，这在历史上是 Android 上的一个棘手问题。所以，如果你正在使用 MVVM 架构，拥有一个可以做到这一点的类会很有帮助。

# 沟通流程

现在我们已经定义了组件，让我们深入 MVVM 通信流程。我们将更进一步，将它与我们的最后一个例子 MVP 进行比较。

[![](../Images/b4c6344c0b88693437753a681e7b47d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SPgnJd9A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r90y8hqzp5s558j1snza.png)

这张图片来自维基百科。

如果你看了上一篇文章，你可能会觉得这种交流模式非常类似于 MVP。值得注意的是，在任何一种情况下，模型和视图都不会相互交流。这就是最大的好处。*MVVM 和 MVP 的区别在于沟通流程。MVP 具有双向通信，但 MVVM 使用单向数据流。*

我推荐这篇由 [David Street](https://www.exclamationlabs.com/blog/the-case-for-unidirectional-data-flow/) 撰写的好文章来理解单向数据流的好处。TL；当交流只在一个方向流动时，它是可预测的，并且没有副作用。

现在，让我们开始用这个架构构建我们的待办事项应用程序。

# 模型

我们在两个项目之间的模型在这里实际上没有改变。在上一个例子中，它必须从我们的 contract 类扩展，但是在这里，我们可以有一个独立的存储库来使用虚拟数据。

```
 open class TaskRepository {
        open fun getItems(): List<BaseTask> {
            return listOf(
                    BaseTask("Sample task 1"),
                    BaseTask("Sample task 2"),
                    BaseTask("Sample task 3"),
                    BaseTask("Sample task 4"),
                    BaseTask("Sample task 5"),
                    BaseTask("Sample task 6"),
                    BaseTask("Sample task 7"),
                    BaseTask("Sample task 8"),
                    BaseTask("Sample task 9"),
                    BaseTask("Sample task 10")
            )
        }
    } 
```

# viewmode

在展示 ViewModel 的代码之前，我想解释一下它需要承担的责任。最重要的是:与 MVP 中的 presenter 不同，ViewModel 应该绝对没有对视图的引用。那么，视图如何知道什么时候做什么呢？视图模型将通过视图可以订阅的 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 公开该信息。也就是说，我们需要以下内容:

1.  我们的视图模型将引用我们的`TaskRepository`来获取任务。
2.  这些任务将通过 LiveData 公开。
3.  当我们添加完任务返回时，视图模型应该检索任务并通过 livedata 公开新任务，这样视图就可以更新适配器。
4.  当单击 add 按钮时，ViewModel 应该通过 LiveData 公开某种方式，让视图知道它必须导航到 add task 屏幕。

考虑到所有这些，我们以下面的 ViewModel 代码结束:

```
 class TaskListViewModel(private val repository: TaskRepository) : ViewModel() {
        val tasks = MutableLiveData<List<BaseTask>>()
        val newTask = MutableLiveData<BaseTask>()
        val navigationAction = MutableLiveData<NavigationAction>()

        fun getTasks() {
            if (tasks.value == null) {
                tasks.value = repository.getItems()
            }
        }

        fun returnedFromAddTask(data: Intent?) {
            val description = data?.getStringExtra(AddTaskActivity.DESCRIPTION_KEY).orEmpty()
            val taskFromIntent = BaseTask(description)
            newTask.value = taskFromIntent
        }

        fun addButtonClicked() {
            navigationAction.value = NavigationAction.ADD_TASK
        }
    } 
```

同样，这里需要注意的关键点是 ViewModel 与视图没有任何联系。现在的好处是，如果我们需要，多个视图可以引用这个视图模型的一个实例。

旁注:`NavigationAction`只是我做的一个枚举，这样我们的视图模型就不必处理每一条可能的导航路线，而只需发出一个视图可以监听并相应处理的动作。我们接下来会看到。

# 视图

与上一个例子类似，我们的视图只是指活动或片段。在我们看代码之前，我将强调几件事:

1.  具有构造函数的视图模型必须使用 [ViewModelProvider 创建。工厂](https://developer.android.com/reference/android/arch/lifecycle/ViewModelProvider.Factory)。
2.  一旦创建了 ViewModel，视图将观察我们创建的三个 LiveData 并做出相应的反应。

```
 class TaskListActivity : BaseTaskListActivity() {
        private val adapter = BaseTaskAdapter()
        private lateinit var viewModel: TaskListViewModel

        private val viewModelFactory = object : ViewModelProvider.Factory {
            override fun <T : ViewModel?> create(modelClass: Class<T>): T {
                val repository = TaskRepository()
                val viewModel = TaskListViewModel(repository)

                @Suppress("UNCHECKED_CAST")
                return viewModel as T
            }
        }

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

            setupViewModel()
            initializeRecyclerView()
            initializeFAB()

            viewModel.getTasks()
        }

        override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
            super.onActivityResult(requestCode, resultCode, data)

            if (requestCode == ADD_TASK_REQUEST && resultCode == Activity.RESULT_OK) {
                viewModel.returnedFromAddTask(data)
            }
        }

        private fun setupViewModel() {
            viewModel = ViewModelProviders.of(this, viewModelFactory).get(TaskListViewModel::class.java)

            viewModel.tasks.observe(this, Observer {
                it?.let(adapter::tasks::set)
            })

            viewModel.newTask.observe(this, Observer {
                it?.let { task ->
                    adapter.tasks += task
                }
            })

            viewModel.navigationAction.observe(this, Observer {
                when (it) {
                    NavigationAction.ADD_TASK -> navigateToAddTask()
                }
            })
        }

        private fun initializeRecyclerView() {
            taskList.adapter = adapter
            taskList.layoutManager = LinearLayoutManager(this)
        }

        private fun initializeFAB() {
            fab.setOnClickListener {
                viewModel.addButtonClicked()
            }
        }

        private fun navigateToAddTask() {
            val intent = Intent(this, AddTaskActivity::class.java)
            startActivityForResult(intent, ADD_TASK_REQUEST)
        }

        companion object {
            private const val ADD_TASK_REQUEST = 0
        }
    } 
```

# 更

要查看这个 MVVM 例子的完整代码，您可以在 [GitHub](https://github.com/AdamMc331/todo-monorepo/tree/master/todo-mvvm) 上找到它！