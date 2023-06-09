# Python 中的测试组织

> 原文：<https://dev.to/danielw/test-organization-in-python-14kk>

很长一段时间以来，Python 一直是我选择的黑客和概念验证语言。

因此，用 Python 进行测试对我来说从来都不是一个重要的问题，因为我不会在生产中使用代码，甚至大部分时间都不会重温它。

最近，我一直在用 python 编写一个小烧瓶应用程序，用 Raspberry Pi Zero W 校准并连续[读取 HX711 称重传感器。](https://tutorials-raspberrypi.com/digital-raspberry-pi-scale-weight-sensor-hx711/)

由于我将这段代码作为我的学士论文的一部分，我认为它是“生产”代码，最重要的是，我希望它易于重构和扩展。

所以我第一次不得不考虑用 Python 进行测试。

* * *

### 设置

首先，我的没有测试的项目看起来像这样:

[![Output of tree command on the root of the project directory containing my code without tests](img/fbea47cbcf3970c0c1d408d8a4a54dc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vv-AoOe4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5yz2ssskc60j0jypivbv.png)

正如你所看到的，这是一个非常简单的设置，有两个模块*后端*和*服务*以及一个入口点`server.py`。

### 目标

为了易于测试，我希望能够为每个模块定义测试，并能够单独运行它们，以及一种运行所有模块的所有测试的简单方法。

### 解

为了测试，我将使用 python`unittest`模块。使用 unittest 进行测试的最小单元是`TestCase`，它可以有一个或多个形式为`def test_*():`的方法。

首先，我试图将模块的不同职责分成`TestCase`类，例如:

```
 import unittest

    class TestCalibrationChecks(unittest.TestCase):

        """
        methods that test whether all checks on the calibration state of the scale work
        """

    class TestCalibrationProcess(unittest.TestCase):

        """
        methods that test calibration of the scale
        """ 
```

Enter fullscreen mode Exit fullscreen mode

在为模块编写测试用例之后，我的项目看起来像这样:

[![Output of tree command on the root of the project directory containing my code with module tests](img/914b5a41b07b4a2725f8beee4eeddb6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hah-hd7K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0my71fyjosuelhaczunp.png)

从项目根运行一个测试用例，如下所示:`python -m unittest backend.test.calibration_test.TestCalibrationChecks`

现在我希望能够运行每个模块中的所有测试，所以我四处查看了[文档](https://docs.python.org/3/library/unittest.html#grouping-tests)，来到了 unittest 的`TestSuite`。

一个`TestSuite`是一个*测试用例*和/或*测试套件*的集合，可以用来将测试分组在一起。

因此，在我的模块的根中，我写了一个文件`tests.py`，将该模块的`test`文件夹中的所有测试用例合并到一个套件中。
对于后端模块，它看起来像这样:

```
 import unittest

    from backend.test import TestCalibrationChecks
    from backend.test import TestCalibrationProcess
    from backend.test import TestSystemHealthChecks

    def test_scale_suite():
        scale_test_suite = unittest.TestSuite([
            unittest.TestLoader().loadTestsFromTestCase(TestCalibrationChecks),
            unittest.TestLoader().loadTestsFromTestCase(TestCalibrationProcess),
            unittest.TestLoader().loadTestsFromTestCase(TestSystemHealthChecks)
        ])
        result = unittest.TestResult()
        runner = unittest.TextTestRunner()
        print(runner.run(scale_test_suite))

    if __name__ == '__main__':
        test_scale_suite() 
```

Enter fullscreen mode Exit fullscreen mode

在为每个模块编写了这样一个文件之后，我的项目看起来是这样的:

[![Output of tree command on the root of the project directory containing my code with module tests and tests.py in each module root](img/c069c74f22df5a97fc450b31b658e073.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZRkvTGNb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/osew3cqa1vu5x4h86fru.png)

我现在能够从项目根用这样的命令运行每个模块的测试了:`python -m unittest backend.tests`。

但是，用一个命令运行所有模块的所有测试怎么样呢？

通过对`backend/tests.py`和`service/tests.py`的微小重构，我能够在根目录中编写一个`tests.py`，并将*后端*和*服务*测试套件组合成一个更大的`TestSuite`

首先，我改变了每个模块中的`tests.py`，这样`TestSuite`就不会在函数中创建，而是一个导出变量:

```
 import unittest

    from backend.test import TestCalibrationChecks
    from backend.test import TestCalibrationProcess
    from backend.test import TestSystemHealthChecks

    scale_test_suite = unittest.TestSuite([
        unittest.TestLoader().loadTestsFromTestCase(TestCalibrationChecks),
        unittest.TestLoader().loadTestsFromTestCase(TestCalibrationProcess),
        unittest.TestLoader().loadTestsFromTestCase(TestSystemHealthChecks)
    ])

    def test_scale_suite():
        result = unittest.TestResult()
        runner = unittest.TextTestRunner()
        print(runner.run(scale_test_suite))

    if __name__ == '__main__':
        test_scale_suite() 
```

Enter fullscreen mode Exit fullscreen mode

现在我能够在项目根中定义`tests.py`如下:

```
 import unittest

    from backend import tests as backend_tests
    from service import tests as service_tests

    complete_test_suite = unittest.TestSuite([
        backend_tests.scale_test_suite,
        service_tests.service_test_suite
    ])

    def run_all_suites():
        result = unittest.TestResult()
        runner = unittest.TextTestRunner()
        print(runner.run(complete_test_suite))

    if __name__ == '__main__':
        run_all_suites() 
```

Enter fullscreen mode Exit fullscreen mode

完成的项目现在看起来像这样:

[![Output of tree command on the root of the project directory containing my code with module tests, tests.py in each module root, and a global tests.py in the project root](img/d4c7be327def03c518e93c0cebd8bb1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eFYpEK95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4kfpcrc0gfiskfhgwm7.png)

从项目根目录，我可以运行

*   所有测试`python -m tests`
*   后端测试`python -m unittest backend.tests`
*   服务测试`python -m unittest service.tests`

*任务完成*

#### 备注

*   我知道有一个函数`unittest.TestLoader().discover()`会自动发现文件树中的所有测试用例。我选择不使用它，因为我喜欢显式地声明要运行什么，这样我就可以看到会发生什么，并避免一些测试因为发现者出于某种原因错过了它们而没有运行。
*   我并不是说这是用 Python 组织测试的正确方法，只是到目前为止，它对我来说工作得很好。很高兴听到您对此的评论:)