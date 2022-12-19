# 争论提议

> 原文：<https://dev.to/vknabel/argumentoverture-44o8>

一个 Swift 游乐场，旨在为命令行工具提供一些解析参数的功能助手。它使用了 [Overture](https://github.com/pointfreeco/swift-overture) ,是为高度的可组合性、灵活性和对你的项目的自由发展影响很小而构建的。

一个核心用例是[射箭](https://github.com/vknabel/archery)的:只有实际解释的参数将被消费。根据当前命令，任何其他命令将被收集(`remaining`)或阻止执行(`exhaust`)。

首先是一个用法的例子。

```
// Experiment
do {
    let (isVerbose, whoToGreet, language, _) = try with(["-v", "hi", "Some string", "--language", "en"], chain(
        flag("verbose", "v"),
        positional("Name"),
        argument("language", "l"),
        exhaust
    ))
} catch {
    print("Command failed:", error)
} 
```

Enter fullscreen mode Exit fullscreen mode

微型图书馆本身的实现。

```
// Errors
private func quoted(_ string: String) -> String {
    return "\"\(string)\""
}

public protocol StringConvertibleError: Error, CustomStringConvertible {}

public struct MissingArgumentError: StringConvertibleError {
    public let name: String?
    public var description: String {
        return with(name, pipe(
            map(quoted),
            map { "Missing argument \($0)" },
            ??"Missing argument"
        ))
    }
}

public struct NonExhaustiveArgumentsError: StringConvertibleError {
    public let remaining: [String]
    public var description: String {
        return "Unused arguments: \(remaining)"
    }
}

// Domain
private func isArgument(named name: String) -> (String) -> Bool {
    return { $0 == "-\(name)" || $0 == "--\(name)" }
}

private func isOneArgument(of names: [String]) -> (String) -> Bool {
    return { passed in
        names.lazy
        .map(isArgument(named:))
        .reduce(false, { $0 || $1(passed) })
    }
}

private func indexOfArguments(named names: [String]) -> ([String]) -> Array<String>.Index? {
    return { passed in
        passed.index(where: isOneArgument(of: names))
    }
}

public func flag(_ names: String...) -> ([String]) -> ([String], Bool) {
    return { arguments in
        if let index = arguments.index(where: isOneArgument(of: names)) {
            var result = arguments
            result.remove(at: index)
            return (result, true)
        } else {
            return (arguments, false)
        }
    }
}

public func positional(_ name: String? = nil) -> ([String]) throws -> ([String], String) {
    return { arguments in
        let index = arguments.index(where: { !$0.starts(with: "-")})
        if let index = index {
            var result = arguments
            result.remove(at: index)
            return (result, arguments[index])
        } else {
            throw MissingArgumentError(name: name)
        }
    }
}

public func optional<A>(_ f: @escaping ([String]) throws -> ([String], A)) -> ([String]) throws -> ([String], A?) {
    return { arguments in
        do {
            let (remaining, parsed) = try f(arguments)
            return (remaining, .some(parsed))
        } catch is MissingArgumentError {
            return (arguments, nil)
        } catch {
            throw error
        }
    }
}

public func argument(_ names: String...) -> ([String]) throws -> ([String], String) {
    return { arguments in
        if let flagIndex = indexOfArguments(named: names)(arguments), arguments.count >= flagIndex + 2 {
            let value = arguments[flagIndex + 1]
            var rest = arguments
            rest.remove(at: flagIndex + 1)
            rest.remove(at: flagIndex)
            return (rest, value)
        } else {
            throw MissingArgumentError(name: names.first)
        }
    }
}

public func exhaust(_ arguments: [String]) throws -> ([String], Void) {
    if arguments.isEmpty {
        return ([], ())
    } else {
        throw NonExhaustiveArgumentsError(remaining: arguments)
    }
}

public func remaining(_ arguments: [String]) -> ([String], [String]) {
    return ([], arguments)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

同样的概念可以应用于进程，尤其是处理中断:

```
with(Process(), concat(
    setWorkingDir("tmp"),
    passInterrupt(),
    setBashCommand("") // is { concat(setLaunchPath("/bin/bash"), setArguments(["-c", $0]) }
)) 
```

Enter fullscreen mode Exit fullscreen mode