# 在类路径上运行所有 JUnit 测试

> 原文：<https://dev.to/grahamcox82/running-all-junit-tests-on-the-classpath-3hpf>

我已经对此进行了一段时间的修补，最终找到了一种可靠的方法来运行类路径上存在的所有 JUnit 测试。在我的例子中，这样我可以将我的端到端测试打包成一个可执行的 JAR，并在 Docker 容器中运行它们，但是还有其他各种用例。

这段代码是用 Groovy 编写的，因为我是用 Spock 和 Geb 编写的，但它在任何 JVM 语言中都同样适用。

还要注意，这使用了奇妙的 [ClassGraph API](https://github.com/classgraph/classgraph) 来寻找测试。

代码如下:

```
import io.github.classgraph.ClassGraph
import org.junit.internal.TextListener
import org.junit.runner.JUnitCore
import org.junit.runner.Request

class RunAllTests {
    static void main(String... args) {

        def testClasses = new ClassGraph()
                .whitelistPackages(RunAllTests.class.packageName)
                .scan()
                .allClasses
                .filter { it.name.endsWith("Spec") }
                .loadClasses()

        def runner = new JUnitCore()
        runner.addListener(new TextListener(System.err))

        def testRequest = Request.classes(*testClasses.toArray())

        def result = runner.run(testRequest)
        System.exit(result.wasSuccessful() ? 0 : 1)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。它会找到与`RunAllTests`类在同一个包中或更低的包中，并且类名以`Spec`结尾的所有东西，然后运行它们。