# 我选择 MVVM +流量控制器的动机

> 原文：<https://dev.to/brightdevs/my-motives-for-mvvm--flowcontrollers-path-1im5>

[![Header](../Images/f103698532513e2a7793cf6f089087a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RtauXh4O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9da7d7394bxh32nplqz6.jpeg)

构建 iOS 应用程序有许多不同的方法。MVC(模型视图控制器)、MVP(模型视图呈现器)、MVVM(模型视图视图模型)、VIPER(视图交互器呈现器实体路由)、Redux...当然，你还可以举出许多其他的例子。我写 iOS 应用程序已经有一段时间了，我注意到 MVVM +流量控制器的方法对我来说非常有效。在这篇文章中，我想重点谈谈“为什么？”我用它来代替其他方法和“如何？”我在应用程序中使用它。

# 为什么？

## 开头

开始 iOS 开发通常与学习基本模式有关，你可以用这些模式来解决你在努力创建应用程序中的第一个屏幕时会遇到的问题。最有可能照顾到你的视图和它们背后的逻辑的第一个模式是 MVC，它在 iOS 开发者中非常流行(但不仅仅是他们)。这主要是由于苹果公司在`UIKit`框架和代码示例中大力推广这种模式，你可以在他们的教程中找到。

## MVC 不好吗？

是啊！这是最糟糕的！它会导致大量的视图控制器出现在你的应用程序上...玩笑归玩笑...

不，不是的。当我最近读了帖子[“关于 iOS 应用架构的许多麻烦”](http://aplus.rs/2017/much-ado-about-ios-app-architecture/)时，我真的很高兴。我不能说我完全同意那里所说的一切，但有些部分我肯定能认同。为什么我很高兴读到这个？因为这句话:

“没有人强迫您在一个控制器中实现多个数据源。在 viewDidLoad 中启动网络调用。在 UIViewController 中解析 JSONs。到带有单例实例的硬连接视图。

许多开发人员指责 MVC 的糟糕和混乱，事实上这是开发人员自己造成的。如果你缺乏纪律，那么即使是毒蛇也不会帮助你。某些模式可能会使正确构建代码和保持代码整洁变得更容易，但是否遵守纪律始终取决于您。

如果最后没那么差我该不该用 MVC？如果我是一个熟练的顾问，我可能会说“看情况”。显然，MVC 有它自己的优点和缺点，我敢打赌，你可以找到许多伟大的文章，这将有助于你下定决心。

## 为什么是 MVVM？

如果 MVC 没有那么糟糕，那么我遵循 MVVM 道路的动机是什么？仅举几个例子:

*   它允许我将大部分代码远离`UIKit` ( [提供了更快测试这些代码的能力——就像 macOS 框架](https://eliaszsawicki.com/are-your-views-dumb-enough/))。
*   它允许我更容易地测试驱动我的视图的逻辑。
*   它与反应式编程方法配合得非常好(但是你不需要使用它们来受益于 MVVM)

所以现在，在采用了 MVVM 模式的原则之后，我能够拥有我的被动视图(尽可能的愚蠢)和驱动我的视图的逻辑，这也是从 UIKit 中分离出来的。

为什么不进一步采用像 VIPER 这样的模式非常适合的划分方法呢？良好的...你能做到的。我以前从未在大型项目中使用过 VIPER(我很高兴听到您对此的看法！)，但是，我认为这种模式对于中小型应用程序来说是一种简单的过度杀伤。我觉得，如果你想让你的解决方案易于理解，同时又能轻松测试你的代码，并与你的代码中依赖于 UIKit 的部分很好地分离开来，那么 MVVM 确实很好用。

## 流量控制器

使用它们的动机是什么？当在 view controller“A”中发现负责转换到屏幕“B”的代码时，这并不是什么不寻常的事情。这种方法的一个问题是，您突然在这两个实体之间创建了一个紧密的耦合，如果您想在其他上下文中拆分或重用它们，您可能会发现自己遇到了麻烦。
井
...问一个问题总是好的——这是一个问题吗？如果你正在做一些简单的事情，那么使用这种导航可能完全没问题，但是，如果你正在做一些更复杂的事情——添加一个流量控制器肯定会有帮助。请记住，添加流量控制器并不是一项高成本的任务——我倾向于在简单的应用程序中使用它们，因为它们帮助我更好地组织代码，并让我清楚地了解应用程序中的导航是如何工作的。将流量控制器添加到您的应用程序将有助于您:

*   保持你的屏幕相互分离，这将允许你很容易地模块化和重用它们。
*   在你的应用程序中控制流量(你可能会有许多不同的流量控制器)
*   为你的`ViewModels`注入依赖

## 训导

一个纯粹的事实是，你开始使用 MVVM 和`FlowControllers`不会立即使你的代码库干净。你猜怎么着？还是可以用`Massive ViewModels`结束的！能否把自己管紧，把代码组织好，就看你自己了。

# 如何？

## MVVM

好的，那么我如何使用 MVVM 呢？我如何保持我的`ViewModels`干净？我如何使用`FlowControllers`？跳上车，让我们看一个快速的例子，让我们看看这个概念在使用中。

我不得不承认，当我第一次涉足 iOS 开发的时候，我认为`ViewModel`是一个拥有视图显示的值的对象。当时我并不认为使用`ViewModels`有多大价值。改变我观点的是你可以在[微软模式和实践](https://msdn.microsoft.com/en-us/library/hh848246.aspx)中读到的方法。对我来说，核心信息是微软方法中的`View`层被表示为 XAML(引用:“具有不包含业务逻辑的有限代码隐藏”)。好的...那么这是否意味着`ViewModel`不仅仅是持有我们的观点所代表的价值观呢？它还能包含驱动这些观点的逻辑吗？是啊！遵循 iOS 上的这种方法，它鼓励你保持你的视图层简单(在 MVVM 的情况下将是`UIView`和`UIViewController`)，并将逻辑移到`ViewModel`。这是让我们达到更好的可测试性并远离`UIKit`的第一步。

让我们一步一步地看一个和`FlowController`一起使用的 MVVM 模式的快速例子。

先说`AppDelegate`。这是什么？

```
 class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
    var mainNavigationController: UINavigationController!
    var mainFlowController: MainFlowController!

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

        mainNavigationController = UINavigationController()

        window = UIWindow(frame: UIScreen.main.bounds)
        window?.rootViewController = mainNavigationController
        window?.makeKeyAndVisible()

        mainFlowController = MainFlowController(rootNavigationController: mainNavigationController)
        mainFlowController.startFlow()

        return true
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

第一件重要的事情是我创建了我的`MainFlowController`,它将负责控制我的应用程序流。我在这里传递的唯一依赖项是我的主导航控制器，它是我的主窗口的根控制器。我也见过直接将`UIWindow`传递给流量控制器的不同方法，但是，如果你不需要它，那么我更喜欢这个“更轻”的对象，它有更明确的职责。

那`MainFlowController`本身呢？

```
class MainFlowController {

    private let rootNavigationController: UINavigationController

    private lazy var entryViewModel: EntryViewModel = {
        let fetcher = FakeUserFetcher()
        let viewModel = EntryDefaultViewModel(userFetcher: fetcher)
        viewModel.onUserNameSelected = self.onUserNameSelected
        return viewModel
    }()
    private lazy var entryViewController = EntryViewController(viewModel: entryViewModel)

    init(rootNavigationController: UINavigationController) {
        self.rootNavigationController = rootNavigationController
    }

    func startFlow() {
        rootNavigationController.pushViewController(entryViewController, animated: true)
    }

    func onUserNameSelected(userName: String) {
        print("name: \(userName)");
        // Show next screen using selected user name
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这个`FlowController`的核心元素肯定是`startFlow`方法，在这种情况下，它会将新的`ViewController`推送到我们的导航堆栈中。这个`ViewController`是用一个`ViewModel`作为参数创建的。`viewModel`对我们来说很重要，然而，从`FlowController's`的角度来看，我们对`onUserNameSelected`的结束特别感兴趣。这是我们的`FlowController`可能感兴趣的`ViewModel`的输出。例如，在选择用户名后，我们可以打开下一个屏幕，让我们选择生日、姓氏、最喜欢的宠物或简单地以一种奇特的方式显示名称。您的`FlowController`允许您控制应用程序的流程，并使`ViewModel`不知道它所使用的上下文。`ViewModel`有它的工作要做，完成后我们的`FlowController`会得到通知。

视图控制器

```
class EntryViewController: UIViewController {

    private let viewModel: EntryViewModel

    private lazy var fetchUserButton: UIButton = {        
        let button = UIButton()
        button.addTarget(self, action: #selector(onFetchUserButtonTapped), for: .touchUpInside)
        // ...setup button
        return button
    }()

    private lazy var selectUserButton: UIButton = {
        let button = UIButton()
        button.addTarget(self, action: #selector(onSelectUserButtonTapped), for: .touchUpInside)
        // ...setup button
        return button
    }()

    private let currentUserLabel = UILabel()

    init(viewModel: EntryViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil) // Layout created programamtically, sorry for that ;(
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    override func loadView() {
        super.loadView()
        setupView()
        setupObservers()
    }

    private func setupObservers() {
        currentUserLabel.reactive.text <~ self.viewModel.userName // bind viewModels output to your UI
    }

    @objc func onFetchUserButtonTapped() {
        viewModel.fetchUser()
    }

    @objc func onSelectUserButtonTapped() {
        viewModel.selectUser()
    }

    private func setupView() {
        // setup constraints etc
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，`ViewController`是 MVVM`View`层的一部分。这里发生的是:

*   将`ViewModel's`输出绑定到 UI 组件
*   从`ViewModel`调用方法
*   如果需要，将生命周期方法传递给`ViewModel`
*   布局设置

最后但同样重要的是——视图模型

```
protocol EntryViewModel {
    func fetchUser()    
    func selectUser()

    var onUserNameSelected: ((String)->Void)? {get set} // If you're using Reactive frameworks, you can also implement this as a stream
    // var onNextSelected: (()->Void)? // Other callbacks could exist here, they do not need to pass data

    var userName: Property<String?> {get} // ReadOnly property that allows others to observe its changes, but not change the property from outside of viewModel
}

class EntryDefaultViewModel: EntryViewModel {

    private let userFetcher: UserFetcher
    private var mutableUserName: MutableProperty<String?> = MutableProperty(nil)
    lazy var userName: Property<String?> = Property(self.mutableUserName)
    weak var onUserNameSelected: ((String)->Void)?

    // injecting dependencies to your viewModel 
    init(userFetcher: UserFetcher) {
        self.userFetcher = userFetcher
    }

    func selectUser() {
        if let userName = userName.value {
            onUserNameSelected?(userName)
        }
    }

    func fetchUser() {        
        // Bind the result of fetchUser() function to a mutableUserName property
        mutableUserName <~ userFetcher.fetchUser().map { $0.name }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

`ViewModel's`部分是我们的`View`层真正跳动的心脏。它将允许`View`观察它所暴露的属性的变化。它还将包含各种行为的逻辑。请记住，您不需要将整个逻辑保存在`ViewModel`中，您可以轻松地提取它并将其作为依赖项添加——这就是发生在`userFetcher`中的事情。这允许我们保持我们的`ViewModels`更干净和易于测试，因为我们可以模仿我们的依赖。

# 总结

遵循 MVVM +流量控制器的方式，在分离/组织我的代码和提高可测试性方面帮助了我很多。如果我必须指出使用这种方法对我最有价值的三个好处，那就是:

*   可以在不同的场景中轻松重用
*   舒适地测试包含您的逻辑并且不与`UIKit`耦合的`ViewModels`
*   在一个地方定义导航的应用程序模块

如果你从未在你的应用程序中使用过类似的方法，那么我强烈建议你尝试一下！

你想进一步了解这个话题吗？看看这些:

[用流量控制器改善你的 iOS 架构](http://merowing.info/2016/01/improve-your-ios-architecture-with-flowcontrollers/)

[Sundell 的 Swift“好家伙，我对此有很多想法”，特邀嘉宾索鲁什·坎卢](https://www.swiftbysundell.com/podcast/10)

[协调员——索鲁什·坎卢](https://www.youtube.com/watch?v=a1g3k3NObkE)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由 Eliasz Sawicki，软件工程师@光明发明
[博客](http://eliaszsawicki.com/)，[推特](https://twitter.com/EliSawic?lang=en)

*本文与[作者的博客](https://eliaszsawicki.com/)交叉发布。*