# 测试 101:测试简介

> 原文：<https://dev.to/alysivji/testing-101-introduction-to-testing-5hdk>

*这篇[帖子](https://alysivji.github.io/testing-101-introduction-to-testing.html)最初发表于 [Siv 脚本](https://alysivji.github.io/)T5*

编程就是写代码解决问题。软件工程是使用一个**结构化过程**来解决问题的实践。作为工程师，我们希望有一个我们可以根据需要更改、扩展和重构的代码库。测试确保我们的程序按预期工作，并且对代码库的更改不会破坏现有的功能。

在我的上一份工作中，我与一名高级工程师合作构建了一个基于微服务的后端，以取代我们现有的 Django monolith。这是一个全新的项目，我们被鼓励去尝试新事物。我正在阅读 [Python Testing with pytest](https://pragprog.com/book/bopytest/python-testing-with-pytest) 并说服高级工程师让我将 [pytest](http://pytest.org/) 带入我们的项目。这是偶然的，因为它迫使我带头编写最初的测试集，我们用它作为我们所有服务的模板。

这一经历强化了《务实的程序员》中强调的原则。这是关于在**我们测试什么**、**我们如何测试**，以及**我们何时测试**的务实性；我们应该利用工具和技术，尽可能高效地测试我们的代码。测试需要简单，没有障碍；一旦测试感觉像是一件苦差事，程序员就不会去做了...这就是软件质量下滑的原因。

我们害怕深入代码，因为要么没有测试，要么现有的测试太脆弱，我们不得不在编写代码时重写测试。这不是软件工程的内容。测试应该支持重构，而不是妨碍我们修改代码库的能力。我们应该把时间花在编写业务逻辑上，而不是花在测试上。

测试是民间传说，在某种意义上说，最佳实践和技术是作为团队的一部分在项目中从一个程序员传给另一个程序员的。如果你是这个行业的新手，并且正在尝试测试，很难知道如何开始。感觉有很多相互矛盾的建议，那是因为确实有。测试是固执己见的，比任何其他软件工程学科都更是如此。人们总是在争论测试什么，如何测试，以及**尤其是**何时测试。

这是一系列帖子中的第一篇，详细介绍了我如何向代码库添加测试的思考过程。在这篇文章中，我提供了一个关于测试世界的广泛介绍，这样我们就可以在以后的文章中有一个共同的词汇。

* * *

#### 目录

*   [什么是测试](#what-is-testing)
*   [测试的好处](#benefits-of-testing)
*   [黑盒 vs 白盒](#black-box-vs-white-box)
*   [测试金字塔](#test-pyramid)
*   [结构化测试](#structuring-tests)
*   [考什么](#what-to-test)
*   [何时编写测试](#when-to-write-tests)
*   [结论](#conclusion)

* * *

## 什么是检测

当我们编写代码时，我们需要运行它来确保它做我们期望它做的事情。测试是我们代码的契约:给定一个值，我们期望返回某个结果。

运行测试可以被认为是一种反馈机制，它通知我们程序是否如预期的那样工作:

[![Tests provide a feedback mechanism](../Images/bfe650bb8ed993ce63c97913e733990d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JmBYhrro--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.io/images/30-39/feedback_loop.jpg)

虽然通过测试[不能证明缺失错误](http://wiki.c2.com/?TestsCantProveTheAbsenceOfBugs)，但是它们确实告诉我们，我们的代码正在以测试定义的方式工作。相反，失败的测试表明有些事情不对劲。我们需要理解为什么我们的测试失败了，这样我们就可以根据需要修改代码和/或测试。

### 测试的属性

#### 1。快的

测试给了我们信心，让我们相信我们的代码正在按预期工作。一个较慢的反馈循环会阻碍发展，因为我们需要更长的时间来发现我们的改变是否正确。如果我们的工作流程被缓慢的测试所困扰，我们就不会经常运行它们。这将导致接下来的问题。

#### 2。确定性的

测试应该是[确定性的](https://en.wikipedia.org/wiki/Deterministic_system)，即相同的输入将总是产生相同的输出。如果测试是非确定性的，我们必须找到一种方法来解释测试中的随机行为。

虽然生产中肯定存在非确定性代码(即机器学习和 AI)，但我们应该尽可能让我们所有的非概率代码都是确定性的。除非我们的程序需要，否则做额外的工作是没有意义的。

#### 3。自动化的

我们可以通过运行它来确认我们的程序是否有效。这可能是手动运行 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 中的命令或刷新网页；在这两种情况下，我们都在观察我们的程序是否做了它应该做的事情。虽然手动测试对于小项目来说很好，但是随着我们项目复杂性的增加，它变得难以管理。

通过自动化我们的测试套件，我们可以快速验证我们的程序按需工作。一些开发人员甚至让他们的测试在文件保存时触发运行。

### 正式定义

让我们复习一些定义，这样我们就有了一个共同的词汇。

[![Diagram of a system under test](../Images/642d56e4437459a7e4eb188b8b533517.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZlUzw5Ng--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.io/images/30-39/system_under_test.jpg)

被测系统(SUT) 是当前正在被测试的实体。这可能是一行代码、一个方法或整个程序。

**验收标准**是指我们执行的检查，允许我们接受系统的输出。验收标准的具体性和范围取决于我们测试的内容:医疗设备和航空航天要求测试要具体，因为出错的空间要小得多。

如果亚马逊做了一个糟糕的推荐，也不是世界末日。如果 IBM 的沃森建议错误的手术，可能会有生命危险。

**测试**是指将输入输入到我们的测试系统中，并根据我们的验收标准验证输出的过程:

*   如果输出正常，我们的测试就通过了。
*   如果输出不正常，我们的测试就会失败，我们必须进行调试。

希望测试失败能为我们提供足够的上下文信息，让我们知道去哪里找。

* * *

## 测试的好处

经过深思熟虑的测试策略与全面的测试用例相结合，可以带来以下好处:

#### 放心修改代码

如果一个程序做任何感兴趣的事情，它在函数、类和模块之间有交互。这意味着一行代码的改变会以意想不到的方式破坏我们的程序。测试给了我们对代码的信心。通过在修改代码后运行我们的测试，我们可以确认我们的更改没有破坏测试定义的现有功能。

相比之下，在没有测试的情况下修改代码库是一个挑战。没有办法知道事情是否按预期进行。我们凭直觉编程，这是一个相当冒险的提议。

#### 及早识别 bug

虫子要花钱。多少取决于你什么时候找到他们。

在[软件开发生命周期](https://en.wikipedia.org/wiki/Systems_development_life_cycle) (SDLC)中，修复 bug 的代价越大。[一个软件错误的真实成本](http://blog.celerity.com/the-true-cost-of-a-software-bug)深入探讨这个问题。

#### 完善系统设计

这一点有点争议，但我认为在编写代码时考虑测试可以改进系统设计。一个彻底的测试套件表明开发者实际上已经对问题进行了深入的思考。编写测试[迫使你使用自己的 API](https://en.wikipedia.org/wiki/Eating_your_own_dog_food)；这有望带来更好的界面。

所有项目都有时间限制，很容易养成走捷径的习惯，这增加了模块之间的耦合，导致复杂的相互依赖。我们必须认识到用意大利面条代码解决问题。

知道我们必须测试我们的代码迫使我们写模块化的代码。如果有些东西很难测试，我们可以实现一个更好的接口。花时间写测试迫使我们正念；在从用户的角度看问题之前，我们先深呼吸一下。

一旦你通过使用像[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)这样的模式编写了可测试的代码，你将会看到添加结构是如何使验证我们的代码做我们期望它做的事情变得更加容易。

* * *

## 黑盒 vs 白盒

测试可以大致分为两大类:黑盒测试和白盒测试。

**黑盒测试**是指测试人员无法看到被测项目内部工作情况的测试技术。

[![Picture showing input and output going into a blackbox](../Images/ee8a718226707027bbb4970886502b54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Drv1lJM4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.io/images/30-39/black_box_testing.jpg)

**白盒测试**是一种测试人员可以看到被测项目内部工作情况的技术。

[![Picture showing input and output going into a transparent box where we can see how information flows](../Images/8bba2cf0e7a1d70dd2ee6ee99e6a3879.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k_SXAu0H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.io/images/30-39/white_box_testing.jpg)

作为开发人员，我们执行白盒测试。我们在盒子里面写了代码，并且知道如何彻底地测试它。这并不是说不需要黑盒测试，我们仍然应该有人在更高的层次上执行测试；接近代码会导致我们测试中的盲点。

* * *

## 测试金字塔

自动化测试金字塔为如何构建我们的测试策略提供了指导。它说我们应该编写大量快速廉价的单元测试和少量缓慢昂贵的端到端测试。

[![Picture showing automated testing pyramid](../Images/550b59098db8dc42edc961b1836bbc44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tjTcgZaD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.io/images/30-39/test_pyramid.png)

测试金字塔不是一个硬性的规则，但是它提供了一个开始考虑测试策略的好地方。一个很好的经验法则是，根据你对系统的信心的需要，在每个级别编写尽可能多的测试。我们应该在编写代码的同时编写测试，朝着适合我们正在进行的项目的测试策略迭代。

### 单元测试

单元测试是低级别的测试，集中于测试我们系统的特定部分。它们写起来很便宜，运行起来很快。测试失败应该提供足够的上下文信息来查明错误的来源。这些测试通常由开发人员在[软件开发生命周期](https://en.wikipedia.org/wiki/Systems_development_life_cycle) (SDLC)的实现阶段编写。

单元测试应该是独立和隔离的；与外部组件的交互增加了我们测试的范围和测试运行的时间。正如我们将在以后的文章中看到的，用 [test doubles](https://martinfowler.com/bliki/TestDouble.html) 替换依赖关系会导致确定性测试快速运行。

我们的单元测试应该有多大？像编程中的其他事情一样，它取决于我们试图做什么。从[行为单元](https://www.agileconnection.com/article/3-keys-mastering-test-driven-development)的角度考虑，允许我们围绕逻辑代码块编写测试。

测试金字塔建议在我们的测试套件中进行大量的单元测试。这些测试给了我们信心，让我们相信我们的程序会像预期的那样工作。编写新代码或修改现有代码可能需要我们重写一些测试。这是标准的实践，我们的测试套件随着我们的代码库而增长。

试着认识到我们的测试套件越来越复杂。记住，**测试我们生产代码的代码也是生产代码**。花时间重构你的测试，以确保它们是有效的。

#### 单元测试示例

假设我们有下面的函数，它获取单词列表并返回最常见的单词以及该单词出现的次数:

```
def find_top_word(words)
    # Return most common word & occurrences
    word_counter = Counter(words)
    return word_counter.most_common(1)[0] 
```

我们可以通过创建一个列表来测试这个函数，在这个列表上运行`find_top_word`函数，并将函数的结果与我们期望的值进行比较:

```
def test_find_top_word():
    words = ["foo", "bar", "bat", "baz", "foo", "baz", "foo"]
    result = find_top_word(words)

    assert result[0] == "foo"
    assert result[1] == 3 
```

如果我们曾经想要改变`find_top_words`的实现，我们可以毫无畏惧地去做。我们的测试确保了在不导致测试失败的情况下`find_top_word`的功能不会改变。

### 集成测试

每个复杂的应用程序都有内部和外部组件协同工作来完成一些有趣的事情。与关注单个组件的单元测试相反，集成测试将系统的各个部分结合起来，作为一个组一起测试。集成测试也可以指在我们的应用程序的服务边界上的测试，即当它到达数据库、文件系统或外部 API 时。

这些测试通常由开发人员编写，但也不是必须的。根据定义，集成测试比单元测试范围更大，运行时间更长。这意味着测试失败需要一些调查:我们知道测试中的一个组件不工作，但是需要找到失败的确切位置。这与单元测试形成对比，单元测试的范围更小，并且准确地指出了失败的地方。

我们应该尝试在类似生产的环境中运行集成测试；这最大限度地降低了由于配置差异而导致测试失败的可能性。

#### 集成测试示例

假设我们有下面的函数，它接受一个 URL 和一个元组`(word, occurrences)`。我们的函数创建了一条记录并保存到数据库:

```
def save_to_db(url, top_word):
    record = TopWord()
    record.url = url
    record.word = top_word[0]
    record.num_occurrences = top_word[1]

    db.session.add(record)
    db.session.commit()

    return record 
```

我们通过传入已知信息来测试这个函数；该功能应该保存我们输入到数据库中的信息。我们的测试代码从数据库中提取新保存的记录，并确认它的字段与我们传入的输入相匹配。

```
def test_save_to_db():
    url = "http://test_url.com"
    most_common_word_details = ("Python", 42)

    word = save_to_db(url, most_common_word_details)

    inserted_record = TopWord.query.get(word.id)
    assert inserted_record.url == "http://test_url.com"
    assert inserted_record.word == "Python"
    assert inserted_record.num_occurrences == 42 
```

请注意，这是我们手动进行的一种测试，用来确认事情是否如预期的那样工作。自动化这个测试使我们不必在每次修改代码时重复检查这个功能。

### 端到端

端到端测试检查系统是否满足我们定义的业务需求。一个常见的测试是以与用户体验相同的方式跟踪系统中的路径。例如，我们可以测试一个新的用户工作流:模拟创建一个帐户，“点击”激活电子邮件中的链接，首次登录，并与我们的 web 应用程序的教程模式弹出窗口进行交互。

我们可以利用浏览器自动化工具，如 [Selenium](https://selenium-python.readthedocs.io/) ，通过我们的用户界面(UI)进行端到端测试。这在我们的 UI 和测试之间产生了依赖，这使得我们的测试变得脆弱:对前端的改变要求我们改变测试。这是不可持续的，因为要么我们的前端将变得静态，要么我们的测试将无法运行。

一个更好的解决方案是测试皮下层，也就是我们用户界面下面的那一层。对于一个 web 应用程序，这将是测试 REST API，包括发送 JSON 和获取 JSON。

我们的[皮下测试](https://martinfowler.com/bliki/SubcutaneousTest.html)是我们和我们前端的合同；它们可以被我们的前端开发人员用作 REST API 的规范。像 [swagger-meqa](https://github.com/meqaio/swagger_meqa) 这样建立在 OpenAPI 规范之上的工具可以帮助我们自动化这个过程。我们也可以使用像[波兹曼](https://www.getpostman.com/)这样的全功能工具来测试、调试和验证我们的 API。

端到端测试被认为是黑盒，因为我们不需要知道任何关于实现的东西来进行测试。这也意味着测试失败并不能说明哪里出了问题；我们需要使用日志来帮助我们跟踪错误和诊断系统故障。

#### 端到端测试示例

这里我们使用 [Flask 测试客户端](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.test_client)在我们的 REST API 上运行皮下测试。幕后有很多事情发生，我们得到的结果(HTTP 状态代码)让我们知道测试是通过还是失败。

```
def test_end_to_end():
    client = app.test_client()

    body = {"url": "https://www.python.org"}
    response = client.post("/top-word", json=body)

    assert response.status_code == HTTPStatus.OK 
```

#### 资源

*   马丁·福勒维基:[test pyramid](https://martinfowler.com/bliki/TestPyramid.html)|[UnitTest](https://martinfowler.com/bliki/UnitTest.html)|[integration test](https://martinfowler.com/bliki/ComponentTest.html)|[EndToEndTest](https://martinfowler.com/bliki/BroadStackTest.html)
*   谷歌测试博客:[拒绝更多的端到端测试](https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html)
*   自动化熊猫:[测试金字塔](https://automationpanda.com/2018/08/01/the-testing-pyramid/)

* * *

## 结构化测试

每个测试用例可以分为以下几个阶段:

*   将被测系统(SUT)设置到测试用例所需的环境中(先决条件)
*   执行我们想在 SUT 身上测试的动作
*   验证预期结果是否出现(后置条件)
*   拆掉 SUT，把环境恢复到我们发现时的状态

有两种广泛使用的结构化测试框架:Arrange-Act-Assert 和 Given-When-Then。

### 安排-行动-断言(AAA)

AAA 模式是分离测试不同部分的抽象:

*   **安排**所有必要的先决条件
*   在 SUT 上表演
*   断言我们的后置条件被满足

#### 排列-动作-断言示例

```
def test_find_top_word():
    # Arrange
    words = ["foo", "bar", "bat", "baz", "foo", "baz", "foo"]

    # Act
    result = find_top_word(words)

    # Assert
    assert result[0] == "foo"
    assert result[1] == 3 
```

阶段之间的清晰分离允许我们看到我们的测试方法是否试图一次测试太多不同的东西。Arrange-Act-Assert 是我在写测试时使用的模式。

### 给定——当——然后(GWT)

GWT 为分离测试的不同阶段提供了一个有用的抽象:

*   **给定**一组先决条件
*   **当**我们在 SUT 上执行一个动作时
*   **那么**我们的后置条件应该如下

GWT 广泛应用于[行为驱动开发](https://en.wikipedia.org/wiki/Behavior-driven_development) (BDD)。

#### 给定-当-然后例子

```
def test_find_top_word():
    # Given a list of word
    words = ["foo", "bar", "bat", "baz", "foo", "baz", "foo"]

    # When we run the function over the list
    result = find_top_word(words)

    # Then we should see `foo` occurring 3 times
    assert result[0] == "foo"
    assert result[1] == 3 
```

#### 资源

*   杰拉德·梅萨罗什:[四阶段测试](http://xunitpatterns.com/Four%20Phase%20Test.html)
*   马丁·福勒维基: [GivenWhenThen](https://martinfowler.com/bliki/GivenWhenThen.html)
*   C2 维基:[安排行为断言](http://wiki.c2.com/?ArrangeActAssert)
*   James Cooke: [为 Python 开发者安排 Act Assert 模式](https://jamescooke.info/arrange-act-assert-pattern-for-python-developers.html)
*   自动化熊猫:[行为驱动的 Python](https://www.youtube.com/watch?v=EtIAbfCrsFI)

* * *

## 考什么

为了证明我们的程序是正确的，我们必须对输入值的每一种可能的组合进行测试。这种类型的穷举测试是不实际的，所以我们需要采用测试策略，允许我们选择最有可能出错的测试用例。

经验丰富的开发人员可以平衡编写代码来解决业务问题和编写测试来确保正确性和防止[回归](https://en.wikipedia.org/wiki/Regression_testing)。找到这种平衡并知道测试什么感觉更像是一门艺术而不是一门科学。幸运的是，我们可以遵循一些经验法则来确保我们的测试是彻底的。

#### 功能需求

我们想确保所有相关要求都已得到落实。我们的测试用例应该足够详细，以检查业务需求。如果某样东西不符合你设定的标准，那么建造它就毫无意义。

#### 基础路径测试

我们必须对每条语句至少测试一次。如果语句有一个条件(`if`或`while`)，我们必须改变我们的测试，以确保我们测试了条件的所有分支。例如，如果我们有下面的代码:

```
if x > 18:
    # statement1 elif 18 >= x >= 35:
    # statement2 else:
    # statement3 
```

为了确保我们命中上述条件的所有分支，我们需要编写以下测试:

1.  `x < 18`
2.  `18 <= x <= 35`
3.  `x > 35`

#### 等价划分

产生相同输出的两个测试用例被认为是等价的。为了涵盖这类错误，我们只需要其中一个测试用例。

#### 边界分析

计算机科学中有两个难题:缓存失效、事物命名和 1 位错误

这是编程中最古老的[笑话之一，但是在它的背后有很多真理，我们经常混淆我们是需要一个`<`还是一个`<=`。这就是为什么我们应该总是测试边界条件。举下面的例子:](https://martinfowler.com/bliki/TwoHardThings.html) 

```
if x > 18:
    # statement1 else:
    # statement2 
```

为了确保我们彻底测试了上面代码片段的边界条件，我们将有`x=17`、`x=18`和`x=19`的测试用例。请注意，如果我们的边界有复合条件，编写测试用例会变得更加复杂。

这是测试边界条件的一个很好的[指南。](https://medium.freecodecamp.org/a-beginners-guide-to-testing-implement-these-quick-checks-to-test-your-code-d50027ad5eed)

#### 类坏数据

这是指以下任何一种情况:

*   数据太少(或没有数据)
*   数据太多
*   无效数据
*   数据大小错误
*   未初始化的数据

#### 数据流测试

侧重于跟踪程序的控制流，重点是探索与数据对象状态相关的事件序列。例如，如果我们试图访问一个已经被删除的变量，我们会得到一个错误。我们可以使用数据流测试来为其他测试没有测试过的变量提出额外的测试用例。

#### 错误猜测

过去的经验提供了对可能导致错误的部分代码库的洞察。记录以前的错误可以提高你将来不再犯同样错误的可能性。

#### 重述

当我说 [*开发人员测试的艺术*](https://alysivji.github.io/tag/art-of-developer-testing.html) 时，我指的是弄清楚要测试什么并高效地完成它。提高测试水平的唯一方法是编写测试，提出更好的测试策略，并学习不同的测试技术。就像在软件开发中一样，你对某件事了解得越多，你就会做得越好。

* * *

## 什么时候写测试

虽然有很多关于何时编写测试的有趣讨论，但我觉得这偏离了测试的重点。当你编写测试时**并不重要，重要的是**你编写测试。****

如果你有兴趣探索这个话题，我推荐以下链接:

*   RubyOnRails 的创造者大卫·海涅梅尔·汉森(DHH) [在 RailsConf 2014](https://www.youtube.com/watch?v=9LfmrkyP81M) 上批评 TDD
*   [TDD 死了吗？与 DHH、马丁·福勒和肯特·贝克的讨论](https://martinfowler.com/articles/is-tdd-dead/)
*   Ted M. Young 的学术调查

* * *

## 结论

在这篇文章中，我们对测试世界有了一个广泛的介绍。既然我们都在同一页上，我们可以在以后的文章中更深入地探索测试。

#### 附加资源

*   代码完成第 22 章:开发者测试
*   代码简单性:[测试的哲学](https://www.codesimplicity.com/post/the-philosophy-of-testing/)
*   凯蒂·哈夫: [Python 测试和持续集成](https://katyhuff.github.io/python-testing/)
*   [测试和编码播客](https://testandcode.com/)