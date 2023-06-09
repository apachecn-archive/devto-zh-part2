# 吃你的蔬菜

> 原文：<https://dev.to/everythingfunct/eat-your-vegetables-kad>

大约一年前，我在工作中使用 Fortran 启动了一个新项目。对于任何不知道的人来说，Fortran 程序员可用的工具和库少之又少，而且不是特别具有可扩展性或适应性。但是，作为测试和自动化的支持者，我去寻找一个测试框架。

我找到的唯一像样的开源选项叫做水果。它没有被很好地记录，但是它声称能够在编译时找到你的测试套件，所以你不必手动维护一个驱动程序。它使用 Rake 作为构建系统，Fortran 部分包含在单个源文件中。

它没有维护，只能从 SourceForge 获得，所以我想如果我要使用它，我最好接管它的维护。我抓起一份拷贝，放入 git，然后开始工作。我已经清理了所有的构建系统组件，甚至修复了一些 bug。但是我对测试框架本身做了很小的改动。框架本身可以在这里找到，如何使用它的示例 hello world 类型的示例可以在这里找到。

做完这一切，我对它非常不满意。它本质上是非常必要的，大量使用内部状态，并且它的输出还有很多不尽人意的地方。我已经做了一年的 fruit 维护者，已经看到了很多其他的测试框架在使用，并且被函数式编程的 bug 咬过，我想，我肯定能写出一个更好的测试框架。这能有多难。

所以，我现在向你们宣布，一个新的 Fortran 测试框架: [Vegetables](https://gitlab.com/everythingfunctional/vegetables) 。为了更健康的代码库，一定要吃蔬菜。我还有很多事情要做，但我相信在这一点上，基础是健全的，是经过验证的。我想解释一下我是如何把它组合在一起的。

我的前几次尝试(如果你真的好奇，可以在 git 历史中找到)都没有成功。事实证明，Fortran 无法正确处理递归数据结构。所以最后不得不链接 C++。这不是特别理想，但这是我能让它工作的唯一方法。

基本上，框架的所有繁重工作都发生在 C++端。但是所有的交互都发生在 Fortran 端，公开等价的类型和方法。Fortran 类型只是抓住一个 C++指针，并将其传递回调用 C++中适当方法的函数。这意味着，除非你真的开始研究框架是如何构建的，否则你真的无法判断。用户不必关心 C++方面的事情。

这样做有不利的一面。我基本上犯了一个内存泄漏。好消息是，在正常情况下，直到执行结束，内存才会被释放。基本上，定义测试的整个过程建立了一个包含所有测试的单一数据结构。然后，运行测试创建第二个相同形状的数据结构，包含所有结果。但是在程序结束之前，这两个函数都不会超出范围，所以在那之前，无论如何都不会调用它们的析构函数。你唯一能够分辨的时候是你是否正在做一些非常奇怪的事情，或者测试测试框架。

这就引出了我的下一个观点。框架已经过全面测试，使用自身作为测试框架。

[![Head Explode](img/aeec7efce06f6567778e3b581a4a22c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--78Wmb-Sk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://everythingfunctional.files.wordpress.com/2018/11/giphy-downsized-large.gif%3Fw%3D400)

好吧，也许没那么令人吃惊。我确信其他一些测试框架已经做到了这一点。但是尝试用这种方式进行 TDD 有点棘手。哦，是的，我用 TDD 来构建我的测试框架。是啊，这一个确实值得一个头脑爆炸。

[![Head Explode](img/aeec7efce06f6567778e3b581a4a22c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--78Wmb-Sk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://everythingfunctional.files.wordpress.com/2018/11/giphy-downsized-large.gif%3Fw%3D400)

在过程的早期阶段，测试并没有真正运行，它们只是编译。但是它们至少是存在的，并且试图编写它们是决定下一步需要编写哪个函数或类型的动力。在开始之前，我对整体设计有一个很好的想法，这也很有帮助。

基本的策略/设计很简单:一个测试或者是一个测试用例，或者是一组测试。因此，递归数据结构。然后运行测试用例产生测试结果，运行测试集合产生结果集合；形状相同的递归数据结构。

一个测试集合有一个描述和一个测试集合。一个测试用例有一个描述和一个返回断言结果的函数。断言结果可以放在一起，所以测试用例的结果甚至可以告诉我们有多少断言是作为测试用例的一部分产生的。

够抽象了，你怎么用这个东西。实际上，很简单。你可以这样定义你的测试。

```
function  test_passing_case_behaviors()  result(test)  use  Vegetables_m,  only:  TestCollection_t,  given,  then,  when  type(TestCollection_t)  ::  test  test  =  given("a passing test case",  &  [when("it is run",  &  [then("it knows it passed",  checkCasePasses),  &  then("it has 1 test case",  checkNumCases),  &  then("it has 1 passing case",  checkNumPassingCases),  &  then("it has no failing case",  checkNumFailingCases),  &  then("it's verbose description still includes the given description",   checkVerboseDescription),  &  then("it's failure description is empty",  checkFailureDescriptionEmpty),  &  then("it knows how many asserts there were",  checkNumAsserts),  &  then("it has no failing asserts",  checkNumFailingAsserts),  &  then("it knows how many asserts passed",  checkNumPassingAsserts)])])  end  function  test_passing_case_behaviors 
```

`given`是一个函数，它接受一个描述和一个测试集合数组，并返回一个测试集合。`when`是一个函数，它接受一个描述和一组测试用例，并返回一个测试集合。`then`是一个函数，它接受一个描述和一个函数，并返回一个测试用例。该函数必须不带参数，并从一个或多个`assert`函数返回结果。像这样:

```
function  checkCasePasses()  result(result_)  use  example_cases_m,  only:  examplePassingTestCase  use  Vegetables_m,  only:  &  Result_t,  &  TestCase_t,  &  TestCaseResult_t,  &  operator(.and.),  &  assertNot,  &  assertThat  type(Result_t)  ::  result_  type(TestCase_t)  ::  test_case  type(TestCaseResult_t)  ::  test_result  test_case  =  examplePassingTestCase()  test_result  =  test_case%run()  result_  =  assertThat(test_result%passed()).and.assertNot(test_result%failed())  end  function  checkCasePasses 
```

如果你不需要完整的 BDD 风格的 given-when-then，还有`describe`和`it`函数，它们分别接受一个描述和一组测试用例，以及描述和函数。在顶层，函数`testThat`接受一组测试集合并返回一个测试集合。单个子例程`runTests`接受一个测试集合作为参数，运行它，并报告结果。

在运行测试之前，测试规范被报告给用户，使用缩进来表示测试集合的嵌套。然后，一旦测试运行，任何失败都可以以完全相同的方式报告。

结果是一个可组合的、纯功能的测试框架，输出非常好，如下所示，略略。看看你是否能从结果中找出问题所在。

```
Running Tests

Test that
    A test collection
        can tell how many tests it has
        includes the given description
        includes the individual test descriptions

...

A total of 100 test cases

Failed

2 of 50 cases failed
2 of 59 assertions failed

Test that
    A test collection
        can tell how many tests it has
            Expected '2' but got '4'
    A test case
        Only has 1 test case
            Expected '1' but got '2' 
```

仍然有相当多的工作要做，但是我现在相信它将会工作并且是值得使用的。如果你很好奇，请点击这里查看，并随时提出你可能有的任何问题或建议。

我也有一个问题要问任何可能考虑使用它的人。支持一种报告所有通过的断言的方法值得吗？现在我只有显示所有测试用例的机制，以及任何失败断言的结果，但没有显示通过的断言。这并不难补充，我只是还没有决定这是否值得。你们觉得怎么样？

编辑:

实际上，我已经设法让这个框架只用 Fortran 就能工作了。数据结构和功能与上面描述的相同，但是不需要链接更多的 C++，对于不想使用和我一样的构建系统和自动化的人来说，这是一个简单的文件。