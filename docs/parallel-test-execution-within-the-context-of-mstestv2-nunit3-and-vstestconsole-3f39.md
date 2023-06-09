# 在 MSTestv2、NUnit3 和 VSTest 的上下文中并行执行测试。安慰

> 原文：<https://dev.to/franndotexe/parallel-test-execution-within-the-context-of-mstestv2-nunit3-and-vstestconsole-3f39>

将自动化测试保持在一个合理的性能范围内不是一件小事，我一直在寻找减少自动化测试总持续时间的方法。最近，我阅读了一些关于测试并行执行的不同工具的能力。这些工具包括与 [vstest.console](https://github.com/Microsoft/vstest) 一起运行时的 [MSTestv2](https://github.com/Microsoft/testfx) 和 [NUnit3](https://github.com/nunit/nunit) 。

在构建测试用例来比较这两者时，我构建了两个项目。一个使用 MSTestv2 (v1.3.2) `[DataTestMethod]` s，旨在利用 [DynamicData](https://github.com/Microsoft/testfx-docs/blob/master/RFCs/006-DynamicData-Attribute.md) ，另一个使用 NUnit3 (v3.10.1)。下面是我的 MSTestv2 测试。

```
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
using System.Threading;

[assembly: Parallelize(Workers = 0, Scope = ExecutionScope.MethodLevel)] //0 means use as many workers as possible

namespace mstest_parallel
{
    [TestClass]
    public class UnitTest1
    {
        private static IEnumerable<object[]> MyTestData()
        {
            for (int i = 0; i < 10; i++)
            {
                yield return new object[] { i * 1000 };
            }
        }

        [DataTestMethod]
        [DynamicData(nameof(MyTestData), DynamicDataSourceType.Method)]
        public void TestMethod1(int testVal)
        {
            Thread.Sleep(testVal);
        }
    }
} 
```

现在，MSTestv2 在某种程度上具备了并行测试的能力。以下是它提供的高级并行选项:

*   **类级别**——每个线程执行`[TestClass]`个测试。在`[TestClass]`中，测试方法连续执行
*   **方法级**——每个线程执行一个`[TestMethod]`
*   **自定义** -用户可以提供一个插件来实现所需的执行语义(尚不支持)。

这些功能可以称为**细粒度并行化**特性，工作线程的数量可以通过汇编属性`[assembly: Parallelize(Workers = n, Scope = Execution.ClassLevel)]`或`.runsettings`文件进行配置。[更多详情请点击这里](https://blogs.msdn.microsoft.com/devops/2018/01/30/mstest-v2-in-assembly-parallel-test-execution/)

请记住，这些不同于 [MSTest v1 的并行选项](https://blogs.msdn.microsoft.com/vstsqualitytools/2009/12/01/executing-unit-tests-in-parallel-on-a-multi-cpucore-machine/)和**也不同于 vstest.console 的并行选项**。

Vstest.console 的[并行选项](https://blogs.msdn.microsoft.com/devops/2016/10/10/parallel-test-execution/)专注于其作者所称的**粗粒度并行化**。这意味着一次可以并行运行多个测试容器。因此，如果您从命令行运行 vstest.console 并指定几个测试容器，即`mytestlib1.dll mytestlib2.dll`, vs test . console 将同时运行每个测试容器，直到达到机器上可能的最大值或配置的最大值。

我们为什么关心？我想从所选的测试框架和运行程序中获得尽可能多的性能——理想情况下，我自己不必编写任何线程代码。**不幸的是，MSTestv2(和 v1)缺少一个非常具体而重要的并行化选项；能够并行运行通过`[DynamicData]`和/或`[DataRow]`** 配置的测试。不支持这一点的证据存在于这里的、这里的和这里的。对于我们的情况，这意味着我们利用`[DynamicData]`来生成独特案例的测试方法将使每个`[TestMethod]`的所有测试案例串行运行，而不是并行运行，从而使我们的大部分并行化工作变得毫无意义。

通过在方法级别为程序集启用并行化，我们的概念验证 MSTestv2 项目在使用 vstest.console 执行时显示了以下结果:

总测试次数:11 次。通过:11。失败:0。跳过:0。

**试运行成功。**

**测试执行时间:47.4547 秒**

没那么好。这些测试中的大多数都是一个接一个地进行的。

**注意**:当运行 MSTestv2 测试时，vstest 显然将“基本”测试方法报告为测试用例，这就是为什么测试计数是 11 而不是 10。我的理解是，它实际上并没有运行，但是包含了与这个`[TestMethod]`相关的所有测试的总体结果。

让我们来看看 NUnit3。这个框架提供了我们想要的的[特性，它运行参数化的测试，这些测试在运行时并行生成用例。](https://github.com/nunit/nunit/issues/2471#issuecomment-340290377)

这两个框架的根本区别在于，MSTestv2 认为通过`[DynamicData]`和`[DataRow]`的测试用例都是在单个【Test method】下的[，而 NUnit3 认为](https://github.com/Microsoft/testfx/issues/450#issuecomment-400966410)[每个测试用例都是它自己单独的【测试】](https://github.com/nunit/docs/wiki/Parameterized-Tests)。

下面是我们调整后用于 NUnit3 的测试方法:

```
using NUnit.Framework;
using System.Collections.Generic;
using System.Threading;

namespace nunit_parallel
{
    [TestFixture]
    [Parallelizable(ParallelScope.Children)] //parameterized tests are considered child tests
    public class UnitTest1
    {
        private static IEnumerable<object[]> MyTestData()
        {
            for (int i = 0; i < 10; i++)
            {
                yield return new object[] { i * 1000 };
            }
        }

        [Test]
        [TestCaseSource(nameof(MyTestData))]
        public void TestMethod1(int testVal)
        {
            Thread.Sleep(testVal);
        }
    }
} 
```

在类 [`[Parallelizable(ParallelScope.Children)]`](https://github.com/nunit/docs/wiki/Framework-Parallel-Test-Execution) 顶部指定的属性告诉 NUnit 并行化类中的每一个测试方法，它认为我们生成的每一个测试用例都是唯一的测试方法。另外，我让 NUnit 决定我的`[LevelOfParallism]`。通过[默认](https://github.com/nunit/docs/wiki/LevelOfParallelism-Attribute)，NUnit3 使用处理器计数或 2，以较大者为准。

现在，使用 vstest.console 运行相同的测试，并按照`[Test]`运行相同数量的测试用例:

总共 10 次测试。通过:10。失败:0。跳过:0。

**试运行成功。**

**测试执行时间:17.5729 秒**

可以预见的是，运行测试套件的时间大大缩短了。

这个设置的一个重要部分是如何运行这些测试。Vstest.console 为我们处理来自每个测试框架的运行测试，只要我们能够[指定必要的测试适配器](https://github.com/Microsoft/vstest-docs/blob/master/RFCs/0004-Adapter-Extensibility.md#specifying-an-adapter)。对于 NUnit3 和 MSTestv2，适配器都是以 NuGet 包的形式提供的。将这些 NuGet 包添加到您的测试项目中会导致在构建时复制出必要的库，只要它们与您指定的测试容器位于同一目录中，vstest.console 就会为您找到它们。

我希望这有助于解释一些关于并行测试的选项，尤其是参数化测试。在研究这些东西的时候，我学到了很多，我确信在编写这些类型的测试套件的时候，这种情况会继续下去。如果你认为我上面写的任何东西需要改正，请联系我。