# 不要注释掉你的单元测试！

> 原文：<https://dev.to/thejessleigh/dont-comment-out-your-unit-tests-1p40>

我最不喜欢的事情莫过于签出一个项目并看到测试模块的大部分被注释掉。当这种情况发生时，如果在这个部分的顶部有一个 TODO 来描述为什么这个测试被忽略了，那么你是幸运的。即使这样，TODO 语句已经过时也是一个相当可靠的假设。也有可能目标代码不再有测试覆盖。因此，如果这部分代码中的某个东西遭受了回归，它将会[无声无息地](https://www.python.org/dev/peps/pep-0020/)失败。

有一些很好的理由可以解释为什么您可能不想运行测试。也许您的代码库中有当前未启用的触发器。也许您的代码中有一个不间断的故障，并且您不能承受由于[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)故障造成的延迟。也许你的一些测试只适用于你的项目的旧版本。这些场景都不理想，但是作为开发人员，我们很少在理想的环境下工作。有时忽略部分测试套件是有意义的。

然而，内置的 [unittest](https://docs.python.org/3/library/unittest.html) 库并没有注释掉您的测试，而是提供了一些优雅地处理有问题的测试的特性。

# 跳过

如果你有一段代码没有被使用，但是你计划在将来启用，那么 [skip](https://docs.python.org/3/library/unittest.html#unittest.skip) 装饰器是合适的。修饰一个任务并接受一个描述跳过原因的字符串。你应该*总是*提供一个不参加考试的理由。

```
@unittest.skip("Email temporarily disabled")
def test_send_email(self):
    status = send_email(params)
    self.assertTrue(status) 
```

像这样跳过测试有双重好处。

首先，当您使用这个装饰器时，如果您运行带有详细输出的测试，就会打印出原因(您可以在运行测试时通过使用`--verbose`或`-v`标志来访问这个模式)。当您运行测试文件时，它会提醒您代码库中可能有未使用的功能。意识到代码库的哪些部分可能是不必要的，是实现良好代码健康的第一步。

第二，当您稍后重新启用这段代码时，查找“跳过”(或您作为原因提供的特定字符串)比滚动大块的注释代码来找出要重新启用的内容更容易。额外的好处是，对于新加入你的代码库的人来说，这是一个可读性更好的解决方案。

也有可能您希望对旧版本的代码运行测试，或者您只希望测试在特定的环境下运行。`unittest.skipIf`允许您指定何时应该或不应该运行某些测试。这样，旧代码仍然被覆盖，整个套件不会因为新版本而失败。

这个装饰器的第一个参数是一个语句，如果它的值为`True`，将导致测试跳过。第二个论点是你跳过测试的人类可读的原因。

```
@unittest.skipIf(mylib.__version__ > (1, 3), "email replaced with push notification")
def test_send_email(self):
    status = send_email(params)
    self.assertTrue(status) 
```

还有`unittest.skipUnless`选项，它将跳过修饰测试*，除非*您传递的条件解析为`True`。我想这是你可以使用的一个选项。我不是你的老板。但是在我看来，使用`unittest.skipIf`似乎总是更简单易懂。

```
@unittest.skipUnless(mylib.__version__ < (1, 3) "email replaced with push notification")
def test_send_email(self):
    status = send_email(params)
    self.assertTrue(status) 
```

除了跳过单个测试之外，您还可以跳过整个测试类。将跳过以下两项单独测试。

```
@unittest.skip("Functionality not currently supported")
class MyCoolProcessTestCase(unittest.TestCase):
    def test_cool_process(self):
        result = cool_process()
        self.assertTrue(result)

    def test_less_cool_process(self):
        result = not_as_cool()
        self.assertIsNotNone(result) 
```

# 预期故障

偶尔，当你在一个截止日期前工作时，你可能已经知道了不影响你的发布的失败。在一个完美的世界里，我们会在部署新版本之前修复一切。我们大多数人都没有这种奢侈。例如，您可能知道时间戳由于夏令时处理不当而关闭。如果您知道是这种情况，并且您在其他地方解释了它，您可能想要将测试标记为[预期失败](https://docs.python.org/3/library/unittest.html#unittest.expectedFailure)。

```
@unittest.expectedFailure
def test_timestamps_match(self):
    expected_time = # what you expect to happen
    actual_time = # timestamp created in your code
    self.assertEqual(expected_time, actual_time, "DST might interfere") 
```

如果一个测试在这个装饰器被应用时失败了，它不被算作一个失败。这个工具很有用，但也很危险。将测试标记为预期失败会导致您错过代码中的重大更改。

另外，请注意，我已经将一条简短的信息作为第三个参数传递给了`self.assertEqual`。这是一个为测试增加可读性和上下文的未被充分利用的选项。如果你经常遇到影响你的代码库的特定部分的不可靠的情况，考虑传递关于以前的失败和解决方案的信息。它可能会拯救你的未来——让你头疼。

## 该用哪个？

`unittest.expectedFailure`是处理非破坏性故障的一个选项，但我个人建议偏向于使用`unittest.skip`或`unittest.skipIf`来代替。将跳过的测试和描述性的原因组合在一起是一种组织和跟踪未使用的测试的简单方法。将一个测试标记为预期失败是一个很好的临时解决方案，但是会导致将来更大的失败。如果您关注您的测试覆盖范围，并确保您未使用的测试不会与您的代码库太不同步，那么这两者都是有效且有用的选择。