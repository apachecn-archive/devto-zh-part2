# 在 Ruby 中 5 分钟内完成简单的 TDD

> 原文：<https://dev.to/chenge/simple-tdd-in-5-minutes-in-ruby-1ik2>

考虑到我夸张的标题，我尽量简单解释一下。

测试驱动是 Kent Beck 的一个巨大进步，它增强了开发人员的信心和代码质量，并减缓了开发人员的头发变白。虽然已经流行 10 年了，但还是看到有人说这个技术难。我不这么认为。这项技术本身非常简单。下面我用一个简单的例子来说明。

# 创建目录

我用一个简单的计算器来说明，创建一个空目录 calc-tdd

```
mkdir calc-tdd
cd calc-tdd
touch test_calc.rb
touch calculator.rb 
```

现在我们有了目录和两个文件，让我们开始编写代码 test_calc.rb，首先编写测试。

# 写测试

```
#test_calc.rb

require "minitest/autorun"

require "./calculator.rb"

class TestCalculator < MiniTest::Test
    def test_add
        calc = Calculator.new
        assert calc.add(1, 2) == 3
    end
end 
```

测试完成后，就可以运行了。

```
ruby test_calc.rb 
```

将报告结果，因为代码尚未实现。

# 实现代码

```
#calculator.rb

class Calculator
    def add(a, b)
        a + b
    end
end 
```

好，再次运行测试，你会发现代码通过。很简单，看表五分钟。

接下来，您可以自己添加一个减法测试 sub。

后续可以学习一下 setup 和 teardown 的用法，当然也有很多值得学习的地方，比如 RSpec。

# 总之

TDD 是一个巨大的进步，基本概念很简单。当然，测试本身就是一个复杂的领域，要成为测试专家还有很多要学的。