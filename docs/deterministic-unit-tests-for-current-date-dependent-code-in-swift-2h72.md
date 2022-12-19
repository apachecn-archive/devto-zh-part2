# Swift 中当前日期相关代码的确定性单元测试。

> 原文：<https://dev.to/ivanmisuno/deterministic-unit-tests-for-current-date-dependent-code-in-swift-2h72>

自从我发表上一篇关于[信封框架](https://dev.to/ivanmisuno/envelopenetwork-or-how-to-abstract-alamofire-and-provide-strongly-typed-mock-responses-in-unit-tests--rxswift-bindings-22m2)的文章已经有一段时间了——围绕 Alamofire 的一层薄薄的包装使编写网络代码的单元测试变得轻而易举。也许这是一篇太大的文章，但无论如何，我希望听到更多的反馈。今天我将分享一个我从几年前就开始使用的非常简单的技巧，它简化了编写单元测试的另一个方面:测试使用当前日期/时间的代码。

# 问题是

因此，假设您正在编写一个方法，请求更新一个已经过期的记录:

```
final class Controller {
    private let entityManager: EntityManaging

    func requestUpdateIfNeeded(_ record: Record) {
        let dateOfExpiration = Date().addingTimeInterval(
            -configuration.expirationInterval)
        if record.lastUpdateTime <= dateOfExpiration {
            entityManager.requestUpdate(record.id)
        }
    }
} 
```

这里，`entityManager`是一个对象，负责请求并存储`id`对记录的更新。

这样一个函数的单元测试会是什么样子呢？

```
import Quick
import Nimble
@testable import MainAppModule

class ControllerSpec: QuickSpec {
    override func spec() {
        var sut: Controller!
        var entityManager: EntityManagingMock!
        beforeEach {
            // Construct instances of `sut` and `entityManager`
        }
        describe("Controller.requestUpdateIfNeeded()") {
            context("when expired") {
                let expiredRecord = RecordFixture.expiredRecord()
                beforeEach {
                    sut.requestUpdateIfNeeded(expiredRecord)
                }
                it("requests entityManager to update the record by its id") {
                    expect(entityManager.requestUpdateCallCount) == 1
                }
            }
            context("when not expired") {
                let nonExpiredRecord = RecordFixture.nonExpiredRecord()
                beforeEach {
                    sut.requestUpdateIfNeeded(nonExpiredRecord)
                }
                it("does not request update") {
                    expect(entityManager.requestUpdateCallCount) == 0
                }
            }
        }
    }
} 
```

看到问题了吗？我们需要一个`nonExpiredRecord`，即`Record`的一个实例，其条件`record.lastUpdateTime <= dateOfExpiration`将是`false`。所以`RecordFixture.nonExpiredRecord()`应该生成一个`Record`，其中`lastUpdateTime`更新为当前时间！想象一下当`Record`是一个`struct`时会是什么样子——你必须复制该结构的所有字段，用当前日期更新一个字段。更糟糕的是，当这种固定来自于例如保存的网络响应时，其模式可能随着时间而改变，支持这种测试代码成为持续的痛苦，并且是 CI 失败的来源。即使 fixture 构造正确，如果实际时间已经过去，在调试器中单步执行函数也可能导致对条件的评估结果错误。

# 解决方案

*冻结时间。*

/Captain 明显模式 on/单元测试应该是确定性的。甚至是那些处理当前时间的。/船长明显模式关闭/

想象一下，在单元测试套件下运行时调用`Date()`总是会返回，比如说，`1 January 2016 12:00GMT`？那么为一个总是“未过期”的记录创建一个测试夹具将是微不足道的，不是吗？

那么我们如何在单元测试下覆盖`Date()`,使它返回一个预定义的日期呢？不管是好是坏，这是不可能直接实现的——彻底改变方法实现已经是过去的事情了。

我们能做的是:

1.  为`Date()`提供一个替代方案，在正常程序执行下返回当前日期，并具有单元测试套件覆盖其行为的能力；
2.  在测试套件中，覆盖它以总是返回预定义的日期；
3.  禁止在源代码中使用 lint 规则和/或 git 预提交钩子来使用`Date()`初始化器。

让我们一步一步来。

## 1.提供了获取当前日期的替代方法。

```
//  Date+current.swift

internal var __date_currentImpl: () -> Date {
    return Date()
}

extension Date {
    /// Return current date.
    /// Please replace `Date()` and `Date(timeIntervalSinceNow:)` with `Date.current`,
    /// the former will be prohibited by lint rules/commit hook.
    static var current: Date {
        return __date_currentImpl()
    }
} 
```

## 2.在测试套件下覆盖当前日期行为。

```
//  Date+mockCurrentDate.swift

@testable import MainAppModule
import Quick

// `configure()` function gets executed when test suite is loaded (same rules as +[NSObject load] apply);
/// This replaces the `Date.current` implementation so that when running under the test suite it always returns `Date.mockDate`,
/// allowing to write unit-tests than test the code dependent on the current date.
class MockCurrentDateConfiguration: QuickConfiguration {
    override class func configure(_ configuration: Configuration) {
        // This gets executes before initialization of `let` constants in unit tests.
        Date.overrideCurrentDate(Date.mockDate)

        // This gets executed before any other `beforeEach` blocks.
        configuration.beforeEach {
            Date.overrideCurrentDate(Date.mockDate)
        }

        // Restore the possibly overridden (in a Quick test) mock date handler.
        configuration.afterEach {
            Date.overrideCurrentDate(Date.mockDate)
        }
    }
}

extension Date {
    static var mockDate: Date = Date(timeIntervalSinceReferenceDate: 536500800) // 1 January 2018, 12:00GMT

    static func overrideCurrentDate(_ currentDate: @autoclosure @escaping () -> Date) {
        __date_current = currentDate
    }
} 
```

如果`Quick`没有被使用，那么同样的技巧可以通过重写单元测试套件类的`class func load()`函数来实现。

所以现在，当在测试套件下运行时，主应用程序模块中调用`Date.current`的代码将总是接收`Date.mockDate`的值，因此构建测试夹具变得像下面这样简单:

```
 let expiredRecord = Record(
        lastUpdateTime: Date.mockDate.addingTimeInterval(
            -configuration.expirationInterval)
    let nonExpiredRecord = Record(
        lastUpdateTime: Date.mockDate) 
```

或者，测试用例可以覆盖代码的当前日期值:

```
 // ...
    beforeEach {
        // Override the current date
        Date.overrideCurrentDate(Date.mockDate.add)
    } 
```

## 3.禁止在代码中使用`Date()`。

让我们在 git 预提交钩子脚本中添加一个部分，用一个简单的 regexp 查找所有添加到 stage 区域的`Date()`模式:

```
#!/bin/sh
# pre-commit
# copy to .git/hooks folder ()

date=$(git diff --cached -- *.swift ":(exclude)*/Date+current.swift" | grep -E '\bDate\(\)\b' | wc -l)
if [ "$date" -gt 0 ] ; then echo "  Error: Usage of Date() initializer is prohibited."
    echo "         Please use Date.current value, and make sure unit-tests are not dependent on the actual current date."
    exit 1
fi 
```

希望这有意义。黑客快乐！

此外，将非常感谢任何反馈。

谢谢！