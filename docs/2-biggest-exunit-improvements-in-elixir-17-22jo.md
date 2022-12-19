# 酏剂 1.7 中最大的两项改进

> 原文：<https://dev.to/blackode/2-biggest-exunit-improvements-in-elixir-17-22jo>

[![Created using crello.com](../Images/c74c917deacaef45fffcf9bfa776e987.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QabqT9ab--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2ARKP2KBCHBNoDf3V_1_JlcQ.jpeg)

*   失败报告中的参数
*   运行带有失败标志的混合测试

## 1。失败报告中的参数

我们通常会做这样的断言

> # [T2】断言是 _ 有效 _ 卡(卡 _type，卡 _number)](#assert-isvalidcardcardtype-cardnumber)

没有在单元测试用例中使用任何类似于`< > <= == >=`的比较操作符。每个人都喜欢聪明。当然，我也是。

**个人经历或印象写这篇文章:**

我有一个关于地图特殊行为的经验，有两种类型的键允许在仙丹中写一个`map`。

我使用了带有`binary`键的`map`，并试图在带有`atom`类型的**键**的地图上测试它们。每次我这么做，测试都会失败。虽然我发送了一个正确的`map`，但是由于键是不同的类型，它失败了。

我花了一整天才弄明白。🌞

我总是试图调试通过的代码 ***逻辑*** 而不是 ***参数*** 。如果之前错误报告有这个功能，我这辈子就能省一天了。

幸运的是，我们在最新版本中有。

***Elixir 1.7*** 版本足够智能，可以告诉我们函数的参数，这里是`is_valid_card(card_type, card_number)`。

作为一个程序员，我不喜欢文档化的证明。我总是更喜欢`test_cases`。好吧，我们来`test`一下这个功能。

在继续之前，我想让你知道我在写这篇文章时使用的版本。

[![**Elixir** and **Erlang** **OTP** versions](../Images/26dc622fc662e640015506733c5003fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m8PjvMWt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/8576/1%2AtHDoidj5NGMYz6ost2pwKQ.png)** *仙丹* *和**二郎** **OTP** 版本*

为了测试这个特性，我们将创建一个项目`basic_math`和一个简单的函数`is_valid_card/2`，这完全是一个**白痴**的函数。用于检查`card_type`给定的`card_number`是否有效。

我们不太担心在这里实现完整的逻辑。函数`is_valid_card/2`没有做任何有用的事情，它只是根据`card_type`判断`card_number`的长度。

```
 $ mix new basic_math && cd basic_math 
```

上面的 shell 命令创建一个新的 Elixir 项目，并将当前工作目录更改为`basic_math`。

希望你们都知道这个项目的结构。如果你不知道的话，看看我的第一个项目。

打开文件`lib/basic_math.ex`，放入下面几行代码。

```
 #lib/basic_math.ex

defmodule BasicMath do

  def is_valid_card(card_type, card_number) do
    case card_type do
      :debit ->
        card_number
        |> Integer.to_charlist()
        |> length
        |> Kernel.==(12)

       :credit ->
        card_number
        |> Integer.to_charlist()
        |> length
        |> Kernel.==(10)

       _ ->
        {:error, :invalid_card_type}
    end
  end
end 
```

添加代码后，文件如下图所示…

[![](../Images/79269b92503b537fe7bf46542e585a4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qna3j39j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2808/1%2AwpLmHTLkFpoo7pBneNioXA.png)

### 关于 basic_math.ex 文件中代码的快速文档。

定义`is_valid_card/2`接收`two`参数`card_type`和`card_number`。我们正在`card_type`上进行案例匹配。它匹配两个 case 值`:credit & :debit`，我希望你已经理解了剩下的代码。没有什么比举起 1 克的石头更难的了。不是那样的，我只是在开玩笑。

### 单元测试用例

现在，我们将编写一些单元测试用例来测试我们的代码。

> # Before use, test the code.

打开文件`test/basic_math_test.exs`并添加以下代码行

**基本设置**

添加以下代码

```
defmodule BasicMathTest do
  use ExUnit.Case
  import BasicMath
  doctest BasicMath

end 
```

**借记卡测试用例**

```
describe "debit_card" do

     setup do
    {:ok, type: :debit, valid_card: 123456789012, invalid_card: 1234567890} 
     end

     test "valid debit card", %{type: type, valid_card: valid_card} do
      assert is_valid_card(type, valid_card)
     end

    test "in-valid debit card", %{type: type, invalid_card: invalid_card} do
      refute is_valid_card(type, invalid_card)
    end

end 
```

**信用卡测试用例**

```
describe "credit_card" do
    setup do
      {:ok, type: :credit, valid_card: 123456789012, invalid_card: 1234567890} 
    end

    test "valid credit card", %{type: type, valid_card: valid_card} do
      assert is_valid_card(type, valid_card)
    end

    test "in-valid credit card", %{type: type, invalid_card: invalid_card} do
      refute is_valid_card(type, invalid_card)
    end

end 
```

出于演示的目的，我故意写了一些失败的测试用例。

整个文件如下图所示

[![basic_math_test.exs](../Images/1ae5860588bc6b1893f5959335fd83e9.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--s0ckicsm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3796/1%2Aq4J3Mt8u9_cfBe-dDYvc3A.png) *基础 _ 数学 _ 测试. exs*

在 credit_card 部分，我们使用了与 debit_card 单元测试用例相同的设置数据，最终导致测试用例失败。

因此，它现在将在失败报告中包含每个参数的值:让我们检查一下。

```
$ mix test 
```

使用上面的命令运行您的测试用例。您将看到类似于下面截图的输出。

[![mix test … testing the file basic_math.ex](../Images/860ce1b9d94314d22bf4764c47852a50.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Qcm9QJ6k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2ASlPz6vBSKVjYfy_84OMpRQ.png) *混合测试…测试文件 basic_math.ex*

如果您观察上面的截图，在**四(4)、*** *两(2)* *个测试用例中，您可以在失败报告中看到传递给测试用例的参数。

```
arguments:

   #1
   :credit

   #2
   123467890 
```

当我们的函数和太多的函数联系在一起时，这种失败报告非常有用。当函数依赖于许多其他函数时，很难调试单元测试用例的失败原因。这就是参数在调试代码失败中发挥重要作用的地方。

这是一个非常健康的特征。💚

## 2。运行带有失败标志的混合测试🚩

当您运行带有标志- failed 的命令混合测试时，它仅运行在您之前的混合测试尝试中失败的测试用例。

这意味着这一次任务混合测试失败不要再执行成功的测试用例。所以，我们可以节省时间。

失败的单元测试用例在第一个混合测试中被跟踪和缓存

我们在文件中总共有 4 个单元测试用例。但是，当您添加了失败选项时，它只给出了两个失败的单元测试用例。

您可以在下面的截图中观察到这一点。

[![mix test — failed](../Images/df5cf3f00163ee4e0b8a96e9afcfe119.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--VQ1pn9rK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A39Qx0qT-tikAADaBFcpRCQ.png) *混测—失败*

希望你喜欢。

快乐的长编码生活…:)