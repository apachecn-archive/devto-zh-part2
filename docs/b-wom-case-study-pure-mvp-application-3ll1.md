# B-Wom 案例研究:纯 MVP 应用

> 原文：<https://dev.to/apium_hub/b-wom-case-study-pure-mvp-application-3ll1>

我们在 [Apiumhub](/) 中执行的最重要的任务之一是与我们的客户合作实施[敏捷开发方法](https://apiumhub.com/tech-blog-barcelona/benefits-of-agile-project-management/)并引入良好的软件开发实践([软件架构](https://apiumhub.com/software-architecture-services-barcelona/)、[测试](https://apiumhub.com/tech-blog-barcelona/top-software-testing-techniques/)等)。).今天，我想重点谈谈我们在 [B-wom 应用](https://www.b-wom.com/en/)中实现的纯 MVP 架构。

**多次使用遗留代码**

[我们通常在](/software-projects-barcelona/) [Apiumhub](https://apiumhub.com/mobile-app-development-barcelona/) 得到的项目包含大量遗留代码，我们的主要任务是帮助我们的客户改进代码、实现新功能以及改进包装和分发系统。

在 B-Wom 的具体案例中，我们对系统的两大部分进行了很好的区分，第一部分是后端部分，其中许多代码是用 PHP 编写的，并带有耦合，第二部分是–2 个移动应用程序:一个是用 Java 编写的 Android 应用程序，另一个是用 Swift 编写的 iOS 应用程序(两者都是平台的本地语言)。

在后端部分，我们的主要任务是提供建议，而不是开发本身，因为大部分重构是由 B-Wom 内部完成的，我们更多地参与了打包和持续集成实施部分。

至于移动应用程序，这里我们完全参与了应用程序打包的重构和改进。通常使用遗留代码是非常微妙的，因为你必须考虑的首要任务之一是你需要在不破坏应用功能的情况下修改代码。

在这种情况下，为了避免面临“重构噩梦”的问题和棘手的项目，我们选择尽可能不引人注目，并遵循“小步骤”(婴儿步骤)的技术，这允许我们在代码中进行小的更改，并尽可能避免破坏功能。

## 我们做了什么和如何做(作案手法):

*   首先，我们确定了与特定功能相对应的代码部分，并分析了与代码其他部分的耦合程度。
*   一旦我们确定了功能的依赖关系，我们就应用依赖投资技术来分离代码，并把它分成更小的功能。
*   当我们检测到重复的代码时，我们所做的就是创建私有方法来集中调用该代码。
*   如果这些方法由我们添加功能的不同类共享，我们所做的是创建具有功能意义的单独的类(对类进行类型化),以公开与应用程序的其他部分共享的独特和公共的功能。
*   如果可能的话，对新功能进行测试，以确保其正确运行

**遗留代码架构**

移动应用的遗留代码结构被合理地分离，因为有一系列具有特定任务的组件。我们称这种架构为“MVC 定制”。

**iOS 架构**

在 iOS 架构部分，遗留代码由具有某些特性的模型-视图-控制器组成。与传统 MVC 的本质区别在于模型的部分，特别是由称为“控制器”的类实现的“服务”，这些类包含调用 ApiManager 的静态方法，API manager 是“Alamofire”的包装器，负责进行后端调用。

模型的部分通过“领域”进行管理，并存储在本地，为应用程序增加一些“反应”能力。架构的其余部分依赖于协议和类扩展来分离并为应用程序提供必要的功能。

android 架构在 Android 部分，所使用的架构在基本原理上与 iOS 非常相似，但他们没有使用 MVC，而是使用了一个稍加修改的 MVP 来提高功能的解耦性。按照 Google 为创建 MVP 而提出的架构基础，演示者与一个“控制器”进行通信，该“控制器”的工作方式几乎与我们在 iOS 架构中解释的“服务”相同，使用静态方法和 ApiManager，API manager 是一个改进的包装器，它管理 Realm 中持久存在的后端调用。

## **新纯 MVP 架构**

在那些我们从零开始开发的功能中，比如新的蔻驰功能，我们利用这个机会添加了 Apiumhub 的新架构提案，由我们的架构师 [Christian Ciceri](https://apiumhub.com/software-developer-jobs-barcelona/) 负责监督。我们实现了纯 MVP ( MVPP)架构。这个想法是将一个横向于平台的架构实例付诸实践(因为我们在 Android 和 iOS 中使用了相同的想法),它允许我们以一种相当简单和清晰的方式分离应用程序不同组件的职责。

纯粹的 MVP 架构基本上就是我们在[的另一篇文章](https://dev.to/apium_hub/mvp-pattern-in-ios-98f)中解释的模型-视图-展示者，但是这里我们添加了“纯粹的”方面，并且特别之处在于展示者仅充当视图和服务之间的连接器。

我们在 B-Wom 中实现的基本架构是基于 Rx 的，所以我们使用了 RxSwift 和 RxJava 来添加应用的反应部分。在本文中，我们将解释自底向上的架构，从描述 api 连接器开始，它执行对后端的调用，并在视图中结束。

Api 管理器

我们的 Api 管理器是一个包装器，它依赖于两个平台的不同框架，使用 Rx 进行后端调用，并通过 API Rest 进行通信。它通常是一个静态类，在配置中定义了后端调用。在 Android 的情况下，我们使用 wrapper over retrieval，而在 iOS over Moya 的情况下，这是一个框架，使得在 Rx 上使用 Alamofire 更加容易。至于 Api 管理器，我们没有做过单元测试。

iOS 示例:

```
 enum ApiClient {
    case getSurveys
}

extension ApiClient:TargetType, AccessTokenAuthorizable {

    var baseURL:URL {
        return URL(string: API_URL)!
    }

    var path: String {
        switch self {
        case .getSurveys:
            return "".getApiEndpointPath(type: .kAPI_ENDPOINT_SURVEYS)
    }

    var method: Moya.Method {
        switch self {
        case .getSurveys:
            return .get
    }

    var task: Task {
        switch self {
        case .getSurveys:
            return .requestPlain
    }

    var sampleData: Data {
        switch self {
        case .getSurveys:
            return stubbedResponse("surveyList")
    }

    func stubbedResponse(_ filename: String) -> Data! {
        @objc class TestClass: NSObject { }

        let bundle = Bundle(for: TestClass.self)
        let path = bundle.path(forResource: filename, ofType: "json")
        return try? Data(contentsOf: URL(fileURLWithPath: path!))
    }
} 
```

Android 示例:

```
 interface NetworkService {

    @GET("$VERSION3$PATH_LANG$SURVEYS")
    fun getSurveyHistory(@Path("$LANG_PARAM") lang:String): Observable<Result<BaseResponse<List>>>

    companion object Factory {

        private val gson: Gson = GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss") .create()

        fun create(): NetworkService {
            return createWithParams(BuildConfig.HOST)
        }

        fun createWithParams(baseUrl:String): NetworkService {
            val retrofit = Retrofit.Builder()
                    .baseUrl(baseUrl)
                    .client(initOkHTTP())
                    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                    .addConverterFactory(GsonConverterFactory.create(gson))
                    .build()
            return retrofit.create(NetworkService::class.java)
        }
    }
} 
```

**存储库**

在我们在 Apiumhub 中设计的 MVPP 架构中，存储库负责连接 ApiManager 或者连接本地存储系统，具体取决于调用是在线还是离线。存储库所做的唯一一件事是公开应用程序将拥有的独立于持久化系统的具体方法，在我们的例子中，它将拥有输入参数，并且它将返回一个具有它所涉及的类型的可观察对象。在存储库的情况下，使用 ApiManager 进行集成测试，以保证这两个组件以正确和协调的方式工作。由于它们不包含业务逻辑，我们不考虑使用单元测试来验证代码，因为我们相信集成测试涵盖了那些用例。

iOS 示例:

```
 protocol SurveyRepositoryInterface {
    func getSurveyList() -> Observable<[String]>
    func sendSurvey(surveyId:String, surveyRequest:SurveyRequest) -> Observable
    func getSurveyHistory(surveyId:String) -> Observable<[Survey]>
    func getAllSurveyHistory() -> Observable<[Survey]>
}

class SurveyRepository: SurveyRepositoryInterface {

    let defaultKeyPath = "payload"

    private let apiClient:MoyaProvider

    init(apiClient:MoyaProvider = ApiClientFactory.createProvider()) {
        self.apiClient = apiClient
    }

    func getSurveyList() -> Observable<[String]> {
        return apiClient.rx.request(.getSurveys)
            .asObservable()
            .filterSuccess()
            .map([String].self, atKeyPath: defaultKeyPath)
            .asObservable()
    }
} 
```

Android 示例:

```
 interface SurveyRepository:INetworkBaseRepository {

    fun getSurveyHistory(surveyId:String): Observable<List>

    companion object {
        fun create(): SurveyRepository {
            return SurveyRepositoryImpl(NetworkService.create())
        }
    }
}

class SurveyRepositoryImpl(apiService: NetworkService) : BaseRepository(apiService), SurveyRepository {

    override fun getSurveyHistory(surveyId: String): Observable<List> {
        return executeRequest(apiService.getSurveyHistory(LocaleHelper.getAppLanguage(), surveyId), listOf())
    }
} 
```

**服务**

服务是这样一个类，它负责调用存储库，执行业务逻辑和数据中必要的状态更改，并通过回调将结果返回给视图，使用 presenter 作为“连接器”。在我们正在处理的功能的情况下，服务具有很少的逻辑，并且大多数状态改变是通过使用 Rx 来解决的。该服务执行一个方法，该方法是从视图通过表示器调用的，使用存储库来获取数据。存储库返回一个可观察对象，该可观察对象根据您想要返回的信息类型或是否有错误而订阅了不同的发布者。
例如，如果我们恢复“调查”列表，并且有一个错误，服务将调用方法 onError()，如果一切都是正确的，它将调用方法 onSuccess()。在服务中，我们使用单元测试来检查应用程序。

iOS 示例:

```
 protocol SurveyServiceInterface {

    func getSurveyList()
    func onSurveyList(onSuccess: @escaping (([String]) -> Void))
    func onErrorSurvey(onError : @escaping (Error) -> ())
}

class SurveyService: SurveyServiceInterface {

    private let disposeBag = DisposeBag()
    private let repository:SurveyRepositoryInterface
    private let surveyListStream = PublishSubject<[String]>()
    private let errorStream = PublishSubject<[Error]>()

    init(repository:SurveyRepositoryInterface) {
        self.repository = repository
    }

    func getSurveyList() {
        repository.getSurveyList().subscribe(onNext: { surveyList in
            self.surveyListStream.onNext(surveyList)
        }, onError: { error in
            self.errorStream.onNext([error])
        }).disposed(by: disposeBag)
    }

    func onSurveyList(onSuccess: @escaping (([String]) -> Void)) {
        self.surveyListStream.subscribe(onNext: { surveyList in
            onSuccess(surveyList)
        }).disposed(by: disposeBag)
    }

    func onErrorSurvey(onError: @escaping (Error) -> ()) {
        self.errorStream.subscribe(onNext: { (errors) in
            onError(errors.first!)
        }).disposed(by: disposeBag)
    }
} 
```

Android 示例:

```
 interface SurveyService:BaseService {

    fun getSurveyHistory(surveyId:String)
    fun onSurveyHistorySuccess(onSuccess: (surveyList: List) -> Unit)
    fun onErrorSurvey(onError:(exception:Throwable) -> Unit)

    companion object {

        fun create(sequenceNumberProvider: SequenceNumberProvider): SurveyService {
            return SurveyServiceImpl(SurveyRepository.create(), sequenceNumberProvider)
        }
    }
}

class SurveyServiceImpl(private val repository:SurveyRepository): BaseServiceImpl(), SurveyService {

    private val surveyHistoryObservable:PublishSubject<List> = PublishSubject.create()
    private val errorObservable:PublishSubject = PublishSubject.create()

    override fun getSurveyHistory(surveyId: String) {

        val demoSurvey:Observable<List> = repository.getSurveyHistory(DEMO_SURVEY_ID)
        val screeningSurvey:Observable<List> = repository.getSurveyHistory(SCREENING_SURVEY_ID)

        Observables.combineLatest(demoSurvey,screeningSurvey){t1, t2 -> t1 + t2}
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribeUntil(disposableObservable,{
                    Log.d("aquitamosServicio", "getSurveyHistory")
                    surveyHistoryObservable.onNext(it)
                }, {errorObservable.onError(it)})
    }

    override fun onSurveyHistorySuccess(onSuccess: (surveyList: List) -> Unit) {
        surveyHistoryObservable
                .filter {
                    isHistorySurveySuccess(it)
                }
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribeUntil(disposableObservable, {
                    Log.d("aquitamosServicio", "onSurveyHistorySuccess")
                    onSuccess(it)
                })
    }

    private fun isHistorySurveySuccess(surveyList:List):Boolean {
        return !surveyList.isEmpty() && !isNextAction(surveyList.last())
    }

    override fun onErrorSurvey(onError: (exception: Throwable) -> Unit) {
        INetworkBaseRepository.errorsStream
            .subscribeOn(Schedulers.newThread())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribeUntil(disposableObservable, { onError(it) })
    }
} 
```

在我们的架构中，表示器是最重要的部分，它允许我们构建更多的模块化应用程序，并且耦合度非常低，因为它唯一做的事情就是连接视图和服务的方法。这允许我们有一个高度可重用的软件，并且每个视图有几个服务，虽然这似乎是矛盾的，但这是模块化系统的优势，因为我们想要的是唯一的二项式视图演示者。我们不会对这些缺乏功能的软件进行测试。

iOS 示例:

```
 protocol SurveyViewInterface:class {    
    func getAllSurveyHistory(action:@escaping () -> ())
    func onSurveyHistorySuccess(surveyList:[Survey])
    func onSurveyError(error:Error)
}

protocol SurveyServiceInterface {

    func getAllSurveyHistory()
    func onSurveyHistorySuccess(onSuccess: @escaping(([Survey]) -> Void))
    func onErrorSurvey(onError : @escaping (Error) -> ())
}

struct SurveyPresenter:LifeCycleAwareComponent {

    var view:SurveyViewInterface
    var service:SurveyServiceInterface

    func onLifeCycleEvent(event: LifeCycleEvent) {
        if (event == .viewDidLoad) {  
            view.getAllSurveyHistory(action: service.getAllSurveyHistory)
            service.onSurveyHistorySuccess(onSuccess: { self.view.onSurveyHistorySuccess(surveyList: $0)})
        } else if (event == .viewWillAppear) {
            service.onErrorSurvey(onError: {self.view.onSurveyError(error: $0)})
        }
    }
} 
```

为了能够在每个视图中维护多个演示者，我们有一个视图的基类，我们称之为 LifeCycleAware。该类管理视图的生命周期，并负责将演示者注册为负责管理事件的发布者中的组件。这是在 iOS 中实现 it 的例子:

```
 enum LifeCycleEvent {
    case viewDidLoad
    case viewWillAppear
    case viewDidAppear
    case viewDidDissapear
}

protocol LifeCycleAwareComponent {
    func onLifeCycleEvent(event:LifeCycleEvent)
}

class LifeCycleAwareViewController: UIViewController {

    private let lifeCicleOwner:PublishSubject = PublishSubject()
    private var subscriptions:[Disposable] = [Disposable]()
    let disposeBag:DisposeBag = DisposeBag()

    func subscribeToLifeCycle(component:LifeCycleAwareComponent) {
        subscriptions.append(lifeCicleOwner.subscribe(onNext: { event in
            component.onLifeCycleEvent(event: event)
        }
        ))
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        lifeCicleOwner.onNext(LifeCycleEvent.viewDidLoad)
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        lifeCicleOwner.onNext(LifeCycleEvent.viewWillAppear)
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        lifeCicleOwner.onNext(LifeCycleEvent.viewDidAppear)
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        lifeCicleOwner.onNext(LifeCycleEvent.viewDidDissapear)
        subscriptions.forEach { $0.dispose() }
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

} 
```

这个生命周期系统通过一个枚举定义了视图生命周期的方法。需要多少生命周期方法就有多少生命周期方法来发出事件，我们必须设法将视图与服务进行通信。在 iOS 中，如果我们希望在 viewDidLoad 中执行视图的方法，我们将创建一个方法，该方法将为生命周期的该方法发出一个事件，订阅该事件的所有演示者将收到该响应或执行调用，这取决于所需的逻辑。

**视图**

它负责用户与应用程序的交互，并定义引起用户动作的输入和输出方法。通常我们定义一个接口(契约)，它将由相应的类根据平台来实现(在 iOS 中，它通常由 ViewController 作为 ViewController 的扩展来实现，而在 Android 中，它是在片段中实现的)。

这类似于 iOS 示例的一部分:

```
 class SurveyViewController: LifeCycleAwareViewController, SurveyViewInterface {

    let dataStream:PublishSubject = PublishSubject()

    func getAllSurveyHistory(action: @escaping() -> ()) {
        dataStream.subscribe(onNext: { (surveyId) in
            action()
        }, onError: { [weak self] (error) in
            self?.showErrorProgress(message: error.localizedDescription)
        }).disposed(by: disposeBag)
    }

    func onSurveyHistorySuccess(surveyList:[Survey]) {
        self.hideLoadingProgress()
        surveyList.forEach { (survey) in
            self.createDataSourceWithQuestion(surveyQuestion: survey)
            if let response = survey.textResponseQuestion() {
                let testAnswer = SurveyTestStruct(question: survey, text: response, answers:nil, haveAnswer:true, surveyTestType: .kSurveyTestTypeAnswer)
                self.dataSource.append(testAnswer)
            }
        }
        self.collectionView.reloadData()
        if self.dataSource.count > 0 {
            self.collectionView.scrollToItem(at: IndexPath(row: self.dataSource.count-1, section: 0), at: .bottom, animated: false)
        }
    }

    func onSurveyError(error: Error) {

        guard let bwomError = error as? BWAPIError, let errorMessage = bwomError.message else {
            self.showErrorProgress(message: "initialtest_api_get_error".localized())
            return
        }
        self.showErrorProgress(message: errorMessage)
        DispatchQueue.main.asyncAfter(deadline: .now() + .seconds(2), execute: {
            self.loadInitialSurvey()
        })

    }
} 
```

在 Apiumhub 中，我们从自己的经验中知道，没有一个确定的最终解决方案可以解决一个架构问题，每个客户都有自己的特点和使用案例，因此纯 MVP 架构是我们已经验证的第一种方法，可以与生产中的实际应用程序一起工作，由于我们客户的反馈和积累的经验，我们每天都在改进和适应。

不要忘记订阅我们的[每月简讯](http://eepurl.com/cC96MY)以获得关于移动架构，尤其是纯 MVP 的最新消息！

## 如果你喜欢这个关于 iOS 中的纯 MVP 的案例研究，你可能会喜欢…

[Banco Falabella 可穿戴 iOS 系统&安卓系统](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[Lazada 案例研究:扩展敏捷](https://apiumhub.com/tech-blog-barcelona/scaling-agile-lazada-case-study/)

[Dexma 案例研究:应用开发外部化](https://apiumhub.com/tech-blog-barcelona/case-study-app-development-externalization-for-dexma/)

[iOS objective -C app: Cornerjob 成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

post [B-Wom 案例研究:纯 MVP 应用](https://apiumhub.com/tech-blog-barcelona/pure-mvp/)最早出现在 [Apiumhub](https://apiumhub.com) 上。