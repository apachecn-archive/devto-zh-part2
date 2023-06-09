# Luhn 测试 Python 中的形

> 原文：<https://dev.to/franiglesias/luhn-test-kata-en-python-3en0>

今天我要做一个有趣的实验:在 Pyhton 的一个 kata。我玩了一个季节的语言，我可以尝试一些小东西。

## Sobre la Luhn 测试形

其想法是开发一种功能来测试信用卡号码，以查看它们是否有效或只是随机选择的数字。

[你可以在 Manuel Rivero 的这个 gist](https://gist.github.com/trikitrok/c2944e1bbbca51127854491ae3720559)中看到练习及其细节，后者在工艺管理巴塞罗那软件的一次会议上介绍了 kata，增加了一个复杂性:创建一个单一的函数或方法来进行验证，只测试公共界面，并在*婴儿步骤中进行验证。【t*

这意味着不要使用可单独测试的协作对象，也不要通过测试专用方法或类似方法来欺骗测试。也就是说，不要在第一次迭代中生成整个算法，而是强迫逐步进行。

他有他的女朋友，这就是我打算在这篇文章里做的。

## 准备环境

与 Python 合作的一个好 IDE 是 [PyCharm，来自 Jetbrains](https://www.jetbrains.com/pycharm/) 。很明显，来自 PHPStorm 公司的 IDEs，使用 PyCharm 是在熟悉的领域，这使事情变得非常容易。此外，他们提供免费版的 PyCharm 社区，所以你什么都赞成。

在 PyCharm 启动一个新项目很简单，你只需要给它取个名字，然后选择 Python 直译器。我选择了[虚拟环境](https://rukbottoland.com/blog/tutorial-de-python-virtualenv/)这种东西，就像创造受控环境来开发一个项目一样。(*免责声明*:我对此很新手，所以任何意见都表示感谢。

就这样了。

## 帝企鹅 con un 测试

第一个是从测试开始，所以我会创建第一个文件，我会将其命名为**【luhnvalidator test . py】**。

在此文件中，我将扩展“`unittest.TestCase`”类以开始编写我的单元测试。这样做:

```
import unittest

class LuhnValidatorTest(unittest.TestCase):

    def test_all_zeros_is_valid(self):
        luhn_validator = LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000000')) 
```

我会解释这个测试以及我是如何做到的。

### 用 Python 编写测试

第一行是主张模块`unittest`，它是 Python 的串行产品，是我们可以在此语言中使用的测试框架之一。

`LuhnValidatorTest`类扩展`unittest.TestCase`类，两点指出语句的结尾和块的开头。

对于我们不熟悉 Python 的用户，应该说，在此语言中缩进非常重要，因为它定义了代码块。因此，在声明行下面，我们有具有缩进级别的实施行，当我们返回到上一缩进级别时，块结束。

缩进不足的 Python 代码无法正常工作，这与其它语言不同。值得注意的是，代码的视觉结构有助于理解代码，使代码非常可读，而且这是个人印象，有助于保持嵌套控制。

`def`是定义函数的关键字，在本例中是 luhnvalidatortest 类的方法。

### 分析问题，选择第一次测试

选择 TDD 中的第一个测试总是一个小挑战，解决它是通过实践和审视我们面前的问题来完善的。TDD 过程本身通常也证实了你选择的是正确的还是需要重新考虑的。

一个澄清:我不会考虑验证作为一个参数传递的字符串长度的整个问题，也不会考虑我在一个案例中会作出的其他考虑因素，因此我们将重点放在这项工作的核心。

对于测试本身，在本练习中，考虑验证算法的工作原理非常重要。要点包括:

*   输入数据是一个 11 位数的字符串。
*   首先，反转数字字符串。
*   然后，占据平等和不偏不倚地位的人被分别对待。
*   奇数组只是加入。
*   如果对组大于 10，则将结果中的数字相加，然后将结果中的数字相加，再将结果中的数字相加。
*   然后再加上奇数和偶数的结果。
*   如果结果为 10 的倍数(即结尾为零)，则输入的卡号有效。

通过分析这一流动，我们可以看到，交易是总和和乘积，而产品是相等总和，我们有一个有趣的开始工作的数字，即 0，这是整数总和的中性要素。仅由零组成的卡号是有效的，因为执行的一系列计算将导致零。因此，它是理想的第一次测试，它将迫使我们创建类和方法。

## 从生产代码开始

我在上面展示的测试显然不会通过。首先，它要求我们创建 LuhnValidator 类。

### python 代码组织

我会用它来解释一些 Python 的东西。就像测试一样，我们可以在同一文件中创建类。事实上，如果你用派沙姆沙伊赫的意图来做的话，他就会这样做。

因为我们想把测试代码和生产代码分开放在不同的文件中，所以我们不得不导入它。python 文件是一个模块，可以包含函数或类。要从其他地方使用它们，我们只需对模块名称使用指令`import`，因此要使用 LuhnValidator，我们需要用模块名称前缀:

```
import unittest

import luhnvalidator

class LuhnValidatorTest(unittest.TestCase):

    def test_all_zeros_is_valid(self):
        luhn_validator = luhnValidator.LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000000')) 
```

但也有另一种方法可以做到这一点，在这种方法中，我们更清楚地了解我们想进口什么:

```
import unittest

from luhnvalidator import LuhnValidator

class LuhnValidatorTest(unittest.TestCase):

    def test_all_zeros_is_valid(self):
        luhn_validator = LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000000')) 
```

这与其他语言中的命名空间类似。脚注的结尾。

### 通过考试的最低码

Helo 在这里，在文件/模块 **luhnvalidator.py** 中:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        pass 
```

`__init__`一直是物体的构造者，在我们的情况下是不需要的。

`pass`是表示函数或方法无所作为而返回`None`的一种方法。

当然，这不会通过测试，但到目前为止，它向我们保证，我们已经落实了必要的最低要求。我们只需要让它返回一些东西:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        return True 
```

现在测试通过了。

事实上，True 和 False 都是 Python 中的第一个大写字母。

## 寻找新的例子

我们必须回到问题的说法。我们要做的第一件事是反转传入的链，所以最好有一个测试，以某种方式证明我们已经反转了它。

这就排除了所有数字相同或形成综合症的卡号:我们想要的是一种在顺序颠倒时发生变化的东西，并且尽可能简单。

我们的第一次测试迫使我们实现一些非常基本的东西:验证器总是返回 True，所以我们希望下一个示例无效，这将迫使我们实现最小的算法。其实有两件事：

*   数字的反转
*   算法的一小部分

这就是我们所拥有的:

*   如果信用卡号码不对称，则可以强制反向数字，例如，所有数字在一端除一个数字之外均相等。
*   最简单的方法是使用投资后的奇数位数，因为只需将这些位数相加即可。
*   下降的数字，而不是偶数，我将不得不乘以二，目前的理想情况是，它们小于 5，这样我就不必再加总得到的数字。
*   为零的数字不会影响计算，因此我可以将您不需要的所有数字保留为零。
*   如果再加上原始数字的结尾将在反向序列的第一个位置结束…

我的下一个例子将是

但是，等一下，∞一个有效或无效的数字？我们一直认为不可能。

事实上，如果我们使用该算法，我们会发现我们的示例无效，因此，我们对代码当前状态的测试不会通过，这就是我们想要的。

为什么这个例子具体而不是另一个？

好吧，我的兴趣是证明我们反转了引入的卡号，所以我将实施一个测试，以确保如果我只考虑投资后的第一位数字，则该数字不是零，因此链已被反转。如果我只考虑第一个数字，而其他数字为零，则评估卡是否有效所需的总额的结果将等于第一个数字。

```
import unittest

from luhnvalidator import LuhnValidator

class LuhnValidatorTest(unittest.TestCase):

    def test_all_zeros_is_valid(self):
        luhn_validator = LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000000'))

    def test_credit_card_with_only_last_digit_significant_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000000001')) 
```

测试失败，所以我们要实施。

首先是反转字符串，Python 是这样简单:

```
inverted = card_number[::-1] 
```

但这并不能通过测试，必须实现算法的最小一部分，即简单地看到反向链中第一位的值。如果不是`0`我们返回 False，因为它是无效的:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        if inverted[0] != "0":
            return False
        return True 
```

这是第一步，也许不足以证明我们想要的一切。但有趣的是，我们正在逐步前进，这就是运动的意图。

## 测试必须强制实施

TDD 的一个前提是，任何生产代码只能作为失败测试的响应而创建。如果我们现在编写一个通过的新测试，我们将无法修改实施。不是它，只是在这个时候，如果我们写了一个新的测试来通过，它并没有告诉我们应该实施什么。它只是证实了我们已经知道的事情。不管怎么说，这种刚写好的测试，可以作为“**回归**”的测试，如果有一天出现故障，表明有东西在改变算法。

我们现在感兴趣的是对示例进行一些更改，以强制对实施进行更改，从而复盖范围。我们特别想把奇数位置的数字加起来。

一种方法是添加一个新的数字，该数字在切换链时处于奇数位置，但更改验证结果，以便卡号有效。这意味着我们输入的数字必须加 10。

一个例子是□，但也值得其他如□或□

但是，有一个简单得多的测试。我们只需把 1 放在我们感兴趣的位置上:3 如果算法不考虑该位置，则测试将失败，并且必须将其输入到计算中:

何 AQíEl 试验:

```
import unittest

from luhnvalidator import LuhnValidator

class LuhnValidatorTest(unittest.TestCase):

    def test_all_zeros_is_valid(self):
        luhn_validator = LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000000'))

    def test_credit_card_with_only_last_digit_significant_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000000001'))

    def test_credit_card_with_only_third_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000000100')) 
```

在确保测试失败后，让我们进入生产代码。

问题是，我有两个选择:一方面，我现在只需检查这两个位置中的任何一个都有非零数字即可通过这个具体测试。

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        if inverted[0] != "0" or inverted[2] != "0":
            return False
        return True 
```

换句话说，上述测试并没有强迫我实施该金额，因为它可以用另一种算法来掩盖。这就是为什么现在我要做一个测试来证明两个数字加 10 就能得到一个有效的卡号:

```
def test_credit_card_with_two_digit_that_sum_ten_is_valid(self):
        luhn_validator = LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('00000000406')) 
```

而测试失败了，因为我的算法不加那些掉在奇数位置的数字。我感兴趣的是开始加入他们，最容易的是直接积累我感兴趣的两个职位。因为我可以忽略剩下的零位数，所以我只需看看结果是否可以除以 10。

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = int(inverted[0]) + int(inverted[2])
        if even % 10 == 0:
            return True
        return False 
```

在此通过测试的示例中，您可以看到 Python 的一些有趣的详细信息，例如:

*   通过指示较大字符串的位置并考虑到字符串是零索引(即第一个位置为零)来访问较大字符串中的字符。
*   字符串转换为 int，函数为`int()`。
*   我们不需要在 if 结构中使用括号。

## 寻找更一般的算法

我们还没有多少东西可以强迫自己写出一个更一般的算法。这就是为什么我们需要另一个测试，如果我们想让它发挥作用，它必须失败。当我们可以观察到重复或我们可以用其他方式表达的某种规律性时，代码会要求我们有一个更一般的算法。

我们将提出以下测试，这迫使我们在验证时考虑第三位数字。现在我已经确定了算法是基于求和的，我可以回到我以前的最小化策略。下面是一个例子(从现在开始，我只介绍具体的测试):

```
def test_credit_card_with_only_fifth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000010000')) 
```

这个测试没有通过，因为算法还没有考虑到第五位。所以现在要做的是:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = int(inverted[0]) + int(inverted[2]) + int(inverted[4])
        if even % 10 == 0:
            return True
        return False 
```

有了这种变化，测试就通过了。好消息是，我们已经开始看到反应物的可能性:计算奇数的总和不仅是重复的，而且开始变得难以阅读。我们可以先从一个方法来解释:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        if even % 10 == 0:
            return True
        return False

    def add_even_digits(self, inverted):
        even = int(inverted[0]) + int(inverted[2]) + int(inverted[4])
        return even 
```

原则上，我们可以继续进行*婴儿步骤*直到复盖所有奇数位置，一个接一个，直到总计六个，为每个位置添加一个测试，但在现阶段，我们似乎已经很明显可以用一个循环推广计算方法。在本练习中，我将逐一展示测试和最终制作代码。以实际工作为例，*婴儿步骤*的大小可以调整，只要我们能确定在任何时候最明显的实施是什么。

这是测试。关键是，每个人都必须考虑到我们到目前为止拥有的东西:

```
def test_credit_card_with_only_seventh_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00001000000'))

    def test_credit_card_with_only_nineth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00100000000'))

    def test_credit_card_with_only_eleventh_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('10000000000')) 
```

这是生产代码，相当丑陋。

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        if even % 10 == 0:
            return True
        return False

    def add_even_digits(self, inverted):
        even = int(inverted[0]) + int(inverted[2]) + int(inverted[4]) + int(inverted[6]) + int(inverted[8]) + int(inverted[10])
        return even 
```

是时候做一个反应器，保持测试绿色:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        if even % 10 == 0:
            return True
        return False

    def add_even_digits(self, inverted):
        even = 0
        for position in range(0, 11, 2):
            even += int(inverted[position])
        return even 
```

有了这个，我们已经有两个部分的算法被复盖了。我们现在必须采取对等的立场。

## 加入福利

对等立场给我们带来了一些挑战:

*   首先我们要把每一个数字乘以二。
*   如果结果大于 10，我们必须将结果数字相加。
*   最后，我们必须把所取得的一切加起来。

最后，我们将结果与奇数位数字相加，以确定卡号是否有效。

但是，我们可以用与前一种类似的方式来解决这个问题。一方面，我们需要忽略奇数的立场，因此在我们的例子中将它们归零。以`1`作为测试的数字似乎是个不错的主意，因为它目前并不要求我们实施减少结果的阶段，如果它大于 10 的话。

所以我们要做个测试：

```
def test_credit_card_with_only_second_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000000010')) 
```

测试失败，让我们实施一些事情来通过测试:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        odd = int(inverted[1]) * 2
        if (even + odd) % 10 == 0:
            return True
        return False

    def add_even_digits(self, inverted):
        even = 0
        for position in range(0, 11, 2):
            even += int(inverted[position])
        return even 
```

我们可以继续使用与以前相同的模式，将数字移动到下一对位置，一次一个测试。简单来说，我将只介绍我所做的测试(我向你保证，我是通过“婴儿步骤”来到这里的):

```
def test_credit_card_with_only_fourth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000001000'))

    def test_credit_card_with_only_sixth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000100000'))

    def test_credit_card_with_only_eigth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00010000000'))

    def test_credit_card_with_only_tenth_digit_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('01000000000')) 
```

这些测试让我可以在重构完成后编写代码:

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        odd = self.add_odd_digits(inverted)
        if (even + odd) % 10 == 0:
            return True
        return False

    def add_odd_digits(self, inverted):
        odd = 0
        for position in range(1, 10, 2):
            odd += int(inverted[position]) * 2
        return odd

    def add_even_digits(self, inverted):
        even = 0
        for position in range(0, 11, 2):
            even += int(inverted[position])
        return even 
```

我们还有一项要求有待执行。如果偶数的两倍大于或等于 10，我们必须加总该产品的数字，并加总该产品。因此，我们必须用一些例子来检验这种情况，迫使我们执行。

例如，案例应该给我们一个无效的数字，根据这个测试:

```
def test_credit_card_with_only_second_digit_five_is_invalid(self):
        luhn_validator = LuhnValidator()
        self.assertFalse(luhn_validator.is_valid('00000000050')) 
```

而失败是因为 5 的两倍是 10，强制方法返回 true。如果将这些数字相加，则结果为 1，这将导致验证失败，从而使我们的测试通过。

```
class LuhnValidator:
    def __init__ (self):
        pass

    def is_valid(self, card_number):
        inverted = card_number[::-1]
        even = self.add_even_digits(inverted)
        odd = self.add_odd_digits(inverted)
        if (even + odd) % 10 == 0:
            return True
        return False

    def add_odd_digits(self, inverted):
        odd = 0
        for position in range(1, 10, 2):
            double = int(inverted[position]) * 2
            if double >= 10:
                double = int(str(double)[0]) + int(str(double)[1])
            odd += double
        return odd

    def add_even_digits(self, inverted):
        even = 0
        for position in range(0, 11, 2):
            even += int(inverted[position])
        return even 
```

这是一个相当丑陋的代码，但它做它的工作。

我们有 14 个测试测试我们算法的一小部分。是时候确保一切正常运转了。我们可以试试在加泰罗尼亚提出的例子。它将作为验收测试。事实上，我可能一开始就写下来了。我将把它放在另一个文件中:**luhnaceaccept test . py**

```
import unittest

import luhnvalidator

class LuhnAcceptanceTest(unittest.TestCase):

    def test_luhn_validates_real_card_number(self):
        luhn_validator = luhnvalidator.LuhnValidator()
        self.assertTrue(luhn_validator.is_valid('49927398716')) 
```

你知道吗？测试通过。

而且有趣的是，考虑如何通过 TDD 和良好的验收测试来减少处理代码缺陷的需要，从而缩短开发时间。

## 总结

为了成为我第一篇关于 Python 的文章，我一点也没变坏，∞是吗？

但是，我们需要更多的东西来考虑完成的练习。也许我们可以重新编写代码。此外，我们将缺乏通过将对等和非对等立场结合起来来控制简单案例的测试，以及在验收测试中的更多示例。