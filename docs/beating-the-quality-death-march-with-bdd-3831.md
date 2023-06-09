# 用 BDD 击败质量死亡行军

> 原文：<https://dev.to/jlhcoder/beating-the-quality-death-march-with-bdd-3831>

我还没有遇到一个软件开发人员或经理会不同意“我们应该构建高质量的软件”这句话。然而，确保质量似乎是一场艰苦的战斗。我曾经把这张 Despair.com 的海报放在我桌子上的一个相框里，但不幸的是，当你在亚马逊工作时，它在一次建筑搬迁中丢失了。😂

[![Despair.com: Quality](img/fcd915848a0cadffd3eef40792be6a7b.png)T2】](https://despair.com/products/quality)

谈到软件质量，我在职业生涯中已经走过了很长的路。在我的第一份工作中，我们甚至根本没有编写自动化测试*。每一个变化都是在你开发的时候手工测试的**颤栗** 。我想这是因为我已经看到了软件开发的这一面，所以我成为了一个如此巨大的质量倡导者。现在，我已经在亚马逊领导了多个团队来开发关键服务和 web UIs，其中每个提交都经过一个完整的连续部署管道，带有自动化单元和集成测试以及其他保障措施，以确保生产过程中的质量，所以我对这个主题有很多话要说。*

 *今天我想讨论的是我得到的一个常见的开发人员问题:你如何确保为你的软件编写集成测试？

此时，大多数团队/公司都在编写单元测试。在您的团队中强制执行一条规则是相对容易的，即所有的功能变更必须在同一个 commit/PR 中包含单元测试变更。虽然代码覆盖率不是一个完美的度量，但它是一个很好的可测量的指标，表明这个策略正在被执行。

但是，光是单元测试[就](https://twitter.com/thepracticaldev/status/845638950517706752) [只是](https://twitter.com/thepracticaldev/status/892788721350836225) [还不够](https://twitter.com/thepracticaldev/status/852508104914874369)。不过，确保编写集成测试可能很棘手。关键的问题是，你通常不能测试一个服务或网站，除非你已经构建了足够的服务或网站来处理端到端的请求。如果你没有仔细考虑如何构建产品特性的交付，你可能会将所有的集成测试保存到项目的最后，因为最后期限的原因，集成测试通常会被搁置。

我想分享一个解决这个问题的方法，我已经在多个团队中尝试过，并且发现这个方法对解决这个问题非常有效:行为驱动开发(BDD)。

## 什么是 BDD？

BDD 是一种源自测试驱动开发(TDD)的开发方法，它专注于在开发解决方案之前，根据具体的场景*定义特性需求，然后将这些场景转化为可执行的测试，以验证解决方案是否满足需求。*

我给你举个例子。假设您正在编写一个简单的银行服务，允许您开户、存款和取款。您可能会收到该服务的需求列表，例如，

1.  用户可以开立和关闭有初始余额的支票或储蓄账户。
2.  只要不透支，用户可以在一个公开的账户上存钱或取钱。
3.  储蓄账户的最低余额为 100 美元。
4.  ...

这些高层次的需求是一个起点，但是当您使用 BDD 时，下一步是将这个项目分解成多个特性，然后使用简单的语言为每个特性编写详细的场景，这些语言可以被开发人员和关键的利益相关者阅读和理解。例如，上述要求将在以下特征文件中描述:

```
Feature: Basic Account operations
  As a customer
  I want to be able to open checking and savings accounts, and credit and debit my accounts
  So I can buy stuff online^H^H^H^H^H save my money

Scenario: Customer creates a checking account
  When the customer opens a checking account with a $0 balance
  Then a new checking account should be created
  And the checking account should have a balance of $0

Scenario: Customer creates a savings account with the minimum balance
  When the customer opens a savings account with a $100 balance
  Then a new savings account should be created
  And the savings account should have a balance of $100

Scenario: Customer creates a savings account with less than the minimum balance
  When the customer opens a savings account with a $50 balance
  Then the call should fail

Scenario: Customer credits their account
  Given the customer has a checking account with a $0 balance
  When the customer credits their checking account $10
  Then the checking account should have a balance of $10

Scenario: Customer debits their account
  Given the customer has a checking account with a $50 balance
  When the customer debits their checking account $10
  Then the checking account should have a balance of $40
... 
```

这些场景的格式非常简单。唯一真实的结构是，以单词“给定”开始的步骤是前提条件或假设，以“何时”开始的步骤是采取的行动，以“然后”开始的步骤是对所采取行动的副作用的断言。“And”可用于将多个步骤链接在一起。

检查并批准特征文件后，就可以构建特征了。之后，像 [Cucumber](https://cucumber.io/) 这样的工具允许您将特性文件中的每一步映射到代码中的一个函数，并将特性文件作为可执行测试运行。在您的 CI/CD 管道中加入一个步骤来执行特性文件，现在您就拥有了针对您的特性的自动化验收测试，每次您对代码库进行更改时都会运行这些测试！

## BDD 为什么管用

就像我说的，我已经在多个团队中使用了 BDD，它非常有效。以下是我喜欢它的地方:

首先，编写特性文件增加了协作，确保开发人员、管理人员和关键的利益相关者都在同一页上，关于系统在构建之前的预期行为应该是什么。我上面给出的特征文件例子看起来写起来很简单，但是当你试图写一个的时候，你会惊讶地发现有这么多的问题。开发人员非常善于思考各种各样的危险场景，并且不得不把它们写出来，将需求澄清问题反馈给管理层/利益相关者。这消除了由于需求误解而浪费的大量时间和精力，这些误解在过程的后期是非常昂贵的。我发现在管理层经历了几次这样的循环之后，他们喜欢上了它，因为他们有信心他们的团队正在构建正确的东西，并且它会工作(并且继续工作！)一旦建成。

其次，它为开发人员提供了一种系统化的方法来确保自动化集成测试确实被编写出来。对于单元测试，您设置了一个规则，即应该为每个提交/PR 编写测试。对于集成测试，您设置了一个规则，即应该为每个特性编写测试。将您的项目分成小的、增量的特性，现在您可以确保集成测试是随着软件的开发而增量编写的。

随着管理人员和开发人员都看到了好处，您不会陷入不重视集成测试的情况，因为优先讨论发生在*特性级别*并且编写集成测试是渐进的，而不是在整个项目的最后，当您感到启动压力时，这是一个巨大的、不可逾越的任务。

## BDD 陷阱

希望在这一点上，我已经把 BDD 的好处讲得很清楚了。也许你有兴趣在自己的团队中尝试一下。太好了！我强烈推荐。然而，我也在几个团队的实践中使用了这种方法，并希望涵盖一些在走这条路时要注意的常见陷阱。

### 陷阱 1:功能太大

由于集成测试是为每个特性编写的，为了增量地开发集成测试，您必须将项目分解成多个特性。这种实践还有许多其他的好处，所以不管你是否使用 BDD，我都推荐它，但是特别是对于 BDD，如果你的“特性”是整个产品或项目，那么你就回到了相同的船上，直到最后他们可能由于时间压力而被削减。

因此，如果你去写特性文件，感觉这是一个令人生畏的、永无止境的任务，你的特性可能太大了，需要进一步分解。如果你正在练习 Scrum，把一个特性想象成应该适合 sprint 的东西。如果你没有练习 Scrum，我的团队的冲刺是 2 周，所以你可以用这段时间作为指导。😊

### 陷阱 2:使用 BDD 进行单元测试

我看的第一本关于 BDD 的书是 [BDD 在行动](https://www.amazon.com/BDD-Action-Behavior-driven-development-lifecycle/dp/161729165X/ref=sr_1_1?ie=UTF8&qid=1539442121&sr=8-1&keywords=bdd+in+action)。它很好地解释了我上面详细描述的好处。然而，它也提倡不仅在我上面描述的验收测试级别使用 BDD，而且在单元测试级别也使用 BDD。那本书有很多我喜欢的地方，但是我认为在单元测试层面使用 BDD 完全是浪费时间。

当你想到这一点时，你用 BDD 所做的是维护一个人类可读的描述你的软件应该如何工作和代码之间的映射，这些代码实际上确保它以这种方式工作。维护这种映射是有开销的，但是只要除了开发人员之外的其他人阅读可读的描述，这是值得的*。为什么一个管理者/涉众会浪费他们的时间去阅读单元测试级别的特性文件呢？他们不应该这样做，因为他们应该更关心系统行为是否符合需求，而不太关心底层的实现细节。我也从未见过开发人员在单元测试级别阅读特性文件，因为他们通常更容易阅读测试代码本身。所以维护这些映射毫无益处。就是不做。*

### 陷阱 3:让非开发人员编写特性文件

许多 BDD 书籍提倡这个理想化的世界，在那里产品经理或利益相关者直接编写特征文件。我认为这个想法是，它将迫使他们在把详细的需求交给开发人员之前，仔细考虑这些需求。我从未见过这在实践中有效。虽然特性文件语言对于开发人员来说可能看起来非常开放和富于表现力，但对于产品经理来说，它仍然是一种非常受约束的格式，他们倾向于在像 Word 这样的文档编辑器中编写实际的散文。

此外，正如我将在下面介绍的，由于场景步骤必须映射到可执行代码，所以编写特性文件变得有点像一门艺术，不仅要指定需求，还要以底层步骤实现可维护的方式来编写。当开发人员对他们的特性文件进行代码审查时，产品经理会很快变得沮丧，因为他们对特性场景步骤的实现有各种各样的评论，而对实际场景内容的评论却很少。

最后，我认为让开发人员将需求文档翻译成特性文件确实有助于他们内化特性的需求。它还使他们能够就产品团队可能没有考虑到的不同边缘案例场景提出许多问题。如果一个开发人员刚刚得到一个功能文件，他们可能不会像自己编写它那样仔细检查或仔细思考。

### 陷阱 4:场景包含太多底层细节

当开发人员编写特性文件时，他们还会考虑底层的 step 实现。Cucumber 等工具为参数化步骤提供了丰富的特性，甚至允许将数据表传递到步骤实现函数中。作为一名开发人员，通过将更多的细节放入显示在您的特征文件中的参数中来尽量减少 step 实现函数中的重复是非常诱人的。

我建议尽量少用 Cucumber *的 step parameters 特性*并且*永远不要*使用更高级的特性，比如数据表。您应该为非开发人员的特性文件的可读性进行优化，这通常意味着将关于场景的较低级的、多余的细节推到步骤实现中。帮你自己(和你的管理层/股东)一个忙，不要写这样的场景:

```
# Don't do this!
Scenario: Customer creates a checking account
  When the customer opens an account with the following data
  | First Name | Last Name | Type     | InitialBalance |
  | This       | Sucks     | CHECKING | 0.00           |
  Then the new account should have the following data
  | First Name | Last Name | Type     | Balance |
  | This       | Sucks     | CHECKING | 0.00    | 
```

希望所有这些额外的细节都降低了场景的可读性。虽然精确，但当你的产品经理和利益相关者试图从不必要的细节中分析出意义时，他们的目光会变得呆滞。

### 陷阱 5:步骤实现可能变成意大利面条代码

step 实现的良好模式可能值得单独发表一篇博文，但我会尽量将其浓缩到本质上。BDD 工具允许场景中的每一步都映射到一个函数调用。然而，对于一个给定的场景，这些步骤需要在前一个步骤中收集的数据是很常见的。这意味着您的函数必须在它们之间共享数据。好的，也许你可以创建一个类来保存你的步骤定义函数，并使用实例变量在函数间共享数据。然而，您通常希望将您的步骤定义分组到单独的逻辑组中，这样您就不会有一个充满步骤定义的庞大类，并且您最终将不可避免地编写使用跨越多个类的步骤的场景。因此，基本上，您需要一些在步骤实现函数之间共享的全局数据。如果您没有一种有组织的方法来管理这些全局数据，而是在实现步骤时随意设置，那么您最终将得到一个非常难以维护的混乱局面。

我发现一个行之有效的解决方案是用一个单独的类来管理全局数据的写入。它可以从任何步骤实现函数中读取，但是它只写在一个特定的地方。例如，当使用 BDD 测试服务 API 时，我创建了一个代理类来包装对该服务的调用。代理类保存步骤实现函数可能需要的任何相关信息。这里有一个 Java 中的例子:

```
public class BankingServiceProxy {
    private final BankingService service;

    // ...

    public void openAccount(OpenAccountRequest request) {
        BankAccountTestData.clearLastException();
        BankAccountTestData.setOpenAccountRequest(request);
        try {
            OpenAccountResponse response = service.openAccount(request);
            BankAccountTestData.setAccountId(response.getAccountId());
            BankAccountTestData.setOpenAccountResponse(response);
        } catch (Exception e) {
            BankAccountTestData.setLastException(e);
            // suppress exceptions. Step implementations will check last exception to assert on call failures
        }
    }

    // ...
} 
```

然后您的`When the customer opens a checking account with a $0 balance`的步骤实现看起来像这样(注释特定于 Cucumber-JVM):

```
public class AccountSteps {
    private final BankingServiceProxy proxy;

    // ...

    @When("the customer opens a (.*) account with a \\$(.*) balance$")
    public void the_customer_opens_an_account(String accountType, int initialBalance) {
        OpenAccountRequest request = new OpenAccountRequest()
            .withAccountType(accountType)
            .withInitialBalance(initialBalance);
        proxy.openAccount(request);
    }
} 
```

请注意，step 实现将保存全局数据的任务留给了代理类。然后后面的步骤可以对保存的数据进行验证:

```
 @Then("a new (.*) account should be created")
    public void a_new_account_should_be_created(String accountType) {
        assertThat(BankAccountTestData.getLastException()).isNull();
        assertThat(BankAccountTestData.getOpenAccountResponse()).isNotNull();
        assertThat(BankAccountTestData.getOpenAccountResponse().getStatus()).isEqualTo(BankAccount.Status.OPEN);
        assertThat(BankAccountTestData.getOpenAccountResponse().getType()).isEqualTo(AccountType.valueOf(accountType.toUpperCase()));
        // ...
    }

    @Then("the call should fail")
    public void the_call_should_fail() {
        assertThat(BankAccountTestData.getLastException()).isNotNull();
    } 
```

### 陷阱 6:测试缓慢

随着您的特性文件套件的增长，测试运行时间会增加很多。测试经常会进行类似的安装/拆卸操作，这可能需要时间。老实说，我还没有找到解决这个问题的灵丹妙药，因为在这一点上，BDD 的好处仍然远远超过长期运行验收测试的问题，以至于我们还没有真正找到解决这个问题的好办法。

然而，这里有一些提示:

1.  您可以将多个 When/Then 组链接到一个场景中，以节省多余的测试设置步骤。不要做得太过火，以至于你的特征文件变成了一个冗长的场景。我倾向于支持单独的场景，但有时将它们结合起来也是有意义的。使用判断来确保您没有损害非开发人员对特性文件的可读性。
2.  使您的步骤实现全局数据类线程安全，并为每个场景使用唯一的测试数据，以便您可以并行运行您的场景。我还没有找到一个 Cucumber 的并行测试运行程序，可以毫不费力地插入到我们当前的构建设置中，所以我没有好的推荐。如果别人有，请评论！一个快速而不实用的解决方案是设置您的自动化 CI/CD 管道，将每个特性文件作为单独的测试运行来运行，以便它们可以并行运行。

## 结论

正确使用时，BDD 是一种非常有效的方法，可以增加团队协作，消除开发过程中的浪费，并确保集成测试与产品的每个特性一起逐步构建。希望这篇文章能激发你尝试的兴趣！*