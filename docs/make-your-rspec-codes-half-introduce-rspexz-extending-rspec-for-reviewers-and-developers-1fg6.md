# 让你的 RSpec 代码减半。RSpecZ 简介为审阅者和开发人员扩展 RSpec。

> 原文：<https://dev.to/seteen/make-your-rspec-codes-half-introduce-rspexz-extending-rspec-for-reviewers-and-developers-1fg6>

前几天在表参道. rb(日本 Ruby meetup)聊过。令人惊讶的是，Matz 参加了那次聚会。

在那里，Matz 对 RSpecZ 进行了评论，这是 RSpec 的瑰宝。
“您应该在 RSpec 存储库中发出一个拉取请求。”

我还没有提出拉取请求。但是我注意到很多人对 RSpecZ 感兴趣。
所以，我先在这里介绍一下 RSpecZ。

# 什么是 RSpecZ？

RSpec 有一个大问题。

RSpec 代码很长(意味着难以审查)。

RSpecZ 正试图解决这个问题。
现在有扩展`let`、`context`、`subject`的方法。我想让 RSpecZ 在未来成为 RSpec 的指导，所以我会制作`generators`、`code analyzer and formatter`之类的东西。

我用图像向你展示 RSpecZ 的有效性。

[![001](../Images/e741ece9197e33c5faeab69e8ce11218.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nEbBDYK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0omvjmh853t9rf7b11pp.png)

这个图像是通过将 RSpecZ 应用到我参与的一个项目的 rspec 代码中而创建的。

我将告诉你这是如何发生的。

# 基本特征

## 扩展上下文

RSpec 的`context`方法灵活且通用。
换句话说，它往往是多余的，难以理解的。

例如，

```
context 'When phone_number is 090-1234-5678' do
  let(:phone_number) { '090-1234-5678' }
  it { expect(phone.save).to be_truthy }
end 
```

Enter fullscreen mode Exit fullscreen mode

(`090-1234-5678`是日本的有效电话号码。)

这个`context`是多余的。因为`context`和`let`展示的是同一个东西。

但是当我们使用 RSpec 时，这种情况会经常发生。

还有一个问题。
RSpec 的`context`本身没有任何意义。`context`第一个参数显示上下文的状态。没有第一个论证，我们无法知道它会产生什么样的语境。

上面的代码意味着将有效的电话号码设置到`phone_number`变量(let)中。RSpecZ 使这段代码变得简单。

```
set_valid(:phone_number, '090-1234-5678') do
  it { expect(phone.save).to be_truthy }
end 
```

Enter fullscreen mode Exit fullscreen mode

`set_valid`方法结合`context`和`let`。
和方法名可以说`valid`的值将被放到`phone_number`中。

当审阅者阅读方法名称时，他可以知道`valid`规格将被写入块中。
并且代码行变短。

使用 RSpecZ，您可以非常容易地编写多个有效案例。

```
set_valids(:phone_number, '090-1234-5678', '080-9566-8466', '070-4567-8901', '09045678945' ) do
  it { expect(phone.save).to be_truthy }
end 
```

Enter fullscreen mode Exit fullscreen mode

与 RSpec 相同的代码将是

```
%w[090-1234-5678 080-9566-8466 070-4567-8901 09045678945].each do |phone_number|
  context "When phone_number is #{phone_number}" do
    let(:phone_number) { phone_number }
    it { expect(phone.save).to be_truthy }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过比较这些代码，您可以知道查看 RSpecZ 代码有多容易。

RSpecZ 在设置了`invalid`值的情况下，不仅有`set_valid`还有`set_invalid`。

选择正确的方法来帮助审查者审查代码。

## 扩展字母

当你写 RSpec 的时候，你可能会写这种代码，

```
let(:name) { 'test-name' }
let(:phone_number) { '090-1234-5678' }
let(:address) { 'test-address' }
let(:params) { {
  name: name,
  phone_number: phone_number,
  address: address
} }

subject { get '/api/test', params: params } 
```

Enter fullscreen mode Exit fullscreen mode

这个`let`可以帮助你改变缺省值的一部分上下文。你可以编写灵活的规范。

然而，这些代码也是多余的。

RSpecZ 有`create_params`方法。

```
let(:name) { 'test-name' }
let(:phone_number) { '090-1234-5678' }
let(:address) { 'test-address' }
create_params :name, :phone_number, :address

subject { get '/api/test', params: params } 
```

Enter fullscreen mode Exit fullscreen mode

这个代码有相同的含义。
`create_params`定义`params` `let`与使用定义的`let` s

实际上，如果参数还没有定义，`create_params`用`test-xxx`字符定义`let`。
所以，下面的代码也有同样的意思。

```
let(:phone_number) { '090-1234-5678' }
create_params :name, :phone_number, :address

subject { get '/api/test', params: params } 
```

Enter fullscreen mode Exit fullscreen mode

以后我会实现更多有用的`let`方法。

## 其他特性

RSpecZ 还有一些其他有用的方法。如果你感兴趣，请查看 github 库。

[https://github.com/RSpecZ/RSpecZ](https://github.com/RSpecZ/RSpecZ)

# 测量 RSpecZ 的有效性

我用比较项目来检验 RSpecZ 的有效性。
一个是没有 RSpecZ 的 rails 项目(我之前参与过)，一个是有 RSpecZ 的 rails 项目(我目前参与过)。

## 处所

*   这些项目完全不同。他们有不同的商业逻辑。
*   我开发了两个项目的代码基础。(基本代码样式相同)
*   与 RSpecZ 的项目刚刚开始(3 个月)，所以代码较少。
*   我们用 RSpecZ 在 project 中使用了 RSpecZ 还没有的其他特性。因此，不仅 RSpecZ 特性使代码更短。

## 结果

|  | RSpec 的行 | 测试数量 |
| --- | --- | --- |
| RSpecZ 项目 | Two thousand one hundred and eighty-three | One thousand two hundred and twenty-three |
| 没有 RSpecZ 的项目 | Twenty thousand and twenty-three | Five thousand one hundred and forty-three |

RSpec 的行数:`request_spec`和`support`文件的总行数。
试验次数:样本数。

## 结论

RSpecZ 可以使 RSpec 代码减半。
(由于前提原因，无法仅比较 RSpecZ 特征)

这可能不准确，但我们可以从结果中知道 RSpecZ 的力量。

# 终于

本文介绍了 RSpecZ 特性。

RSpecZ 是几个月前刚刚开始的。我们的佣金很少。

有兴趣请评论。