# EnvelopeNetwork，或者如何抽象 Alamofire，并在单元测试+ RxSwift 绑定中提供强类型模拟响应。

> 原文：<https://dev.to/ivanmisuno/envelopenetwork-or-how-to-abstract-alamofire-and-provide-strongly-typed-mock-responses-in-unit-tests--rxswift-bindings-22m2>

# 简介

*这篇文章公开了一系列描述[信封框架](https://github.com/ivanmisuno/Envelope)的技术文章，其目的是在几个现有的基础框架之上提供现代的基于协议的抽象层，包括`UIKit`、`Alamofire`、`Realm`等，使单元测试成为一等公民。每一组 API 和扩展都被组织成一个独立的框架，在`Envelope`伞状项目下。在每篇文章中，我将提供基本原理，讨论设计选择，并给出使用新 API 的例子。虽然我将主要关注 Swift 实现，但是所讨论的想法可以应用于更广泛的环境。*

*我将展示如何应用基于现代 Swift 协议的编程方法、S.O.L.I.D .编程原则和依赖注入(DI)模式来解决编写完美(且容易)的单元可测试网络代码的繁琐任务，而无需在生产代码中引入测试专用代码。我希望以一等公民的身份推广测试思想，这有助于在应用程序或框架开发的每个阶段推动设计决策，这对构建健壮的可伸缩应用程序至关重要。*

*文章中提供的例子基于[信封](https://github.com/ivanmisuno/Envelope)项目下的`EnvelopeNetwork framework`。该项目还包含一个有更多示例和单元测试的操场。这个项目本身是我在 2016-2017 年期间在 OkiDokiMessenger app 上工作的结果，它从上述概念的谨慎应用中受益匪浅。*

# 定义网络层的协议

为了测试一个发出网络请求的函数，你通常会怎么做？您可以从单元测试中调用这个函数，并提供一个模拟响应来验证函数的逻辑。

为了使编写单元测试变得可能和容易，注入模拟响应的能力应该是网络库的一个组成部分。不幸的是，甚至不是主要的图书馆，像 Alamofire，自然地提供这种可能性(例如，见[这一期](https://github.com/Alamofire/Alamofire/issues/328))。SO 上的答案建议实现一个`Response`类的变体，它内置了对返回模拟响应的支持，当被单元测试启用时。还有其他基于这个概念的框架，例如， [TRON](https://github.com/MLSDev/TRON) ，它能够向调用者提供模拟响应。然而，这种方法将特定于测试的代码与生产代码混合在一起，而不是将其抽象出来，这反过来使得生产代码不那么健壮，并且更难维护。

另一个解决方案是在运行单元测试时模拟网络堆栈级别的响应，例如，使用[ohhttpsubs](https://github.com/AliSoftware/OHHTTPStubs)库，但这仅允许模拟原始响应数据，这是笨拙的，不是类型安全的，并且随着单元测试数量的增加以及 API 和数据结构的变化，也很难维护。

这里还值得一提的是，通常情况下，网络响应处理程序，即使是在结构最完善、细分最细的应用程序架构中，API 调用封装在非常薄的包装类中，也包含一些重复的逻辑，如参数和请求体编码逻辑、响应验证和错误检查逻辑以及响应反序列化逻辑。一个好的单元测试应该分别测试所有这些逻辑，确保在给定输入差异的情况下正确地构建请求，正确地检查响应中的错误条件，并且反序列化响应体。使用上面提到的方法编写检查所有这些条件的单元测试，比如提供原始的网络响应，是一项非常耗时的任务，而且不是很有启发性。假设有 20 个 API 方法，每个都需要或多或少相似的测试。维护以这种方式编写的单元测试是一个更大的问题。这种方法肯定无法扩展。

允许解决这一挑战的现代设计策略是将所有实现细节隐藏在清晰定义的协议层之后。当从生产环境中调用代码时，实际的网络层实现由应用引导程序实例化，并依赖注入到 API 包装中。另一方面，测试套件实例化了一个“模拟”网络层，它实际上并不进行任何网络调用，而是返回单元测试中设置的模拟响应。

设计抽象层和测试端模拟机制时的另一个考虑是，网络库的客户端可能更喜欢强类型的反序列化对象，而不是原始数据或 JSON 响应。虽然库可以很好地提供向调用者返回强类型反序列化对象的方法，但模仿层也应该为单元测试提供实例化这些相同类型的方法，并将它们作为模仿响应提供给测试中的类，而不是处理原始响应或 JSON 数据。

利用`URLSession`编写一个完整的网络库来满足上面的需求，这本身并不是一个简单的任务。更快的方法是使用一个现有的库，并在其上添加一个基于协议的抽象层，这将允许使用库的所有功能，同时解决可测试性问题。对于我的工作，我选择了`Alamofire`框架作为网络基础，因为它提供了简化和抽象重复繁琐任务的方法，如编码请求参数、验证和反序列化响应。我们将设计一个基于协议的抽象层，提供一个类似的接口(理想情况下，允许直接替换现有的客户端代码)，还提供一个模拟层的实现，允许编写易于维护的单元测试，同时利用 Alamofire 框架的其余功能。

我们先从回顾 Alamofire 框架架构开始。它的顶层对象是`SessionManager`，它用可配置的参数实例化了一个`URLSession`。然后这个对象允许对底层的`URLSession`实例发出请求。每个请求都是`DataRequest`、`DownloadDataRequest`或`UploadDataRequest`的一个实例，允许控制其生命周期并获得原始响应数据。在此之上是一组将原始响应转换为类型化响应的响应序列化器。

所以，让我们开始定义我们自己的顶级网络抽象层协议:

```
protocol Networking {
} 
```

Enter fullscreen mode Exit fullscreen mode

每当一个类需要使用网络层时，它可以通过依赖注入来获得，这样调用代码就可以完全控制这个类将使用网络层的哪个实例:

```
final class TwitterAPIService {
    private let network: Networking
    init(network: Networking) {
        self.network = network
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Alamofire 的`SessionManager`公开了一些创建网络请求的函数。让我们在它之后建模我们的网络层，所以，理想情况下，我们可以在将来有一个简单的 Alamofire 代码的替代。让我们这样来声明`request()`函数:

```
protocol Networking {
   func request(
       _ url: URLConvertible,
       method: HTTPMethod,
       parameters: Parameters?,
       encoding: ParameterEncoding,
       headers: HTTPHeaders?)
       -> NetworkRequesting
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用 Alamofire 框架本身定义的类型(例如，`URLConvertible`、`HTTPMethod`等)，这对于我们在 Alamofire 上创建基于协议的抽象来说是可以的，因为这些已经是协议类型或值类型。

`request()`函数返回一个`NetworkRequesting`类型的实例。这种类型抽象出请求细节，包括验证、进度报告和响应序列化:

```
protocol NetworkRequesting {
   var request: URLRequest? { get }
   var response: HTTPURLResponse? { get }

   func cancel()

   @discardableResult
   func progress(
       queue: DispatchQueue,
       progressHandler: @escaping Request.ProgressHandler)
       -> Self

   @discardableResult
   func response<T: DataResponseSerializerProtocol>(
       queue: DispatchQueue,
       responseSerializer: T,
       completionHandler: @escaping (DataResponse<T.SerializedObject>) -> Void)
       -> Self

   @discardableResult
   func validate(validation: @escaping DataRequest.Validation)
       -> Self
} 
```

Enter fullscreen mode Exit fullscreen mode

这个协议也是模仿 Alamofire 的`DataRequest`类，它将允许在未来进行简单的替换。最有趣的函数`response()`采用通用响应序列化程序的实例，并通过`completionHandler`回调报告类型化的结果值。

有了这些，我们就可以开始编写客户端代码了，例如，搜索 tweets:

```
struct TweetsResponse: Decodable {
    // ...
}

protocol TwitterAPIServicing {
    func searchTweets(q: String, responseCallback: @escaping (Result<TweetsResponse>) -> ())
}

class TwitterAPIService: TwitterAPIServicing {
    struct Configuration {
        let endpointUrl: URL
        static func defaultConfiguration() -> Configuration {
            return Configuration(
                endpointUrl: URL(string: "https://api.twitter.com/1.1/search/tweets.json")!
            )
        }
    }

    private let network: Networking
    private let configuration: Configuration

    init(network: Networking,
         configuration: Configuration = Configuration.defaultConfiguration()) {
        self.network = network
        self.configuration = configuration
    }

    // MARK: - TwitterAPIServicing

    func searchTweets(q: String, responseCallback: @escaping (Result<TweetsResponse>) -> ()) {
        network
            .request(configuration.endpointUrl, method: .get, parameters: ["q": q], encoding: URLEncoding.queryString, headers: nil)
            .response(queue: DispatchQueue.main, responseSerializer: DataRequest.dataResponseSerializer(), completionHandler: { (dataResponse: DataResponse<Data>) in
                switch dataResponse.result {
                case .success(let responseData):
                    do {
                        // read json from network response
                        let tweetsResponse: TweetsResponse = try JSONDecoder().decode(TweetsResponse.self, from: responseData)
                        // deserialize typed object from json
                        responseCallback(.success(tweetsResponse))
                    } catch {
                        responseCallback(.failure(error))
                    }
                case .failure(let error):
                    responseCallback(.failure(error))
                }
            })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们使用 Alamofire 数据响应序列化程序，并自己解码 JSON。这将是一个重复的任务，如果库为它提供一个通用的解决方案，客户端代码将会受益。这可以通过定制的响应序列化器类和`NetworkRequesting`协议中的通用扩展函数来实现:

```
public final class CodableSerializer<T: Decodable>: DataResponseSerializerProtocol {

    // MARK: - DataResponseSerializerProtocol
    public typealias SerializedObject = T

    public var serializeResponse: (URLRequest?, HTTPURLResponse?, Data?, Error?) -> Result<SerializedObject> {
        return { (request: URLRequest?, response: HTTPURLResponse?, data: Data?, error: Error?) -> Result<SerializedObject> in
            let result = Request.serializeResponseData(response: response, data: data, error: error)
            switch result {
            case .success(let data):
                do {
                    let decodedObject = try JSONDecoder().decode(T.self, from: data)
                    return .success(decodedObject)
                } catch {
                    return .failure(AFError.responseSerializationFailed(reason: .jsonSerializationFailed(error: error)))
                }
            case .failure(let error):
                return .failure(error)
            }
        }
    }
}

public extension NetworkRequesting {

    @discardableResult
    func responseObject<T: Decodable>(
        queue: DispatchQueue = DispatchQueue.main,
        completionHandler: @escaping (DataResponse<T>) -> Void) -> Self {

        return response(queue: queue, responseSerializer: CodableSerializer<T>()) { (dataResponse: DataResponse<T>) in
            completionHandler(dataResponse)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个扩展和一些其他的协议扩展，`TwitterAPIService`函数将开始看起来像这样:

```
 // …
    func searchTweets(q: String, responseCallback: @escaping (Result<TweetsResponse>) -> ()) {
        network
            .get(configuration.endpointUrl, parameters: ["q": q])
            .responseObject { (response: DataResponse<TweetsResponse>) in
                responseCallback(response.result)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

考虑到还没有真正实现`Networking`和`NetworkRequesting`协议，这真是太好了，是吧？

# 使用 Alamofire 提供默认实现

提供网络层的默认实现非常简单，因为协议层是在公共 Alamofire 类接口之后建模的。

```
public final class AlamofireNetwork: Networking {
    private let alamofireSessionManager: SessionManager
    public init(alamofireSessionManager: SessionManager) {
        self.alamofireSessionManager = alamofireSessionManager
    }

    // MARK: - Networking
    public func request(
        _ url: URLConvertible,
        method: HTTPMethod,
        parameters: Parameters?,
        encoding: ParameterEncoding,
        headers: HTTPHeaders?)
        -> NetworkRequesting {

        let alamofireRequest = alamofireSessionManager.request(url, method: method, parameters: parameters, encoding: encoding, headers: headers)
        return AlamofireNetworkRequest(alamofireRequest: alamofireRequest)
    }
}

public class AlamofireNetworkRequest: NetworkRequesting {
    private let alamofireRequest: DataRequest
    public init(alamofireRequest: DataRequest) {
        self.alamofireRequest = alamofireRequest
    }

    // MARK: - NetworkRequesting
    public final var request: URLRequest? { return alamofireRequest.request }
    public final var response: HTTPURLResponse? { return alamofireRequest.response }

    public final func cancel() {
        alamofireRequest.cancel()
    }

    @discardableResult
    public final func progress(
        queue: DispatchQueue,
        progressHandler: @escaping Request.ProgressHandler)
        -> Self {

        alamofireRequest.downloadProgress(queue: queue, closure: progressHandler)

        return self
    }

    @discardableResult
    public final func response<T: DataResponseSerializerProtocol>(
        queue: DispatchQueue,
        responseSerializer: T,
        completionHandler: @escaping (DataResponse<T.SerializedObject>) -> Void)
        -> Self {

        alamofireRequest
            .response(queue: queue, responseSerializer: responseSerializer, completionHandler: completionHandler)
            .validate()

        return self
    }

    @discardableResult
    public final func validate(validation: @escaping DataRequest.Validation)
        -> Self {

        alamofireRequest.validate(validation)

        return self
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

应用程序引导程序通过创建和配置 Alamofire 的`SessionManager`，并用它创建一个`AlamofireNetwork`实例:
，来实例化网络层

```
 fileprivate static func defaultNetwork() -> Networking {

        let urlSessionConfiguration: URLSessionConfiguration
        // initialize URLSessionConfiguration

        let trustPolicyManager: ServerTrustPolicyManager
        // initialize ServerTrustPolicyManager

        let alamofireSessionManager = SessionManager(configuration: urlSessionConfiguration,
                                                     serverTrustPolicyManager: trustPolicyManager)
        let network = AlamofireNetwork(alamofireSessionManager: alamofireSessionManager)
        return network
    } 
```

Enter fullscreen mode Exit fullscreen mode

`Networking`的已配置实例存储在[应用程序范围](https://eng.uber.com/deep-scope-hierarchies/)的适当级别，并被依赖注入到需要联网的类中，在我们的示例中是`TwitterAPIService` :

```
protocol MainDependency {
    var network: Networking { get }
}
protocol MainScreenBuilding {
    func build() -> MainScreenRouting
}
final class MainScreenBuilder: MainScreenBuilding {
    let dependency: MainDependency
    init(dependency: MainDependency) {
        self.dependency = dependency
    }
    // MARK: - MainScreenBuilding
    func build() -> MainScreenRouting {
        let twitteApi = TwitterAPIService(network: dependency.network)
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 用手动模拟测试 Swift 代码

让我们再看一下`searchTweets()`函数，以便列出我们想要在单元测试套件中测试的条件:

```
 func searchTweets(q: String, responseCallback: @escaping (Result<TweetsResponse>) -> ()) {
        network
            .get(configuration.endpointUrl, parameters: ["q": q])          // 1
            .responseObject { (response: DataResponse<TweetsResponse>) in
                responseCallback(response.result)                          // 2
            }
    } 
```

Enter fullscreen mode Exit fullscreen mode

该函数只有两行包含逻辑的代码:

*   第 1 行:用正确的 URL 和参数创建请求；
*   第 2 行:将结果返回给调用者。

需要注意的重要一点是，一个适当组织的测试应该只测试被测试的类/函数中明确包含的逻辑。在为`searchTweets`函数编写单元测试时，我们只想测试上面列出的条件，这些条件表达了开发人员的初衷，别无其他。我们不应该转到其他抽象层次，测试例如`get()`函数实际上创建了一个`GET` HTTP 请求，或者测试结果 JSON 数据如何被反序列化的细节。我们应该预料到这些细节已经在网络库的测试套件中测试过了。

让我们为上面的函数编写理想的测试套件:

```
class TwitterAPIServiceSpec: TestSpec {
    override func spec() {
        describe("TwitterAPIService") {
            var network: NetworkingMock!
            var sut: TwitterAPIService!

            let mockEndpointUrl = URL(string: "https://search.twitter.com/1")!
            let mockConfiguration = TwitterAPIService.Configuration(endpointUrl: mockEndpointUrl)

            beforeEach {
                network = NetworkingMock()
                sut = TwitterAPIService(
                    network: network,
                    configuration: mockConfiguration)
            }

            describe("searchTweets()") {
                let mockQuery = "#awesome_testing"
                let mockResult = Result.success(TweetsResponse())
                var actualUrl: URL?
                var actualQuery: String?
                var observedResult: Result<TweetsResponse>?
                beforeEach {
                    network.mockResponse(mockResult, validateRequest: { (url: URLConvertible, method: HTTPMethod, parameters: Parameters?, encoding: ParameterEncoding, headers: HTTPHeaders?) in
                        actualUrl = try? url.asURL()
                        actualQuery = parameters?["q"] as? String
                    })

                    _ = sut.searchTweets(q: mockQuery, responseCallback: { (result: Result<TweetsResponse>) in
                        observedResult = result
                    })
                }
                it("calls network.request()") {
                    expect(network.requestCallCount) == 1
                }
                it("uses correct endpoint URL") {
                    expect(actualUrl) == mockEndpointUrl
                }
                it("uses correct parameter value") {
                    expect(actualQuery) == mockQuery
                }
                it("returns expected result") {
                    expect(observedResult).to(beSuccess())
                    expect(observedResult?.value) == mockResult.value
                }
            } // describe("searchTweets()")
        } // describe("TwitterAPIService")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个测试的地方在于它是完全独立的。它不依赖于网络连接等全局状态，也不依赖于共享的全局`NSURLSession`对象。`TwitterAPIService`类以通过初始化器注入的 Swift 协议的形式显式声明其所有依赖关系，这样测试用例集可以很容易地构造和替换真正的依赖关系。

`NetworkingMock`类符合`Networking`协议，并实现了一个典型的 testing-double helper 接口，允许检查和处理方法调用，还增加了一点，通过让测试提供对请求的强类型模拟响应来允许更高级别的测试，并通过指定验证闭包来检查请求的属性。

另一个有用的扩展是`beSuccess()`快速匹配器功能，它允许断言接收到的`Result<>`值是否成功。类似地，`beFailure()`快速匹配器函数实现了一个匹配`Result.failure`值的谓词。

# 锦上添花:RxSwift 绑定

框架中的扩展允许编写像这样简洁的网络代码:

```
 func searchTweets(q: String) -> Single<TweetsResponse> {
        return network
            .rx.get(configuration.endpointUrl, parameters: ["q": q])
            .object()
    } 
```

Enter fullscreen mode Exit fullscreen mode

单元测试 Rx 代码有一些特性，我想在下面的测试用例中介绍其中的几个:

```
 describe("searchTweets()") {
        let mockQuery = "#awesome_testing"
        let mockResult = Result.success(TweetsResponse())
        var actualUrl: URL?
        var actualQuery: String?
        var observedResult: SingleEvent<TweetsResponse>?
        beforeEach {
            network.mockResponse(mockResult, validateRequest: { (url: URLConvertible, method: HTTPMethod, parameters: Parameters?, encoding: ParameterEncoding, headers: HTTPHeaders?) in
                actualUrl = try? url.asURL()
                actualQuery = parameters?["q"] as? String
            })

            sut
                .searchTweets(q: mockQuery)
                .subscribe { (result: SingleEvent<TweetsResponse>) in
                    observedResult = result
                }
                .disposed(afterEach: self)
        }
        it("network.request() is called") {
            expect(network.requestCallCount) == 1
        }
        it("correct URL was used") {
            expect(actualUrl) == mockEndpointUrl
        }
        it("cotrrect parameter was used") {
            expect(actualQuery) == mockQuery
        }
        it("observed result as expected") {
            expect(observedResult).to(beSuccess())
            expect(observedResult?.value) == mockResult.value
        }
    } // describe("searchTweets()") 
```

Enter fullscreen mode Exit fullscreen mode

首先，设置代码与上面的`TwitterAPIServiceSpec`类完全相同。第二，`network.mockResponse()`称呼也是一样。我个人很喜欢，嘲讽层在原生→ Rx 映射层下面工作。一个明显的变化是:测试代码订阅 Rx 序列，在本例中是`Single<TweetsResponse>`，而不是向网络处理程序提供闭包，返回的`Disposable`实例由扩展`TestCase`类(在`EnvelopeTest`框架中定义)的测试可用的`disposed(afterEach: self)`扩展函数处理。最后，还有另一对`beSuccess()` / `beFaulure()`匹配器函数，对`SingleEvent`的实例进行操作。

因此，在[https://github.com/ivanmisuno/Envelope](https://github.com/ivanmisuno/Envelope)检查回购，并查看存储库中包含的`EnvelopeNetwork.playground`，并测试扩展。希望你会觉得有用。

# 开发库的后续步骤:

*   为 EnvelopeNetwork、EnvelopeNetworkRx 和 EnvelopeTest 框架添加 macOS、tvOS、watchOS 目标→库的 0.1.1 版本；
*   添加 UIKit / AppKit 抽象层；
*   添加 RealmSwift 抽象层；

非常感谢你的意见。

谢谢大家！

* * *

关于我:大约 28 年前，我开始自学程序员；我在 2006 年做机器学习的博士研究时是 C++的首席开发人员；我与人合伙创办了几个软件创业公司，领导开发团队，并在国际高科技公司工作过。我非常幸运地在非常鼓舞人心和具有挑战性的环境中工作，同时开发了一些世界上发展最快的移动应用程序，并体验了一些明智选择的架构原则，这些原则被 400 多名工程团队的每个成员完全遵循，使每个人都可以快速移动，同时允许创建非常健壮、稳定和可伸缩的应用程序。在这一系列的文章中，我想给出一些实际的例子来说明这些原则的应用是如何产生一个质量更好的软件的。