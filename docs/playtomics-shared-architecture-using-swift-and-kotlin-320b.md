# Playtomic 使用 Swift 和 Kotlin 的共享架构

> 原文：<https://dev.to/playtomic/playtomics-shared-architecture-using-swift-and-kotlin-320b>

当从零开始任何项目时，选择技术堆栈是第一个也是最重要的决策之一。在 [Playtomic](https://playtomic.io) 时，我们知道我们想要为移动应用选择一个堆栈，让我们能够在有限的可用资源下提供最佳体验。

移动堆栈有多种选择:

#### 网络技术

像响应式 web 应用程序或渐进式 web 应用程序这样的解决方案允许您利用您的 web 前端体验，同时为所有人提供一个项目。一些本地功能不能使用，但对于大多数应用程序来说，这已经足够了。分发是通过网络浏览器，而不是在应用商店，这可能是一个优势或劣势，取决于你的情况

#### 杂交

通往 native 的下一步你可以选择使用 Phonegap/Cordoba 这样的混合框架，它也使用 web 技术，但被打包到一个应用捆绑包中，提供额外的功能和比纯 web 更好的 UX。

#### 多平台解决方案

有几种多平台解决方案，如 Xamarin、ReactNative 或更新的 Flutter。它们都有自己的卖点和缺点，但总的来说，它们提供了一个统一的解决方案，通过使用单一语言来构建原生应用程序，在某种程度上，使用带有自定义组件的单一 UI 层，跨平台共享大部分代码，同时提供良好的 UX，与原生应用程序提供的非常接近(如果不是相同的话)。

#### 原生

行业标准，带来最佳 UX 的同时也需要最多的资源。构建原生意味着在每个平台上构建一个应用，每个平台都有自己的语言、工具集、库和框架。

## 我们的困境

在没有进入每个项目和我们的分析的太多细节的情况下，我们知道我们希望 Playtomic 成为体育领域的领导者，并且作为一个移动优先项目，我们希望它为我们的最终用户带来最好的体验。

我们还知道，一旦选定，该技术堆栈将是我们唯一的堆栈，因为我们没有足够的资源来维护一个由不同堆栈或 Big Bang 重构组成的“弗兰肯斯坦应用程序”,以完全从一个堆栈迁移到另一个堆栈。我们希望“堆栈”可以投入生产，并且有足够的社区和成熟度来“确定”它将在我们的项目生命周期中得到支持。

那基本上留给我们三个候选人: **Xamarin** ， **ReactNative** 和**Native**；在前两个例子中，React 比 Xamarin 更吸引我们，因为它的编程模型、与未来 web 前端共享代码的可能性以及令人惊叹的社区和工具。

另一方面，在选择解决方案时，您还必须考虑您拥有的团队。在我们的案例中，我们是专业的本地开发人员，拥有两种平台(Android 和 iOS)的经验，对 React 或其他多平台堆栈几乎没有经验。此外，在那个时候，没有 web 前端开发人员或公司内任何有足够 React 经验的人在学习曲线期间指导移动团队。

考虑到所有这些，我们最适合的是本地的。它几乎提供了我们想要的一切(最佳 UX、行业标准、成熟度、长期愿景、优秀的工具、优秀的社区、...)除了一个重要的方面:**成本**

如前所述，进行本地化意味着构建 2 个应用程序(iOS/Android)，与多平台解决方案的单个应用程序相比，这需要额外的成本。但是有多少呢？让我们试着用一个例子来给出非常粗略的数字(*注意，这些不是真实的数字，它们只是根据我们自己以前的经验做出的估计，不要太认真，只是作为一个说明*):

*   **原生**:假设你正在构建一个在 iOS 上耗时 100 小时的功能。然后，将它移植到 Android 会额外花费大约 80 小时(不是 100 小时，因为总是有知识可以从第一个平台“移植”过来)。共 180h

*   多平台:同样的功能大约需要 120 个小时，这取决于你能重用多少和使用的技术。它不是写一次就能到处运行，但也足够接近，只需在单个平台上增加一小部分额外工作。

因此，大约 180 小时对 120 小时，或者换句话说**大约 50%的额外时间用于本地化**。那是相当多的！尤其是像我们这样的小团队。

因此，我们的下一个问题是:**我们能否找到一种方法来构建一个本机应用程序，最大限度地提高跨平台的可重用性，并保持低成本**，接近多平台解决方案提供的成本？如果是这样的话，我们的设置工作会花费不到 1-2 个月的时间吗？(在产品和设计团队开始交付定义良好的功能之前，我们只有这段时间)

我曾经参与过一些非常小的项目(概念证明和一个小型库)，使用这种方法取得了非常好的结果。但构建一个完整的应用程序是一个完全不同的挑战，尤其是当应用程序增长时。

## 共享基础

因此，我们带着一个目标开始构建:**跨原生平台的可重用性**

我们所做的是将两个平台的应用分成 3 个主要部分:

[![Application architecture](../Images/81dcfa6df378850a2dbcf4b7d7aae11e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rz2H897K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmqgg73kp7hlh4r9zvmj.png)

*   用于连接我们的后端并提供持久性的框架。它提供模型、服务和一些实用程序。
*   **Playtomic UI** :带有可视化组件的框架，如自定义文本字段、输入验证器、通用数据源/适配器、动画、...
*   **应用代码**:我们的模块和特性被构建的地方。它包括并使用了前两者。

我们确保两个框架提供相同的公共 API(不同的实现)，我们还在应用中使用的概念上构建了一些外观，这些概念在每个平台上以不同的方式提供，再次保持相同的 API。仅举几个例子:

*   `Promise` ( [iOS](https://github.com/angelolloqui/blog-shared-architecure-swift/blob/master/promise/Promise.swift) [Android](https://github.com/angelolloqui/blog-shared-architecure-kotlin/blob/master/promise/Promise.kt) )
*   `HttpClient` ( [iOS](https://github.com/angelolloqui/blog-shared-architecure-swift/blob/master/http/HttpClient.swift) [Android](https://github.com/angelolloqui/blog-shared-architecure-kotlin/blob/master/http/OkHttpClient.kt) )
*   `JSONObject` ( [iOS](https://github.com/angelolloqui/blog-shared-architecure-swift/blob/master/json/JSONObject.swift) [Android](https://github.com/angelolloqui/blog-shared-architecure-kotlin/blob/master/json/JSONObject.kt) )
*   `NavigationManager` ( [iOS](https://github.com/angelolloqui/blog-shared-architecure-swift/blob/master/manager/navigation/NavigationManager.swift) [Android](https://github.com/angelolloqui/blog-shared-architecure-kotlin/blob/master/manager/navigation/NavigationManager.kt) )
*   `LocationManager` ( [iOS](https://github.com/angelolloqui/blog-shared-architecure-swift/blob/master/manager/location/LocationManager.swift) [Android](https://github.com/angelolloqui/blog-shared-architecure-kotlin/blob/master/manager/location/LocationManager.kt) )
*   ...

> (你可以查看这些回购中的所有代码:[斯威夫特](https://github.com/angelolloqui/blog-shared-architecure-swift)T2】科特林

我们还选择了 Swift/Kotlin 的组合，因为它们有巨大的相似性，我们使用 [SwiftKotlin](https://github.com/angelolloqui/SwiftKotlin) 来最小化从 iOS 到 Android 传输代码所需的时间。

最后，我们在基础对象上添加了一些扩展，以提供某种语言中缺少的一些方法(例如:`compactMap`、`flatMap`、`let`，...)

## 例子

让我快速浏览一个例子，在这个例子中是 Swift 和 Kotlin 中的登录视图和演示者

[![Transpiling presenters](../Images/4af85db9976cf9b6857a6265184b8771.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ezDgm9aU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--rYUtmvQV--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_66%252Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jagvlpiob80fujyogr6.gif)

> 在上面的截图中，我们只需对 Kotlin 代码做一点小小的修改，就可以在 15 秒内通过传输 iOS 版本的演示程序，得到一个完全可用的 Android 版本的演示程序。

#### 登录插件

斯威夫特

```
class LoginPresenter: Presenter<ILoginView> {
    let coordinator: IAuthCoordinator
    let appEventManager: IAppEventManager
    let messageBarManager: IMessageBarManager
    let navigationManager: INavigationManager
    let authenticationService: IAuthenticationService    

    init(coordinator: IAuthCoordinator,
         appEventManager: IAppEventManager,
         messageBarManager: IMessageBarManager,
         navigationManager: INavigationManager,
         authenticationService: IAuthenticationService) {
        self.coordinator = coordinator
        self.appEventManager = appEventManager
        self.messageBarManager = messageBarManager
        self.navigationManager = navigationManager
        self.authenticationService = authenticationService
    }

    override func viewPresented() {
        super.viewPresented()
        view?.setIsLoading(false)
        if authenticationService.isLoggedIn() {
            skipLogin()
        }
    }

    func skipLogin() {
        self.view.let { self.navigationManager.dismiss(view: $0, animated: true) }
    }

    func login(email: String, password: String) {
        view?.setIsLoading(true)
        authenticationService.login(user: email, password: password).then { [weak self] _ in
            guard let `self` = self else { return }
            self.view.let { self.navigationManager.dismiss(view: $0, animated: true) }
            self.appEventManager.sendEvent(AppEvent.loginWithCredentials(success: true))
        }.always { [weak self] in
            self?.view?.setIsLoading(false)
        }.catchError { [weak self] (error) in
            self?.messageBarManager.showError(error: error)
            self?.appEventManager.sendEvent(AppEvent.loginWithCredentials(success: false))
        }
    }

    func rememberPassword() {
        navigationManager.show(coordinator.requestPasswordIntent(), animation: NavigationAnimation.push)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

锅

```
class LoginPresenter(
        private val coordinator: IAuthCoordinator,
        private val appEventManager: IAppEventManager,
        private val messageBarManager: IMessageBarManager,
        private val navigationManager: INavigationManager,
        private val authenticationService: IAuthenticationService)
    : Presenter<ILoginView>() {

    override fun viewPresented() {
        super.viewPresented()
        view?.setIsLoading(false)
        if (authenticationService.isLoggedIn()) {
            skipLogin()
        }
    }

    fun skipLogin() {
        this.view?.let { this.navigationManager.dismiss(view = it, animated = true) }
    }

    fun login(email: String, password: String) {
        view?.setIsLoading(true)
        authenticationService.login(email, password)
                .then {
                    this.view?.let { this.navigationManager.dismiss(view = it, animated = true) }
                    appEventManager.sendEvent(AppEvent.loginWithCredentials(success = true))
                }
                .always { 
                    view?.setIsLoading(false) 
                }
                .catchError { error ->
                    messageBarManager.showError(error = error)
                    appEventManager.sendEvent(AppEvent.loginWithCredentials(success = false))
                }
    }

    fun rememberPassword() {
        navigationManager.show(coordinator.requestPasswordIntent(), animation = NavigationAnimation.push)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

###### 字符串相似度:73.59%

#### [登录视图](#loginview)

```
class LoginViewController: PresenterViewController<LoginPresenter> {
    @IBOutlet weak var usernameTextField: PlaytomicTextField!
    @IBOutlet weak var passwordTextField: PlaytomicTextField!
    @IBOutlet weak var loginButton: UIButton!
    @IBOutlet weak var dismissButton: UIButton!
    @IBOutlet weak var loadingIndicator: UIActivityIndicatorView!

    override func viewDidLoad() {
        super.viewDidLoad()

        usernameTextField.configure(
            inputType: .email,
            labelText: R.string.localizable.auth_login_user_field(),
            errorMessage: R.string.localizable.auth_login_user_error(),
            validators: [
                TextFieldEmailValidatorBehavior(textField: usernameTextField.textField)
            ],
            editTextDidChangeCallback: { [weak self] in self?.reloadLoginButtonState() }
        )

        passwordTextField.configure(
            inputType: .password,
            labelText: R.string.localizable.auth_login_password_field(),
            errorMessage: R.string.localizable.auth_login_password_error(),
            validators: [
                TextFieldLengthValidatorBehavior(textField: passwordTextField.textField, minLength: 5, maxLength: nil)
            ],
            editTextDidChangeCallback: { [weak self] in self?.reloadLoginButtonState() }
        )
        reloadLoginButtonState()
    }

    @IBAction func login() {
        view.endEditing(true)
        presenter.login(email: usernameTextField.text, password: passwordTextField.text)
    }

    @IBAction func skipLogin() {
        presenter.skipLogin()
    }

    @IBAction func rememberPassword() {
        presenter.rememberPassword()
    }

    func reloadLoginButtonState() {
        let fieldsValid = usernameTextField.isValid && passwordTextField.isValid
        let loading = loadingIndicator.isAnimating
        loginButton.isEnabled = fieldsValid && !loading
    }

    // ****  View Interface  ****

    func setIsLoading(loading: Bool) {
        if newValue {
            loadingIndicator.startAnimating()
        } else {
            loadingIndicator.stopAnimating()
        }
        reloadLoginButtonState()
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

```
class LoginFragment : PresenterFragment<LoginPresenter>(R.layout.login_fragment), ILoginView {

    @BindView(R.id.username_edit_text_custom)
    lateinit var usernameCustomEditText: PlaytomicTextField

    @BindView(R.id.password_edit_text_custom)
    lateinit var passwordCustomEditText: PlaytomicTextField

    @BindView(R.id.login_button)
    lateinit var loginButton: Button

    @BindView(R.id.toolbar_back_button)
    lateinit var dismissButton: ImageButton

    @BindView(R.id.loading_indicator)
    lateinit var loadingIndicator: ProgressBar

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        usernameCustomEditText.configure(
                inputType = PlaytomicTextField.InputType.email,
                labelText = R.string.auth_login_user_field,
                errorMessage = R.string.auth_login_user_error,
                validators = listOf(
                        TextFieldEmailValidatorBehavior(usernameCustomEditText.editText)
                ),
                editTextDidChangeCallback = ::reloadLoginButtonState
        )
        usernameCustomEditText.nextTextField = passwordCustomEditText

        passwordCustomEditText.configure(
                inputType = PlaytomicTextField.InputType.password,
                labelText = R.string.auth_login_password_field,
                errorMessage = R.string.auth_login_password_error,
                validators = listOf(
                        TextFieldLengthValidatorBehavior(passwordCustomEditText.editText, 5, null)
                ),
                editTextDidChangeCallback = ::reloadLoginButtonState
        )
        reloadLoginButtonState()
    }

    @OnClick(R.id.login_button)
    internal fun login() {
        hideKeyboard()
        presenter.login(email = usernameCustomEditText.text, password = passwordCustomEditText.text)
    }

    @OnClick(R.id.auth_login_forget_password_button)
    internal fun rememberPassword() {
        presenter.rememberPassword()
    }

    private fun reloadLoginButtonState() {
        val fieldsValid = usernameCustomEditText.isValid && passwordCustomEditText.isValid
        val loading = loadingIndicator.visibility == View.VISIBLE
        loginButton.isEnabled = fieldsValid && !loading
    }

    // ****  View Interface  ****

    override fun setIsLoading(loading: Boolean) {
        loadingIndicator.visibility = if (loading) View.VISIBLE else View.GONE
        reloadLoginButtonState()
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

###### 字符串相似度:70.8%

* * *

如您所见，除了一些语言差异(构造函数，关键字，...)可以快速传输。此外，通过使用 PlaytomicUI 组件和我们的一些扩展，即使在视图层上，代码也是相似的。这部分的主要工作对应于在界面构建器(iOS)或布局 XML(Android)中布局元素。

这里需要注意的一点是，我们本可以决定用代码或者像 [Layout](https://github.com/schibsted/layout) 这样的工具来编写视图。这也使得更多的重用成为可能，但是我们故意选择不这样做，因为我们想尽可能地保持这部分(用户实际看到和经历的部分)的标准化。这也允许我们在需要时使用和遵循平台组件和约定，以及事实上可用的开发工具，从而保持适度的学习曲线，并充分利用我们的本地开发专业知识。

## 好人、坏人、丑人

在使用解释过的共享架构、约定和工具工作了 1.5 年之后，我们对什么对我们有效，什么不太有效有了一个非常清晰的认识。让我试着反省一下:

### 好人

*   团队统一:没有 Android/iOS 团队的区别，因为同一个开发者总是把他的工作转移到另一个平台。这导致了极端的联合、平台平等和更少的争议/障碍
*   **团队表现**:开发 app 代码比独立写 2 个平台快很多。通常只需要几分钟就可以传送演示者、交互者、协调者、模型和服务。XML 和 Xib 文件占用了剩余的时间，并且时不时地在管理器中运行一些代码。平均来说，从一个平台转换到另一个平台需要大约 30%的额外时间，这取决于所涉及的视图的数量和复杂性，非常接近多平台解决方案。
*   **完全原生的 UX** :视觉组件和应用性能与任何其他原生应用相同。此外，不像多平台解决方案那样，在应用程序大小和应用程序启动时间上没有额外的损失。
*   **长期愿景**:我们在每个平台上使用事实上的工具、框架和语言，我们没有重要的依赖。我们可以确定代码将在很多年内有效，即使在某个时候团队成长，我们停止共享代码，它们仍然是有效的独立的标准原生项目。
*   良好的抽象和代码质量:我们想要尽可能多地重用代码的事实迫使开发人员非常仔细地考虑他们想要构建的抽象。它鼓励关注点的适当分离、单一的责任类、更多的测试(以验证透明性)等等。事实上，我甚至会说，代码审查也更有效，因为你可以将 PR 与对应部分并排比较，并在更高的级别上检测问题。质量不仅是理想的，而且从第一天起就被视为实际的生产力提升。
*   **减少项目的认知负荷**:拥有 1 个代码库使得理解项目和记住内部细节容易得多。

### 坏了

*   额外的架构工作:众所周知，构建这些共享的抽象和扩展需要时间。在我们的案例中，我们花了大约 1 个月的时间研究架构基础，从那以后，我们不得不不时地做一些改变和补充。总开销很难计算，但在开始时尤其明显。
*   **隐藏在语言差异中的错误**:在大多数情况下，翻译非常有效💥。然而，在这 18 个月的工作中，我们遇到了 3 到 4 次意想不到的语言差异导致的错误。特别重要的是 Swift 中的值类型(`struct`)，它在 Kotlin 中没有对应的类型，或者在 Kotlin 中没有代替数组的排序。这些差异造成了限制，如果考虑不周，就会成为 bug 的来源。
*   **语言特性的最大公约数**:与上一条类似，共享代码会限制你使用语言的方式(或者需要更多的翻译工作)。因此，我们倾向于使用语言特性的最大公约数来编写代码。Swift 限制的几个例子是值类型和协议扩展，而在 Kotlin 中，接口中装饰符或默认参数的使用。
*   **视图层需要每个平台的工作**:编写视图需要每个平台的具体工作。这对开发时间、测试和 bug 修复有影响，这在具有共享 UI 的多平台解决方案中不会发生太多。
*   学习曲线:我们使用的所有架构组件和代码约定都是这个项目特有的，因此需要一些学习。然而，公平地说，所有项目都有自己的内部约定和架构设计，所以至少在两个平台上有相同的就意味着只有一条曲线需要通过，而不是两条。

### 丑陋

*   **混合约定** : Kotlin 和 Swift 非常相似，但是它们使用不同的命名约定。例如，在 Kotlin/Java 中，常量是大写的，而在 Swift 中却不是。或者，Java 中常见的经典前缀`I`在 Swift 中并不存在(最接近名称的后缀`Protocol`)。因此，当共享代码时，你必须要么混合各种惯例，要么用更多的手工编辑来惩罚转换过程，以适应不同的版本。我们从每个平台的约定开始，我们逐渐进入一个单一的约定，在我们看来，这是两个世界最好的约定，并且正在成为我们事实上的移动团队约定(但是对于外部开发人员来说，它看起来很“丑陋”)
*   **手动复制变更**:编译在构建新特性时非常有用，因为你可以复制&粘贴完整的编译代码。然而，在维护代码时，开发人员需要记住将平台 A 上所做的相同更改应用到平台 b 上。因此，我们有时会忘记复制，导致应用行为的一些不一致。我们通过公关来控制这种情况，迫使两个平台进行相同类型的更改，并并行审查它们，但仍然存在人为错误的情况。
*   团队扩展:使用这种方法时，拥有这样一个小团队很有帮助，因为它需要成员之间进行大量的交流。我们不确定这将如何与更多的开发人员一起扩展，但我们怀疑它不会有一天我们有 6+人左右。此外，只要我们想继续有效地共享代码，我们就“被迫”雇佣(或教授)多平台专家。

总的来说，回顾过去，我们觉得这个决定对我们团队来说是正确的。这并不意味着使用 React 将是一个错误(可能不是)，但我们对我们得到的结果非常满意。当然，我们时不时会遇到一些问题，我们不得不花几个月的时间进行抽象(无论如何，这段时间应该是用来学习 React 的)，但是**我们现在拥有最好的 UX，开发速度也非常不错**。此外，我们**不依赖于某些第三方框架或工具**(甚至 SwiftKotlin 可以被删除，只需手动传输代码，这也不是那么糟糕)这给了我们长期的信心，我们**可以自由选择我们喜欢的每个模块的应用架构**(MVP、MVVM、VIPER、REDUX、...).我们也可以利用所有的**原生好东西**一发布，我们就可以充分利用**团队的知识**。

> 用[工具 4Noobs](https://www.tools4noobs.com/online_tools/string_similarity/) 计算字符串相似度