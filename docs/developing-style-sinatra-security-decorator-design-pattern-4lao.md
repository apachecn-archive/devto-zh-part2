# 开发风格:Sinatra 安全，装饰设计模式

> 原文：<https://dev.to/micahshute/developing-style-sinatra-security-decorator-design-pattern-4lao>

我打算记录我在编程教育的早期阶段学到的所有东西，原因有三:

1.  如果没有别的，巩固我学到的东西
2.  从更有经验的人那里获得负面反馈
3.  通过我写的东西和我收到的反馈帮助其他初学者。

对我来说最重要的部分是第二项——我坚信负面反馈是智力发展的一个因素，如果不是最重要的因素的话。我从来没有过编程导师，我也看不到在不久的将来有这样的可能性，所以我想尽可能地弥补这种反馈元素的缺失。所以我鼓励任何有经验的开发者阅读这篇文章，如果你在我写的东西中看到不准确或低效的东西，请随时向我扔一些知识炸弹。特别是在这篇文章中，我乐于接受关于安全最佳实践的反馈，因为这似乎是一个经常被忽视的领域，也是一个我需要学习的领域。

也就是说，我今天写的是从制作我的第一个 web 应用程序中学到的经验，我是用 Sinatra 框架做的。我将谈到我在实现适当的应用程序安全性方面学到的东西，然后谈到我在项目中使用的装饰设计模式。

## 安全

我真的很高兴了解并尝试实现 OOP 设计模式，但这不是我在制作应用程序时遇到的第一个深入研究的领域。相反，这是建立适当的安全机制的障碍。虽然这可能没有我想要的优雅，

[![Lightsaber](../Images/14bb42f243bac73c7d4ce8ee8ad66748.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2yh2LZTA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7wqhs6w.png)

它是一个非常实用的基本工具，需要像对待任何新算法或高效设计模式一样认真对待。

[![Blaster](../Images/8cff80217cf6a2fdc60fd85d908a3ef6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UiDLpMoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xeDiQuR.jpg)

即使在简单的 web 应用程序中，正确的身份验证和授权也不是小事。如果不认真对待，您可能会暴露您的用户信息、应用程序信息，甚至危及您的服务器。[2]此外，你没有发展技能和肌肉记忆，而这些可能是你以后获得更敏感的应用所需要的。

那么，一个简单的 Sinatra App 需要哪些步骤呢？嗯，我们必须考虑如何对用户进行授权和认证。我们在登录时对照数据库检查他们的用户名和密码。然后我们用 cookies 持久化一个会话。对于我们的用例来说，这是两个主要的脆弱领域。幸运的是，使用 ActiveRecord 抽象通过使用准备好的语句(使用参数化查询) [OWASP 的 SQL 注入备忘单](https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet)在登录时防范 [SQL 注入](https://en.wikipedia.org/wiki/SQL_injection)。如果我们花时间正确使用它，Sinatra 还为我们提供了(几乎)所有保护 cookies 安全所需的工具。

[![Cookies](../Images/0bca949ba0ce46df318164455ea95a30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bGavk42U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Kx0Lxfv.png)

您可能知道也可能不知道，cookies 是从服务器发送给用户并存储在他们浏览器上的散列(只是字典)。这使得从用户到服务器的后续 HTTP 请求可以与该 cookie 一起发送，并且服务器可以知道关于该用户的信息——例如，他们以前已经过身份验证(即，它用于持久化状态)。

一个问题是，被用户或第三方修改的 cookies 可能会导致意想不到的、潜在的“坏”事情发生，尤其是如果这些修改是恶意的。为了帮助阻止以特定的方式“智能地”转换 cookie 数据以损害应用程序的操作，我们可以加密我们的 cookie。这仍然有可能允许更改数据，但是用户可能不知道他们是如何更改数据的。Sinatra 做得更好，默认情况下加密我们的 cookies 并签名，这加密了数据，并可以告诉服务器数据是否被篡改。这是通过“session_secret”完成的。

那么，如果辛纳特拉做到了这一点，我们为什么还要关心做其他事情呢？

(注:你也可以让你的 cookies 被盗，然后被第三方使用，甚至通过你自己的浏览器假装他们是你——这是我在制作网站后学到的，我将在下面讨论它。Sinatra 通过使 cookies 仅 HTTP)提供了一些保护措施

Rack (Sinatra 中间件)通过 [OpenSSL](https://en.wikipedia.org/wiki/OpenSSL) 使用 [HMAC-SHA1](https://en.wikipedia.org/wiki/HMAC) 对 cookies 进行签名，使用 session_secret 和会话中的编码数据。虽然 HMAC-SHA1 散列法是一个单向过程(这意味着攻击者不能逆转它)，但它也是一个非常快速的过程——这意味着攻击者可以非常快速地反复运行它，对你的密码进行不同的猜测。他们可以通过相同的 HMAC-SHA1 算法运行他们的猜测和 cookie 数据，并查看他们的输出哈希是否等于您的 cookie 哈希，来获取您的 session_secret。如果是的话，他们的猜测是正确的，现在他们可以欺骗服务器。这被称为字典攻击。而且他们不用自己编写这个过程的代码；他们可以[下载软件](https://github.com/hashcat/hashcat)快速有效地做到这一点。他们还可以获得预先汇编的常用密码和秘密短语词典。我看到一个例子，有人在字典攻击的 45 秒内用“超级秘密”的 session_secret 破解了一个 Sinatra 的 app[2]。这就是 HMAC-SHA1 速度快的危险——我们使用的字符串越短，我们被黑客攻击的速度就越快。Sinatra 的 GitHub 页面推荐至少 32 字节的 session_secret。每个字符一个字节

```
"a".bytesize # => 1 
```

Enter fullscreen mode Exit fullscreen mode

我们使用“秘密”或“超级秘密”(分别为 6 个字节和 13 个字节)大大低于该建议。

同样，不言而喻的是，如果我们公开我们的 session_secret(即在提交过程中不小心把它放到 GitHub 上)，甚至不需要猜测就可以破解您的 cookies。

那么，我做了什么来对抗它呢？

*   [X]获得一个随机的 64 位十六进制数，它不会出现在字典中。通过 sysrandom gem 生成它，该 gem 使用本机操作系统的方法创建十六进制，这样更安全。复制下面代码的输出，获得一个随机的 64 字节十六进制字符串。

```
ruby -e "require 'sysrandom/securerandom'; puts SecureRandom.hex(64)" 
```

Enter fullscreen mode Exit fullscreen mode

应该注意的是，这是一个 64 位的十六进制值，所以每个字符(即使作为一个字符串，它是 1 个字节)代表每个字符 4 位(或半个字节)。(注意，2^4 = 16，这是一个十六进制字符中可能出现的符号数)。所以作为字符串的输出实际上是 128 字节(即有 128 个十六进制符号)。

*   [X]将我的 session_secret 64 字节十六进制字符串保存为环境变量，如[12 因子应用](https://12factor.net/config)和我的 API 密钥所建议的那样，这样它们就不会出现在我不希望它们出现的地方，我可以很容易地更改它们，并且它们是语言和操作系统无关的。通过以下方式为您的应用程序检索它:

```
set :session_secret, ENV.fetch('SESSION_SECRET') 
```

Enter fullscreen mode Exit fullscreen mode

(点击阅读更多关于[环境变量的信息)](http://blog.honeybadger.io/ruby-guide-environment-variables/)

在制作这个应用的几天后，我读到了关于跨站请求伪造的文章，( [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) )。我了解到，人们可以使用网站上的帖子，这些帖子没有正确地过滤用户输入，以使用户在用户不知情的情况下使用户的浏览器访问该网站(通常通过使用带有 src 的 img 标签访问该网站)。我对 Javascript、AJAX 等知之甚少，但这促使我了解更多(我将很快分享)。无论如何，我决定去我的网站做一些乱七八糟的事情，我发现我可以通过张贴评论将脚本插入到网站中。这使得攻击者有很多选择，从窃取其他用户的 cookies，对他们进行 CSRF 攻击，或者覆盖那个网页。我决定伪装成一个试图在网站用户中散布不满情绪的人(我通过在浏览器中检查网站获得所有的查询信息，而不是通过制作网站的过程):

[![script in post](../Images/24410ee912581e8bdff2e2dfcdb7d414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bEFHtXGk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XQty8t7.png) 

[![post output](../Images/a8136cf01e606c9214279d167843292a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZN1l1u1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/trgsg9s.png)

为了解决这个问题，我需要净化我的输入。起初我打算自己过滤，但最好的方法可能是使用一种既定的方法。所以我最终使用了 sanitize gem，现在我的黑客尝试看起来像这样:

[![sanitized](../Images/4d61b64cacdca39d3e60a26647a091a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fxhf8Wqe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/002rLv4.png)

令我失望的是，我开始制作一个 web 应用程序，却忘记了这个非常基本的概念。这应该是任何设计 web 应用程序的人必备的知识。

* * *

## 我的第一个设计模式——装饰者

**我的应用:**

*   让用户通过从 API 获取大量信息来跟踪实体并对其做出反应
*   允许用户发布帖子并对帖子做出反应，以及对应用程序中的实体做出反应。

**并发症:**

*   我的应用程序将显示来自 API 调用的大量数据——所有这些数据都可能快速变化。所以，我不想把它保存在我的数据库中，因为:
    *   必须频繁地从 API 中提取信息，并频繁地在数据库中覆盖这些信息——这对有用的信息来说是太多的冗余工作。
    *   对于我的免费云数据库的大小来说，这是太多的信息。
*   但是，我需要这些实体的数据库对象来跟踪用户决定关注什么，与他们相关的所有帖子，喜欢什么和不喜欢什么，等等。

**要解决的问题:**

*   我想在一个类中为每种类型的对象访问我的数据库关系和所有 API 数据。例如，我希望我的实体类具有所有的 ActiveRecord::Base 功能，并通过方法调用获得所有的 API 数据。但是我想在不保存额外信息的情况下这样做。

这个问题可以用一些简单的方法解决。但是设计模式并不总是用来将问题从不可解转化为可解——很多时候，一旦问题被实例化，它们会使问题的解决方案变得优雅、灵活，并且对我们开发人员来说容易。

**我的回答**装修工。

> 装饰者:动态地给一个对象附加额外的责任。装饰者定义了一个更高级的接口，使得子系统更容易使用。[1]

对我来说，决定使用什么样的设计模式和实现它们一样困难，如果不是更困难的话——这里有一个它们如何相互关联的图形表示，帮助我们决定相对于其他模式使用哪一个:

[![Design Pattern Relationships](../Images/8f7459330c2e8e65f6bef0807d5e0499.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Iy63fBxF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vQXBIko.png)

对我第一次来说，这是很难接受的。这就是我如何做出决定的。

1.  设计模式[1]提供了一种选择模式的方法。这种方法包括您可能试图解决的一般问题的列表，以及相应的可能的设计模式解决方案。阅读这个列表时，我遇到了通过子类化来扩展功能的*。我想扩展功能，但我不想子类化。检查。*
2.  我编译了列出的可能的设计模式解决方案:

    *   桥
    *   责任链
    *   复合材料
    *   装饰者
    *   观察者
    *   战略
3.  我分析了每一个的意图，如[1]所述:

    > ## 桥
    > 
    > 将抽象与其实现解耦，这样两者可以独立变化

这不太对。API 和数据库模型都不是对方的抽象。

> ## 责任链
> 
> 通过给多个对象一个处理请求的机会，避免将请求的发送方耦合到接收方。链接接收对象，并沿着链传递请求，直到有对象处理它。

不适用。

> ## 复合
> 
> 将对象组成树结构来表示部分-整体层次结构。Composite 允许客户端统一处理单个对象和对象的组合。

我没有研究等级结构。这个不适合我的特殊情况。

> ## 观察者
> 
> 定义对象之间的一对多依赖关系，这样当一个对象改变状态时，它的所有依赖对象都会得到通知并自动更新

不，因为我没有试图确保连接的对象之间的统一操作。

> ## 策略
> 
> 定义一系列算法，封装每一个算法，并使它们可以互换。策略让算法独立于使用它的客户端而变化

我想我可以使用策略设计模式来解决这个问题。我不确定哪一个最好，最后我决定选择室内设计师。

> ## 装饰工
> 
> 动态地将附加责任附加到对象上。Decorators 为扩展功能提供了子类化的灵活替代方案。

听起来差不多。

然而，我想在这里补充一点，我在这里使用的装饰器(尽管它对我来说工作得很好)可能不是最完美的。当你特别想要将*动态*职责分配给许多*个体对象*，而不是整个类时，装饰器作为一个概念被充分利用。这防止了这种功能的增加影响其他对象。也就是说，我确实有一个重构的计划，以更好地使用设计模式，并且通过使用它，现在重构更容易了。

因此，首先让我们做一个简单的例子来解释一个“完美”的场景:想象我正在买冰淇淋。室内设计师如何帮助我们？

建立我们的宇宙:

```
class PriceError < StandardError
    def initialize(msg="That is not a valid price")
        super
    end
end

class IceCream

    def initialize(price: 1.0)
        @price = price
    end

    def price=(amt)
        raise PriceError if !amt.is_a?(Float)  && !amt.is_a?(Integer)
        @price = amt
    end

    def price
        return @price
    end

    def ingredients
        "Ice Cream"
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们希望能够选择我们想要的冰淇淋，并相应地改变成分和价格。为此，我们可以创建装饰类。

```
class WithJimmies
    def initialize(item)
        @item = item
    end

    def price
        @item.price + 0.5
    end

    def ingredients
        @item.ingredients + ", Jimmies"
    end
end

class WithChocolateSyrup
    def initialize(item)
        @item = item
    end

    def price
        @item.price + 0.2
    end

    def ingredients
        @item.ingredients + ", Chocolate Syrup"
    end
end

class WithOreos
    def initialize(item)
        @item = item
    end

    def price
        @item.price + 1.0
    end

    def ingredients
        @item.ingredients + ", Oreos"
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以做我们的冰淇淋了！

```
my_snack = IceCream.new
my_snack = WithJimmies.new(my_snack)
my_snack = WithOreos.new(my_snack)
my_snack.ingredients # => "Ice Cream, Jimmies, Oreos"
my_snack.price # => 2.5

my_friends_snack = IceCream.new
my_friends_snack = WithChocolateSyrup.new(my_friends_snack)
my_friends_snack.ingredients # => "Ice Cream, Chocolate Syrup"
my_friends_snack.price # => 1.2 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们想要的功能。我从一美元一节的冰淇淋课开始。这就是我想让大家做的。我可以只吃一个冰淇淋就结账，而不用说明我不想要其他的东西。我可以手动更改或“装饰”我的冰淇淋，而不需要原始类做任何事情，只需要它遵守具有价格和成分属性的接口。我的朋友可以用同样的类做同样的事情，但是选择不同的选项，得到他自己个性化的结果。

我甚至可以在混合中添加其他类，它们可以由相同的装饰者来装饰，只要它们遵守要求它们有价格和成分的*‘接口’*。除此之外，他们可以完全不同！

```
class FroYo 
    def initialize(price: 1.5)
        @price = price
    end

    def price
        return @price
    end

    def price_to_s 
        return "$#{'%.2f' % @price}"
    end

    def ingredients
        "Froyo"
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在只需将上一个示例中的第一行替换为 my_snack = FroYo.new，除了用 FroYo 代替冰激凌之外，就可以有相同的输出！

“那么，为什么不能将这些功能作为方法编程到一个超类中，然后再将这些功能子类化呢？”你可以的。但是在 OOP 设计中实现设计模式的一个很大的目标是**支持对象组合而不是类继承**为什么？

*   对象组合也称为黑盒重用。在这里，我们使用一堆不同类型的对象，通过它们在运行时相互关联的方式来完成更大的任务。这是一种更加模块化和灵活的做事方式。
*   继承可以说是“打破封装”——由于不必要的重叠，对父类的更改可以(意外地)改变子类的行为方式。
*   重用组合模式比继承模式更容易。
*   [1]声明:“理想情况下，您不应该为了实现重用而创建新的组件。您应该能够通过对象组合来组装现有组件，从而获得所需的所有功能。”然而，它也承认这并不总是可行的。
*   尽管如此，继承很容易使用，并且受到语言的支持，所以有时使用它是有好处的。

虽然这两种方法都可以解决问题，但是对象组合有助于防止大型的、笨拙的超类，允许更容易地动态分配责任，并且帮助我们遵守[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)

现在我们对装饰器有了一个直觉，我认为看到一个正式的定义是很重要的，以类图的形式

[![Decorator Pattern UML](../Images/d08bcdd1c2c6aca6a31ddf14b276ea4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AfTFPIxq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/XADKZbN.gif)

那么，我如何使用一个装饰者呢？

我想要一个轻量级的数据库，所以我只想在数据库中保存一个实体的字符串标识符，这足以从 API 中获取所有信息。此外，我不想在数据库中保存每个实体标识符，只保存用户感兴趣并与之交互的标识符。因此，我构建了一个装饰器，它将一个类作为初始化参数，并将 API 的惟一标识符作为属性。Decorator 的 initialize 方法中需要的这个类可能是以下两种情况之一:“占位符”类(我创建的)或 ActiveRecord 实例(两者都需要有 entity_identifier 属性)。使用占位符或 ActiveRecord 类的能力是我选择使用 Decorator 而不是策略的主要原因，因为它允许我只将用户实际交互的实体保存到数据库中(即有一个允许我使用 ActiveRecord 类而不是“一次性”类的接口)。我让装饰器能够通过镜像 ActiveRecord 方法来检索它的关系(如果它是一个 ActiveRecord::Base 实例)。例如，我仍然希望`entity.user_likes`返回 ActiveRecord 用户实例。我希望能够创建新的数据库关系，并像调用 ActiveRecord 对象一样调用 decorator 类上的`.save`。我还希望任何用户交互都能够将实体保存到数据库中(如果它还没有保存),同时在装饰器中用实际的 ActiveRecord 对象替换占位符对象，这样我就可以保存和检索所有关系。所以我在装饰器中包含了一些方法来完成这个任务。一些例子:

```
def entity_identifier
    self.entity.entity_identifier
end

def save
    saved = self.entity.save 
    # note: the EntityPlaceholder #save method returns an ActiveRecord object
    self.entity = saved if self.entity.is_a?(EntityPlaceholder)
    !!saved
end

def following_users
    self.save if self.entity.is_a?(EntityPlaceholder)
    self.entity.following_users
end 
```

Enter fullscreen mode Exit fullscreen mode

我还让装饰者能够通过 APIManager 类从 API 中检索信息。装饰器将在实例化时用额外数据填充自己。

当我在应用程序的不同部分重新呈现实体时，这种模式将允许我扩展应用程序的功能，我希望在应用程序的不同部分显示不同的属性，从不同的 API 端点获取信息，并以不同的方式与用户交互。我想要更改/添加的任何新功能都可以通过不同的装饰器在 ActiveRecord 对象或 Placeholder 对象上完成。

虽然对于一个装饰者来说，这可能不是最好的用例，或者我可能实现得很草率，或者可能有一些更好的设计模式，但是使用这种模式确实使我的代码更容易使用，让我练习搜索和选择设计模式，并让我对装饰者设计模式有了更好的理解。下次我有问题时，我将能够有一个新的方法来潜在地解决它，并且我将能够改进我的想法的具体实现。

[![larger world](../Images/ecdbd477fef16edf22fbedd60d15d6ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NNF6xX-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OOZKmer.png)

## 参考文献

[1]《可重用面向对象软件的设计模式元素》，伽马，赫尔姆，约翰逊，Vlissides，1995

【2】[一行危及你的服务器的代码](https://martinfowler.com/articles/session-secret.html)

【3】[Sinatra GitHub 自述文件](https://github.com/sinatra/sinatra#session-secret-security)

【4】[SysRandom GitHub 自述文件](https://github.com/cryptosphere/sysrandom)

【5】[一个行家的环境变量指南](http://blog.honeybadger.io/ruby-guide-environment-variables/)