# 一个书呆子的第三方开源框架列表，用于一个新的 Swift iOS 应用程序项目。

> 原文：<https://dev.to/ivanmisuno/a-nerds-list-of-3rd-party-open-source-frameworks-to-use-in-a-new-swift-ios-app-project-4had>

如果你是一家成熟产品公司的一员，你通常不会有太多机会从零开始一个新项目。脸书[前阵子把](https://www.facebook.com/notes/facebook-engineering/under-the-hood-rebuilding-facebook-for-ios/10151036091753920/)的混合应用移植到了原生 Objective-C 上。 [Lyft](https://www.fastcompany.com/3050266/lyft-goes-swift-how-and-why-it-rewrote-its-app-from-scratch-in-apples-new-lang) ，[优步](https://www.skilled.io/u/playgroundscon/swift-at-scale)确实用现代 Swift 语言重写了他们旧的 Objective-C 应用程序。很少有人敢做同样的事情，这是可以理解的——因为完全重写核心业务应用程序的成本和风险太高。我有幸体验了优步的重写感觉，我看到了这是多么巨大的努力和它需要每个人的承诺水平。如果可以，我很想让时光倒流，再经历一次。

作为一名工程师和一名有抱负的技术领导者，对我来说最重要的收获是，选择开发团队普遍接受的实践，以及底层工具和编程范例的选择，对软件项目的成功有着巨大的影响。有了早期做出的正确决策和良好的沟通，开发人员团队可以获得巨大的速度并长期保持这种速度，而不会堆积技术债务，继续保持应用程序架构的干净和可维护性，并且不会踩到彼此的脚趾。

# 摘要

我最近开始了几个新的 iOS 应用项目。最大的一款花了我两年时间才完成，是一款成熟的原生 iOS 消息应用。在从事这项工作时，我能够尝试多种方法和架构模式，并观察它们的表现如何。从这次经历中最大的收获是，整个应用程序的架构需要保持一致，这与我在大型工程组织中工作的经历完全吻合。你不能有效地在 onboarding 部分将 MVC 与 Redux 结合起来进行设置，而在其他地方将 VIPER(或 RIBs)结合起来。

对我来说，在项目开始时获得足够的速度，并在整个项目生命周期中保持速度的最有效的方法是使用一组第三方框架，这些框架提供足够的高级抽象能力，并遵循一个精心选择的架构原则，而不是在应用程序的不同部分混合抽象级别。作为一个团队领导，你能做的最好的事情就是看到你早期建立的原则被所有团队成员遵守。

所以，长话短说，下面是我将再次用于我开始的每个新的 iOS 应用程序的原则和框架列表。

# 原则

*   [RIBs](https://github.com/uber/RIBs/wiki) 架构(针对典型的面向 UI 的 app):
    *   因为它允许应用程序的业务逻辑，而不是 UI 树，来驱动应用程序的架构；
    *   因为它允许实现下面设置的代码覆盖率和依赖项注入目标；
    *   因为这是一个非常简单的概念，使用它你并不真的需要一个框架，你可以通过采用一个仅由 4-5 个协议组成的模式来开始使用它；
*   基于初始化器的静态[依赖注入](https://medium.com/ios-os-x-development/dependency-injection-in-swift-a959c6eee0ab):
    *   因为它允许你的代码 100%单元可测试；
*   用于传递数据的反应性( [RxSwift](https://github.com/ReactiveX/RxSwift) )数据/事件流:
    *   因为从数据和事件流的角度来考虑你的应用，简化了它的架构；
*   涵盖 100%业务和表示/导航逻辑的单元测试:
    *   业务逻辑类决定应用程序应该导航到哪个屏幕，应该发出什么网络请求，以及将什么数据存储到本地数据库；
    *   导航逻辑很难在一个经典的 MVC 应用程序中进行单元测试，这就是肋骨发光的地方；
*   覆盖 100%用户界面的快照测试:
    *   因为您可以采用单元测试原则来测试 UI，这很酷(例如，确保在每种支持的语言中，长标签都很好地换行，并且不会破坏屏幕的其余部分)；
*   涵盖关键端到端用户旅程的 UI 自动化测试:
    *   创建和维护它们是困难的，并且需要一些高级工具，但是它自动化了端到端的问答，并且最终获得了显著的回报；
*   代码生成，例如:
    *   模拟类，这样在单元测试中你就有了测试替身；
    *   网络服务和后端模型都是从单个跨平台 IDL 文件生成的代码，在系统和团队之间共享，因为它保证了这些系统可以可靠地相互对话；
    *   本地化字符串、图像和其他资源，以便有一个过程来保持您的字符串和资源有组织、类型安全，并自动移除垃圾；
*   多线程、定时器、操作队列仅允许使用 RxSwift 调度程序:
    *   因此代码是可单元测试的(不仅仅是原则上，而是以一种可靠、快速和可控制的方式)，单元测试不是[易变的](https://www.swiftbysundell.com/posts/reducing-flakiness-in-swift-tests)；
*   所有的 UI 都是使用专门的领域特定语言以编程方式创建的(所以没有 XIBs 故事板！):
    *   以便所有的代码变更都可以被代码审查；
    *   从而将 UI 逻辑与业务逻辑分离，避免了大量的视图控制器模式；
    *   以便可以使用基于初始化器的依赖注入来实例化视图控制器和视图(并避免可选的展开)；
    *   以便可以使用 UI 快照测试；
*   自动化翻译流程:
    *   所以你不必手动上传你的字符串进行翻译；
*   每一个重大的代码变更、倡议，甚至到详细的功能实现细节和首次展示计划都要[传达给所有团队成员](https://blog.pragmaticengineer.com/scaling-engineering-teams-via-writing-things-down-rfcs/):
    *   以便分享知识；
    *   建立了反馈回路，使开发团队更加稳定和高效；
    *   因为这是我所知道的唯一一个可以从 2 人团队扩展到 1000 多人团队的过程；
*   我是否提到了强制性的提交前同行代码评审，团队之间的责任区域被清晰地定义，并且建立了从相应团队中自动分配评审者的规则:
    *   以便共享知识和维护编码规程。

定义了原则之后，什么样的最小框架集最接近我们的目标呢？

# 第三方框架

1.  对于主要应用目标:
    *   [RxSwift，RxCocoa](https://github.com/ReactiveX/RxSwift) 作为 Swift 语言中建立和支持最多的反应库；
    *   [网络抽象层的 alamo fire](https://github.com/Alamofire/Alamofire)；
    *   [Realm](https://github.com/realm/realm-cocoa) 作为移动数据库；
    *   [SnapKit](https://github.com/SnapKit/SnapKit) 作为一种特定于领域的语言，帮助在不使用 xib 的情况下构建 UI；
    *   作为一种编程模式和一个固执己见的应用程序的业务逻辑构建框架；
    *   [Envelope](https://github.com/ivanmisuno/Envelope) 提供 UIKit、Network、Realm 上基于协议的抽象(目前正在做，从不同的项目中提取一个可重用的细粒度框架)；
2.  对于测试目标:
    *   [Sourcery](https://github.com/krzysztofzablocki/Sourcery) 和 [SwiftMockTemplates](https://github.com/ivanmisuno/swift-sourcery-templates) 代码生成模板，为单元测试生成模拟类；
    *   [Swiftgen](https://github.com/SwiftGen/SwiftGen) 生成字符串和图像资产；
    *   [缠绕](https://github.com/scelis/twine)使翻译过程自动化；
    *   用于单元测试的快速和[敏捷](https://github.com/Quick/Nimble)作为普通的旧 XCTest 的替代；
    *   [用于快照测试的 iosnapshottestcase](https://github.com/uber/ios-snapshot-test-case)。

# 结论

上面的列表似乎包括了来自不同领域的框架。那是故意的。我希望框架提供高级抽象概念，而不是具体的构建块，这就是为什么我不会使用第三方图像拾取器实现，而是很乐意将 RIBs 和 RxSwift 与 Alamofire 和 Realm 一起使用。前者为应用程序增加了具体的专门化，以及对(可能)维护得不好的组件的依赖，而后者增加了通用编程概念，这些概念可以很好地协同工作并得到很好的支持。

像往常一样，我会感谢任何反馈或问题。
感谢阅读！