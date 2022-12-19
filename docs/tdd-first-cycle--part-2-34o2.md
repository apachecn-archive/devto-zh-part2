# TDD 第一周期(第二部分)

> 原文：<https://dev.to/apium_hub/tdd-first-cycle--part-2-34o2>

让我们继续我们的 TDD 系列文章，在第一部分中，我们看了 TDD 和单元测试背后的理论。在第二部分， *TDD 第一周期*，我们开始开发我们的应用程序，一个笔记应用程序，用户可以写笔记和我们想到的一切。如果您有兴趣了解我们如何在应用程序中开发任何特定功能，请留下评论。让我们从我们的 TDD 第一周期流程开始吧！

## *TDD 第一周期*

要开始开发我们的应用程序，我们可以从用户实体开始(非常通用，它用于一切)，我们稍后会看到是否有必要将其更改为更具体的东西。

我们必须时刻牢记 TDD 循环。第一步是红色的，所以我们寻找一个失败的测试，一旦我们把它变成绿色，我们就有了应用程序的功能(部分或全部)。

在这个过程中，我们必须为我们的实体“发明”一个可能的接口。它将会不断发展，因为新的或更好的东西将会出现，很可能我们将不得不修改测试和生产代码。我们不应该认为一旦编写了一个测试并将其置为绿色，它就不能被修改或删除…让我们从一个测试开始，该测试验证已经创建了一个带有姓名的用户，例如:

```
 describe("given user", () => {
  describe("with valid data", () => {
    test("is created", (done: any) => {
      const validUser = new User("someId", "Oscar", "Galindo");
      expect(validUser.toString()).toEqual("User(someId,Oscar,Galindo)");
      done();
    });
  });
}); 
```

> 在第一个测试中，我们用 toString (*)检查创建的用户是否是我们期望的用户。随着实体的增长，要进行检查实体所有属性的测试会有短期成本，我们可以问自己，测试实体的所有属性是否有意义。([单元测试的艺术](https://www.manning.com/books/the-art-of-unit-testing))。
> 
> (toString 还没有经过测试(显式)，因为它是我们用来进行调试/测试的方法。

好的，我们已经有了一个红色的测试，因为 *TDD 第一周期*指示:红色。让我们回到 *TDD 第一周期*，我们必须以最简单的方式将测试置于绿色，这听起来可能很荒谬，但这是必须要做的。TDD 帮助你专注于一件事，但是当然，这取决于程序员，因此我们必须记住存在的过程和帮助我们的过程。

第一个错误出现在编译时。它不知道“用户”是什么，当我们创建它时，我们应该将输入参数赋给类的属性:

```
 export class User {
  constructor(private id: string, private name: string, private lastname: string) {}
} 
```

> 在你可能在互联网上找到的例子中，他们通常在第一次迭代中实现尽可能多的硬编码，扭曲响应以将测试置于绿色…嗯，这不是 100%那样。
> 
> 大多数想要学习 TDD 的开发人员认为这是浪费时间，当我们知道实现(将测试置于绿色)并且简单时，硬编码答案似乎是荒谬的，它也在 TDD 中被接受。有几种方法可以最快地将测试置于绿色，尽管在本章中我将只提到其中的两种:

*   伪造答案:伪造答案，以便尽快将测试置于绿色。
*   显而易见的实现:如果我们知道实现，那么它很容易开发，并且不将测试置于绿色环境的风险很小，继续进行吧。
    *   如果我们不把测试标为绿色，就回去伪造它。这里最重要的是关注测试要求我们做什么，仅此而已。

很好，我们已经是绿色的了，现在是重构的部分。这一部分很特别，我有类似“tdd 帮助你做出有用的狗屎代码”的想法，很难理解(这是 TDD 学习的一部分)TDD 并不意味着做测试。正如我们已经提到的，TDD 是一个帮助你设计、建模你的代码的过程，在安全的情况下采取尽可能少的步骤。它确认了我们脑子里的东西是正确的，给了我们一个持续的设计空间。如果你跳过这最后一步，那么说明你不是在做 TDD，你是在做 TF(先测试)。TDD 公式(不精确且有争议):TF + Refactor = TDD

说了这么多，第一次测试可以做什么样的重构呢？在这种情况下，我们将不做任何事情，我们没有任何重复，我们也不想抽象任何东西。让我给你看一篇非常好的文章，在那里你可以找到一个在循环的每一步都要考虑的清单: [TDD 清单](http://www.giorgiosironi.com/2010/03/tdd-checklist-red-green-refactor-in.html)和一本很棒的书[重构](https://martinfowler.com/books/refactoring.html)。

让我们继续，我们已经完成了 *TDD 第一周期*。基于第一个测试，我想知道用户是否可以使用空名称，我也想知道它是否可以有数字或超过一百万个字符，但我专注于一个，其他的都写在我的待办事项列表中，以便一步一步地检查它们(我通常在应用程序中有一个 ToDo.md，我在那里写下我看到的一切)，显然它们是我们必须做的验证，我们将会做。

首先，像往常一样，我们创建一个测试来验证名称不能为空，很简单，对吗？

```
 describe("with empty name", () => {
    test("should throw", (done: any) => {
      const userThrowWithEmptyNameCreator = () => new User("someId", "", "Galindo");
      expect(userThrowWithEmptyNameCreator).toThrow();
      done();
    });
}); 
```

我们有一个测试在一个空名字之前失败了，这一次它告诉我们它应该给出一个异常，但是它没有这样做，一些正常的事情没有实现。我们将以最简单的方式开发这一功能:

```
 export class User {
  constructor(private name: string, private lastname: string) {
    if (name === "") {
      throw new Error("empty name");
    }
  }
} 
```

我们回归绿色！我们有了进步，我们有了新的功能，我们有了一个实现了某些验证规则的用户实体，好的方面是它给了我们一些自由来安全地进行某些重构，因为我们对经过验证的代码进行了测试，如果我们破坏了某些东西，它会通知我们。

一旦我们把它变成绿色，我们必须重构我们所拥有的，但是在产品代码的层面上，我们不会碰任何东西。测试代码呢？这也是我们的准则，这些准则也很重要，也必须得到维护。产品代码难以维护/阅读可能会引起担忧，但如果在测试中发现这种类型的代码，情况会更糟。如果您在测试中发现这种类型的代码，会发生两件事:

*   你不再相信你的测试
*   你没有注意它们，被跳过的测试开始出现，被评论或者直接被删除，直到最终没有测试，我们已经知道接下来会发生什么。

在这个小测试中，我们可以重构什么？在这两个测试中，我们创建了一个用户，我们可以将这个用户创建到一个方法中，并保持如下状态:

```
 describe("given user", () => {
  const createUser = (name: string, lastname: string) => new User("someId", name, lastname);
  const createValidUser = () => createUser("Oscar", "Galindo");
  const createUserWithEmptyName = () => createUser("", "Galindo");

  describe("with valid data", () => {
    test("is created", (done: any) => {
      const validUser = new User("someId", "Oscar", "Galindo");
      expect(validUser.toString()).toEqual("User(someId,Oscar,Galindo)");
      done();
    });
  });

  describe("with empty name", () => {
    test("should throw", (done: any) => {
      expect(createUserWithEmptyName).toThrow();
      done();
    });
  });
}); 
```

> 创建这些对象有许多策略:
> 
> *   我们使用的方法是，在实体创建测试中创建一个方法，但是如果实体增长，我们可能会有问题(尽管我认为我们可以调查这是否是一个设计问题
> *   [对象母亲](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.18.4710&rep=rep1&type=pdf)
> *   [测试数据生成器](http://www.natpryce.com/articles/000714.html)

一旦重构，我们可以看到测试保持绿色。我们获得了大量的可读性，并且我们为其他开发人员的维护任务提供了便利(最重要的是，我们帮助其他人理解应用程序是如何工作的，因为测试成为了项目文档的一部分)

现在让我们对姓氏做完全相同的操作。我们从测试开始，它实际上与 name 的测试相同，我们必须继续前面的重构，我们已经有了一个创建用户的工厂，为什么不使用它呢？

```
 const createUserWithEmptyLastName = () => createUser("Oscar", "");
describe("with empty lastname", () => {
    test("should throw", (done: any) => {
      expect(createUserWithEmptyLastName).toThrow();
      done();
    });
}); 
```

红色的测试，很好，这里开发人员开始失去一点注意力，我重复 TDD 的 3 个法则之一是不要写超过足够数量的代码来通过测试，那么，让我们来看看这些代码？

```
 export class User {
  constructor(private name: string, private lastname: string) {
    if (name === "") {
      throw new Error("empty name");
    }
    if (lastname === "") {
      throw new Error("empty lastname");
    }
  }
} 
```

我们有绿色的测试，我们有新的功能！酷，现在有趣的事情来了，重构！这次在生产代码 [![🙂](img/cfea5b58aa48fab10278694e22eb01ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UFibHl0O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/2.2.1/72x72/1f642.png) 中，为了避免重复，这里有许多抽象技术(我们将使用 ValueObjects)。但是我们将在我的 TDD 系列的第三篇文章中讨论它。即将推出，保持更新，不要错过！

如果你想了解更多关于 *TDD 第一周期*的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇关于 TDD 第一周期的文章很有趣，你可能会喜欢…

[软件开发中的 TDD 实例(上)](https://dev.to/apium_hub/tdd-example-in-software-development-part-i-5bjn)

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[在 Java 中使用 Vavr 功能更强](https://dev.to/apium_hub/be-more-functional-in-java-with-vavr-5b4i)

[TDD 第一周期(第二部分)](https://apiumhub.com/tech-blog-barcelona/tdd-first-cycle/)的帖子最早出现在 [Apiumhub](https://apiumhub.com) 上。