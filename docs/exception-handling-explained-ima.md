# 解释了异常处理

> 原文：<https://dev.to/jgauffin/exception-handling-explained-ima>

这篇文章解释了什么是异常处理，以及它与传统的使用错误代码的错误处理有何不同。这篇文章并没有将
引入用法或异常类，而只是解释它们的用途。

让我们从错误开始。

# 错误简介

从编程之初，错误代码就被用来处理应用程序中的错误。错误代码用于指示
执行的功能是否成功。

这里有一个简单的例子:

```
public bool SaveDefaultAccount(string userName, string accountName)
{
    int userId = GetUserIdFromName(userName);
    if (userId == -1)
        return false;

    int accountId = GetAccountFromName(accountName);
    if (accountId == -1)
        return false;

    return StoreDefaultAccount(userId, accountId);
} 
```

这个例子说明了当您使用错误代码时，如果出现问题，API
消费者必须中止。你可能会，但是团队的其他成员，或者那些在你之前维护应用程序的人呢？这就像你会让整个警察队伍退休，并期望所有的
*公民守规矩，做守法的好公民。*

 *一个开发人员可能很懒，只是将
代码写成(或重构)如下:

```
public bool SaveDefaultAccount(string userName, string accountName)
{
    int userId = GetUserIdFromName("Arne");
    int accountId = GetAccountFromName("Savings account");
    return StoreDefaultAccount(userId, accountId);
} 
```

问题是代码看起来完全合法，但却忽略了
错误。例如，如果你错误地在账户选择页面的所有
账户名称后添加了一个空格，上面的代码就会开始
失败。更糟糕的是，直到依赖于默认帐户的
代码开始失败，你才会知道，而那可能是
过一会儿。追踪随后的错误可能很有挑战性，主要是如果默认帐户以不同的方式设置。

错误代码很容易上手，而且非常强大。甚至像 Go-lang 这样的现代语言也使用错误而不是异常。下面是
的一个 go 片段:

```
f, err := os.Open("filename.ext")
if err != nil {
    log.Fatal(err)
} 
```

虽然错误易于理解和使用，但它们有三个明显的缺点:

## 它们不传达上下文

本部分针对只使用错误代码的语言。

例如，在 Windows 中，错误代码 2 表示“找不到文件”。没有办法说明是哪个文件，也没有其他信息可以帮助你理解文件丢失的原因。错误代码就是这样。指示特定错误的代码
，没有上下文或线索。

问题是很难理解错误发生的原因或环境。Windows API 通过
引入一个叫做`GetLastError()`的方法来解决这个问题，这个方法用来获得更多关于错误的信息
。

```
OFSTRUCT buffer;
HFILE hFile = OpenFile("d:\\sample.txt", &buffer, OF_READ);
if (hFile == HFILE_ERROR)
{
    // all this is required to get the error message.
    // you typically add it to a separate function

    // Get the error code, as hFile only indicates an error
    // but not which one.
    var errorCode = GetLastError();

    // Get the generic error message which
    // represents the above error code.
    LPVOID lpMsgBuf;
    LPVOID lpDisplayBuf;
    FormatMessage(
        FORMAT_MESSAGE_ALLOCATE_BUFFER | 
        FORMAT_MESSAGE_FROM_SYSTEM |
        FORMAT_MESSAGE_IGNORE_INSERTS,
        NULL,
        errorCode,
        MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
        (LPTSTR) &lpMsgBuf,
        0, NULL );
} 
```

我的观点是，当你想
*解决*错误时，一个错误代码是不够的。

## 负担在函数调用者身上

可以肯定地说，所有的应用程序都有错误，错误可以被忽略是极其罕见的。忽略错误可能看起来有效，但迟早会出现相应的问题。
找到根本原因会困难得多，因为发现的错误
只是第一个错误的结果。任何一种“修复”都只是一种
解决方法，它会使代码变得混乱，但并不能防止根本原因
再次发生。

在代码中处理所有错误是至关重要的。当你使用
错误代码时，很容易忽略或忘记错误。时间紧迫的
截止日期？与一个模糊的错误或不可理解的需求搏斗？
这些情况使得走捷径变得如此容易。如果不是你团队中所有的
开发人员都有相同的纪律，错误将会被
忽略。

# 输入异常

例外是针对特殊情况的。如果事情不像预期的那样发展，你就有了一个例外的情况。

听起来很简单，是吧？

但这意味着什么呢？

## 一个例外的例子

如果你预料到某件事会失败，你应该防范那种情况。

这里有一个例子:

假设你喜欢购买最新、最热门的科技产品。当新的小工具发布时，你想成为第一个。

[![](img/b28980f3f9269835028b7680d02b079b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5WqDUfIJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o83dfiz9eacxmggbl7pf.jpg)

### 场景 1

如果你像我们大多数人一样，你可能不会只是去疯狂购物。你需要确保你有足够的钱。

1.  检查你的银行账户。
2.  去逛街。
3.  支付

### 场景二

然而，如果你足够幸运，有足够的钱，你可以直接去购物:

1.  去逛街。
2.  支付

### 区别

在第一个场景中，我们有一个需要处理的已知问题:
金钱限制。因此，我们总是需要检查我们是否有足够的
钱。在第二种情况下，我们应该有足够的钱。

在场景 2 中，如果有人攻击了我们，会发生什么？

1.  有人黑了我们
2.  去买东西
3.  支付

在这种情况下，我们得到了一个例外，因为这是一个不应该发生的情况，因为我们 ***期望*** 有足够的钱。

这是一种特殊情况。

### *为什么我们总不能像场景 1 那样写代码？*

首先，它是关于交流意图。我们通常会得到一组我们应该满足的业务需求。它们告诉我们当
实现不同的用例时会发生什么。如果我们去添加许多对
可能发生但不应该发生的事情的检查，我们就会失去
用例与代码之间的联系。很难说出
代码的意图，这反过来导致假设，并最终降低代码
的质量。

第二，如果我们增加许多支票，我们就在编码解决办法，因为真正的问题是我们的账户被黑了，而不是我们不能支付。通过增加检查和中止而不是支付，我们隐藏了这个事实。

## 有哪些例外

例外情况是在定义应用程序应该做什么时没有考虑到的情况。为消息队列
编写消息库时，您希望收到完整的消息，但为 TCP 编写一个
时，您希望收到部分消息。一种情况下的例外不一定是另一种情况下的例外。

如果你要打开一个文件，可能会发生几个错误。
不存在的目录、文件丢失、访问被拒绝、部分文件、
等。大多数程序员都知道这些错误，所以它们不是
的例外，对吗？

不对。在大多数情况下，他们是例外。因为你通常期望一个文件存在，并且它是完整的和可读的。好吧，如果
你正在编写一个数据取证应用程序，大多数错误都是
预料到的，并且应该被处理(因此不是例外情况)。

### 异常的存在是为了防止你的应用程序做一些愚蠢的事情。

理解这一点对你来说至关重要。不要认为异常是编码时可以用来控制应用程序的东西。将
异常视为一种机制，以防止您的应用程序做
一些错误/意想不到的事情。

### 异常的存在是为了帮助你将来解决问题

由于异常不是一种流控制机制，所以在执行代码时，它们并没有增加多少
值(前面描述的点除外)。

然而，编写信息性的异常消息使得
纠正未来的错误变得更加容易，因为它们为错误添加了上下文。永远试着这样做，你未来的自己会感谢你的。

## 代码示例

让我们采用与本文开头相同的代码，
但是改为使用异常处理。

```
public void SaveDefaultAccount(string userName, string accountName)
{
    int userId = GetUserIdFromName("Arne");
    int accountId = GetAccountFromName("Savings account");
    StoreDefaultAccount(userId, accountId);
} 
```

如您所见，该方法现在返回 void，因为它不需要
表明一切顺利。它也不需要验证来自被调用方法的
错误代码。如果不能保证预期的结果，可以有把握地假设
异常中止处理。

事实上，在大多数情况下，我们根本不必关心是否抛出了异常。记住，异常是用来告知发生了意想不到的事情。如果我们不能预测它，我们怎么能
处理这个异常呢？

我们来看一下`StoreDefaultAccount`方法。需要理解的最重要的事情
是，该方法说一个默认帐户应该被成功地
存储。因为这是方法承诺，所以我们必须在每次发现阻止默认的
账户被存储时抛出一个
异常。

```
public void StoreDefaultAccount(int userId, int accountId)
{
    if (userId <= 0)
        throw new ArgumentOutOfRangeException(nameof(userId), userId,
            "A valid user id must be specified.");
    if (accountId <= 0)
        throw new ArgumentOutOfRangeException(nameof(accountId), accountId,
            "A valid account id must be specified.");

    var account = _accountRepository.GetById(accountId);
    if (account.OwnerId != accountId)
        throw new InvalidOperationException($"User {userId} do not own account {accountId}.");

    var user = _userRepository.GetById(userId);
    user.DefaultAccount = accountId;
    _userRepository.Update(user);
} 
```

前两个异常用于减少错误，如解析错误
或无效数据。

第三个例外是业务规则。我们只能使用用户自己的账户作为默认账户。

如果给定的 accountId 在数据库中不存在，`_accountRepository.GetById(accountId);`将抛出一个
异常，因为
方法名称声明应该提取一个帐户。

# 总结

异常的目的不是让你根据事情是否失败采取不同的行动
。即它们不是流量
控制机制。相反，异常被用来确保您的
应用程序交付预期的结果(或者尝试)。

记住这一点，我希望你和我一样觉得它们很有用。有了
正确的思维模式(并使用异常)，你可以节省很多时间，因为
你不必追踪为什么你的数据库有很多数据
不一致(这会导致以后的错误)。

这篇文章是我们异常处理系列的一部分。要了解更多信息，请访问我们的[网站](https://coderr.io/exception-handling/)。*