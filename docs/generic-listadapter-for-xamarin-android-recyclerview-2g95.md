# Xamarin 的通用 ListAdapter。Android 回收视图

> 原文：<https://dev.to/jpda/generic-listadapter-for-xamarin-android-recyclerview-2g95>

我先声明我对 Android(或一般的移动设备)开发一无所知。我现在已经使用 Xamarin 大约 7 个小时了，我认为它很棒，但我唯一的目标是获得一个应用程序的存根，作为实际工作的客户端，这是一堆 Azure 的东西。这可能对某些人有用，或者对我来说这可能是一个完全的催款-克鲁格时刻，只有时间会告诉我们。小心行事。

在阅读了一些关于 ListViews 的内容后，我发现了关于[recycle view](https://blog.xamarin.com/recyclerview-highly-optimized-collections-for-android-apps/)的内容，它有一些很酷的视窗管理功能，可以在将即将进入视窗的新项目排队时，为你已经滚动过的项目回收内存。

[![Image from Xamarin blog post [here](https://blog.xamarin.com/recyclerview-highly-optimized-collections-for-android-apps/)](../Images/e214d89525745bd18c783582e0f31cd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i9XONi0P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpda.dev/img/1%2520__rNlpuO__%2520byTAYObhua4IN9w.png) 图片来自 Xamarin 博文[此处](https://blog.xamarin.com/recyclerview-highly-optimized-collections-for-android-apps/)

总之，我的存根应用程序有两种集合类型，联系人和对话。这很简单，但是当我在处理适配器→视图持有器时，这看起来有些重复。

### 我们开始吧

我浏览了 Xamarin 的博文，根据我的需要和类型修改了那里的内容。我有:

*   [联系人](https://gist.github.com/jpda/4ccc9cf61210970753925262eca42954#file-contact-cs)——我的实际实体，在这里是`Name`和`Email`
*   [contacts list row](https://gist.github.com/jpda/4ccc9cf61210970753925262eca42954#file-contactlistrow-axml)—axml 项目模板标记
*   [ContactsAdapter](https://gist.github.com/jpda/4ccc9cf61210970753925262eca42954#file-contactsadapter-cs) —实际放大视图并绑定数据的适配器
*   [ContactAdapterViewHolder](https://gist.github.com/jpda/4ccc9cf61210970753925262eca42954#file-contactadapterviewholder-cs)—一个 Holder 对象，它保存对`RecyclerView`模板中视图的引用(例如，`TextView`

当我开始为不同的集合复制这个时，我突然意识到这实际上是许多相同的代码。

如果我们看一下联系章节，我们会看到很多相同的东西:

*   我们产品的一般集合
*   将`ViewHolder`链接到布局
*   将实体项目绑定到`ViewHolder`

真的就这些了。看一看:

请注意，这里并没有太多具体的东西。让我们看看可重复使用的:

我们唯一需要知道的具体信息是

*   `T` —集合类型
*   `V` —取景器的类型
*   `Action<T,V>` —获取我们的项目和视图并将它们绑定的方法

让我们看看使用情况——你会注意到我们实际上只是将一些代码放到了不同的地方。这是我们在类似 MainActivity.cs 中的用法。我们的绑定逻辑已经转移到匿名方法，我们明确地告诉我们的`ListAdapter`我们的集合和我们的`ViewHolder`的类型。

到目前为止，我认为 Xamarin 相当不错。我不认为职业会很快转向移动开发，但这至少让事情变得容易多了！更多内容将在我们即将上线的蓝色部分中提供。