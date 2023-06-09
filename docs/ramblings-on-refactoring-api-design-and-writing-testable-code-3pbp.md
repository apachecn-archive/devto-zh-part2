# 漫谈重构、API 设计和编写可测试代码。

> 原文：<https://dev.to/svemaraju/ramblings-on-refactoring-api-design-and-writing-testable-code-3pbp>

有很多关于设计好的 API 的资源。我个人认为约书亚·布洛赫的演讲信息量非常大，马丁·福勒的博客、演讲和书籍也非常出色。Erich Gamma 等人的设计模式也是一个很有见地的资源。我提到这一切，因为我试图提取他们所有的智慧，并将其与我的经验相结合。我的工作主要是构建 web 服务，我将在这篇博客的上下文中使用 API 作为 HTTP API 的占位符，但是它也可以适用于任何种类的 API。

我将借用 Jasmin Blanchette 的[API 设计小手册](https://issuu.com/madman1969/docs/the_little_manual_of_api_design)中关于如何设计 API 的一些要点。

* * *

### 要易学易记。

我们每个人都经历过或者将要经历一个好的/不好的命名约定如何影响使用某个 API 的便利性。保持一致是实现简单设计的最简单的方法之一。减少冗余是另一个原因。

我曾经为获取两种不同的酒店构建了类似下面的端点。

```
api/XHotels/
api/XHotel/

api/search/Y/?method=list
api/search/Y/?method=detail 
```

Enter fullscreen mode Exit fullscreen mode

这两个网站本质上是从两个不同的提供商那里获取酒店，它们拥有如此任意的 URL 的原因是它们在建造时没有意识到它们将如何被使用。在这两种情况下，用户不应该为了接收一个列表而去记忆两种不同的 URL 模式。

### 适应变化。设计对实现细节的过度依赖使得添加/删除逻辑变得困难。

软件所有的事情都是先从了解需求开始的。在某些情况下，需求是直截了当的，但是更多的时候，需要提出问题来精确地得出“需要做什么”。“是什么”比简单的最终结果更微妙，“是什么”还包括“如何”,如如何简单/如何容易/如何快速/如何安全等。
实施应根据预期用途进行调整，而不是相反。直观的 api 设计可以通过首先关注用例，然后转移到实现来实现。

只关注实现可能会导致设计迫使用户过于依赖 api 的实现。

例如:编写一个函数，它根据被传递的参数的数量有不同的行为。我自己和其他人在编写 api 接口时都做过这样的设计选择。让接口过多地受功能实现的支配会使 api 变得脆弱。假设我们有两种客户，高级客户和基本客户。现在观察下面这段代码。

```
def create_organisation_profile(**kwargs):
    if 'registration' in kwargs:
        model = PremiumUser
    else:
        model = BasicUser
    ... 
```

Enter fullscreen mode Exit fullscreen mode

使逻辑依赖于输入数据是否包含特定元素，使得代码不太适合将来在我们的应用程序中添加/删除客户类型。将一个额外的字段传递给 function/api 来区分执行哪个逻辑要高效得多。在这种情况下，决定布尔 v/s 枚举变得很重要，这取决于我们的用例是否可以有两个以上的变体，即准确预测是否只能有两个同类。编写上述逻辑的一种更持久的方式是

```
def create_organisation_profile(**kwargs):
    if kwargs['type'] == PREMIUM:
        model = PremiumUser
    elif kwargs['type'] == BASIC:
        model = BasicUser
    ... 
```

Enter fullscreen mode Exit fullscreen mode

可以添加更多的 elif 条件来处理任何未来的客户类型。

### 尽可能分离出与软件不同层交互的代码。例如:数据库查询、第三方 API 调用、推送通知等。

我发现将源代码分成不同的逻辑层对应用程序的开发和维护非常有帮助。换句话说，遵循 MVC/MVP/MTV 模式。上述模式通过开始为模型、控制器和视图创建单独的文件来实现。但是在开发过程中被忽略或忘记的是，故意使用这种分离来决定代码应该写在哪里。换句话说，问这个问题——“这个逻辑应该停留在哪里？”-每次你写一段代码。另一件人们经常忽略的事情是，在创建这三个文件后停止。通过对编写的逻辑(或创建的文件)进行进一步的分类，可以获得更好、更清晰的项目结构。

```
def register_user(request):
    . . .
    user = User.objects.create(username=username,
        email=email,
        password=password,
        first_name=first_name,
        last_name=last_name)

    email_text = email_text.replace('#FIRST_NAME',user.first_name)

    notification = Noitification.objects.create(user=user,
       notification_type=SIGN_UP)
    notification.send(text=text) 
```

Enter fullscreen mode Exit fullscreen mode

正如您从上面所看到的，将所有的操作逻辑放在一个函数中是一个糟糕的主意，即使是为了防止 bug。从开发新手转型的关键时刻之一是不要陷入“这有什么问题，它工作得很好”的思维陷阱。一个更有经验的开发人员会用稍微不同的方式来表达——“它现在工作得很好，但是可能会出错”
代码分离的另一个用途是它使代码变得干燥，也就是说，如果我们有一个方法/函数有我们的认证逻辑，我们可以重用它，而不用到处重写代码。

### 在任何适用的地方使用设计模式..使代码更加可读和可调试。

设计模式是解决问题的有用工具。它们帮助我们从可重用性、隔离性、可重构性等方面进行思考。一般来说，我通常是在一个已经设计好的框架上编写我的逻辑，这个框架包含了所有正确的模式。但是偶尔我会遇到一个问题，这迫使我定制所提供的开箱即用的解决方案。这就是设计模式发挥作用的时候了。在需要/适用的地方明智地使用它们可以极大地提高代码的可读性和可维护性。

> 应用程序代码只编写一次，但是在应用程序的整个生命周期中，不同的开发人员会反复阅读。可读代码更容易记录和维护。它也不太可能包含错误，因为代码的可读性使错误更加明显。(API 设计小手册)

我想每个人都会在某个时候遇到可读性问题。但是我想强调一下设计模式是如何使代码不仅更健壮，而且可读性和可维护性更强的。

`if`或`switch`语句在实现业务规则时是不可避免的。*如果*你的业务规则太复杂，你可能会得到这样的结果——

```
...

if payment.customer in discounted_customers:
    # custom flow for premium customers

if payment.get_pending_amount() > 0:
    # flow for handling pending payments
else:
    # usual flow 

... 
```

Enter fullscreen mode Exit fullscreen mode

使用稍微定制的策略模式可以帮助避免上述代码可能出现的许多问题。

```
# strategy.py
def get_customer_strategy(payment):
    if payment.customer.type == PREMIUM:
        return process_premium_customer
    return process_free_customer

def get_payment_strategy(payment):
    if payment.get_pending_amount() > 0:
        return remind_pending_amounts
    elif payment.get_pending_amount() == 0:
        return process_order 
```

Enter fullscreen mode Exit fullscreen mode

像这样分割条件将自动提高可读性，仅仅是函数名的存在，函数名本身是描述性的，足够小，可以消化逻辑。

### 在概括和专门化 API 的每个方面的范围之间找到正确的平衡——当有疑问时，把它去掉

Joshua Bloch 在他关于 API 设计的演讲中谈到了这一点。建议不要过度概括 API 用例。Bloch 在他的演讲中使用库作为 API 的例子，但是这也可以应用于 Web 服务。让一个端点对每个请求执行太多的操作会导致大量的调试问题。

```
def post(request):
    ...
    create_event(**data)
    create_attendee_types(**types)
    create_attendees(attendee_list)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

一个很好的例子是从同一个 API 调用中创建父记录和子记录。这将导致因依赖过多而导致的失败，并会扰乱应用程序的状态。

### 编写可测试的代码。

最后，测试也是设计 API 的一个重要部分。编写良好的测试套件可以在开发过程的早期发现设计漏洞和错误，等等。

*   在每次修复错误、重构或添加功能后帮助进行回归测试。
*   将需求具体化——代码期望做什么？
*   让你思考你是否真的解决了问题。
*   迫使你做出更好的设计决策。
*   给你一个衡量质量和进步的标准。
*   及早捕捉虫子。
*   有助于知识转移。
*   使您的代码可读性强，易于导航。

> 编写测试没有秘密..只有编写可测试代码的秘密。-谷歌的米斯科·赫维里

编写可测试的代码是一门艺术。但幸运的是，编写可测试的代码与设计好的 APIs 软件是重叠的。