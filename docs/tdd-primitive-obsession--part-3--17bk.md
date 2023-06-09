# TDD:原始执念(三)

> 原文：<https://dev.to/apium_hub/tdd-primitive-obsession--part-3--17bk>

上个月我们谈到了软件开发中的 [TDD 例子(第一部分)](https://dev.to/apium_hub/tdd-example-in-software-development-part-i-5bjn)和 [TDD 第一周期(第二部分](https://dev.to/apium_hub/tdd-first-cycle--part-2-34o2))。在这篇新的 TDD 和 primitive 执念文章中，我们将关注于消除重复和加强实体的构造者，这是拥有一个健壮系统的关键。

## TDD:原始执念

这里出现了一个有趣的 DDD 概念——使用[值对象](https://martinfowler.com/bliki/ValueObject.html),从头脑中移除[原始执念](https://refactoring.guru/smells/primitive-obsession)。字符串不是名字，名字是字符串的子集([细化类型](https://www.beyondthelines.net/programming/refined-types/))。在我们用 client 创建的无处不在的语言中，没有字符串，只有名字或姓氏。

在字符串集合内存在着*、【4】、*和你能想象到的一切，一个名字是一样的吗？我们不能忽视这个逻辑，与姓氏发生的情况一样，我们也不想重复代码…

让我们从名称[值对象](https://martinfowler.com/bliki/ValueObject.html)开始:

```
 export class Name {
  constructor(private value: string) {
    if (value === "") {
      throw new Error(`Name should not be empty`)
    }
  }
}

describe("given Name value object", () => {
  const nameGenerator = (value: string) => new Name(value);
  const emptyValueName = () => nameGenerator("");
  const validName = nameGenerator("Oscar");

  describe("with valid value", () => {
    test("should not throw", (done: any) => {
      expect(validName.toDTO()).toEqual("Oscar");
      done();
    });
  });
  describe("when empty value", () => {
    test("should throw", (done: any) => {
      expect(emptyValueName).toThrow();
      done();
    });
  });
}); 
```

我们有一个来自业务的 [ValueObject](https://martinfowler.com/bliki/ValueObject.html) ，我们用绿色测试验证了它，正如我们所了解的，但我们的用户现在不知道名称，我们必须让他知道:

```
 export class User {
  constructor(private id: string, private name: Name, private lastname: string) {
    if (lastname === "") {
      throw new Error("empty lastname");
    }
  }
} 
```

完美，现在测试可以了，因为我们已经传递了一个字符串，现在它是一个名称，我们真的要修改它吗？我们一路走来做错了什么吗？答案是否定的，我们不仅仅要修改用户创建的函数(记得在前面的重构中，我们将它移除为一个函数，我们只需要在一个地方改变它),但是在上面，我们将消除测试！不需要测试它在名字为空时抛出异常，因为它直接输入一个名字，名字已经做了验证，剩下的在编译中完成！新的测试代码如下所示:

```
 const createUser = (name: string, lastname: string) => new User("someId", new Name(name), lastname); 
```

哦！这是一个什么样的单位，其中用户收到正确的名称？好吧，如果你已经走了这么远还没有阅读来自 Fowler 的 UnitTest 链接，那么是时候这样做了，当你完成时，你会明白一切(最重要的是，你会喜欢它，我保证)。

我们已经重构了姓名，但是我们还需要姓氏，现在我们开始吧，因为所有的内容都是绿色的。这个例子很简单，因为它和名字一样！复制和粘贴时间！

```
 // test/LastNameSpec.ts
export class LastName {
  constructor(private value: string) {
    if (value === "") {
      throw new Error(`LastName should not be empty`)
    }
  }

  toDTO() {
    return this.value;
  }
}

describe("given lastname", () => {
    const lastNameGenerator = (value: string) => new LastName(value);
    const emptyValueLastName = () => lastNameGenerator("");
    const validLastName = () => lastNameGenerator("Oscar");

    describe("with valid value", () => {
      test("should not throw", (done: any) => {
        expect(validLastName).not.toThrow();
        done();
      });
    });

    describe("when empty value", () => {
      test("should throw", (done: any) => {
        expect(emptyValueLastName).toThrow();
        done();
      });
    });
});

// src/User.ts
export class User {
  constructor(private id: string, private name: Name, private lastname: LastName) {}
}

describe("given user", () => {
  const createUser = (name: string, lastname: string) => new User("someId", new Name(name), new LastName(lastname));
  const createValidUser = () => createUser("Oscar", "Galindo");
  const createUserWithEmptyName = () => createUser("", "Galindo");
  const createUserWithEmptyLastName = () => createUser("Oscar", "");

  describe("with valid data", () => {
    test("is created", (done: any) => {
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

  describe("with empty lastname", () => {
    test("should throw", (done: any) => {
      expect(createUserWithEmptyLastName).toThrow();
      done();
    });
  });
}); 
```

我认为下一个阶段(重构)非常重要，但事实是这三个阶段都很重要。知道我们处于哪个阶段并关注它是至关重要的。我们看到 Name 和 LastName 具有完全相同的验证逻辑，我们可以将它提取到一个更抽象的类(让我们称之为 NonEmptyString ), Name 和 LastName 从它扩展而来:

```
 export class NonEmptyString {
  constructor(protected value: string) {
    if (value === "") {
      throw new Error(`${this.constructor.name} should not be empty`)
    }
  }

  toDTO() {
    return this.value;
  }
}

export class Name extends NonEmptyString {}

export class LastName extends NonEmptyString {}

describe("given nonEmptyString value object", () => {
  describe("when create with valid data", () => {
    test("should not throw", (done: any) => {
      const validEmptyString = () => new NonEmptyString("some random string");
      expect(validEmptyString).not.toThrow();
      done();
    });
  });

  describe("with empty value", () => {
    test("should throw", (done: any) => {
      const invalidEmptyString = () => new NonEmptyString("");
      expect(invalidEmptyString).toThrow();
      done();
    });
  });
}); 
```

很好，在产品代码的层面上，我们已经有了一个有趣的重构和绿色的测试。我们需要重构的是测试，我们有几个重复的测试，由于这种重构，我们可以消除两个测试，这两个测试是用无效的名字和姓氏创建用户，为什么？因为我们的用户类不再理解字符串，它理解 Name 和 LastName，那这两个实体呢？如果它们是空的，它们就不存在，有人不可能用空值来构建这些实体，我们已经控制了它，用户构建的实体不再取决于我们的测试，而是取决于编译器，酷吧？更少的测试，更少的维护，我们的代码获得了可读性。

现在，在这一过程中，我们已经看到了如何在我们的[实体](http://sneakycode.net/entity-is-for-identity/)中将 ID 作为字符串拖动，现在我们理解了名称不是字符串而是名称的概念，我们可以认为 ID 属性是相同的，我们必须验证 ID 具有我们需要的格式，等等。

我们知道一个实体是通过它的 id 来识别的，那么，如果我们把一个实体和它自己进行比较，它们应该是相同的，对吗？让我们编写测试:

```
 describe("when compare same entity", () => {
    test("should be equal", (done: any) => {
      const user = User.create("Oscar", "Galindo");
      expect(user.equals(user)).toBeTrue();
      done();
    });
}); 
```

> 此时，我们已经在 User 类中创建了两个静态方法，一个用于创建，另一个用于实例化(注册一个新用户或实例化一个以前注册的用户)，唯一的区别是一个接收 ID，另一个不接收。
> 
> ```
>  export class User {
> static create(name: string, lastname: string): User {
> return new User(v4(), new Name(name), new LastName(lastname));
> }
> 
> static new(id: string, name: string, lastname: string): User {
> return new User(id, new Name(name), new LastName(lastname));
> }
> } 
> ```

我们可以考虑使用**显而易见的实现**,因为这种实现既熟悉又简单，但我知道我们必须进行两次测试，一次是在两个用户相同的情况下，另一次是在他们不同的情况下，所以我们将一步一步来，这不需要任何成本:

```
 export class User {
  ...
  equals(other: User): boolean {
    return true;
  }
} 
```

我们已经有了绿色的测试，我们创建了下面的测试，它将覆盖该方法的所有情况，我们将它置于绿色并准备好:

```
 // userSpec.ts
describe("when compare two differents users", () => {
    test("should return false", (done: any) => {
      const user = User.create("Oscar", "Galindo");
      const otherUser = User.create("Pedro", "Garcia");
      expect(user.equals(otherUser)).toBeFalse();
      done();
    });
});

// user.ts
export class User {
  ...
  equals(other: User): boolean {
    return this.id === other.id;
  }
} 
```

我们进入重构阶段，在这个阶段，我们不仅消除了重复，我们还在 TDD 提供的空间内设计我们的应用程序。

一个*用户*知道，要将自己与另一个*用户*进行比较，比较标识符( *id* )就足够了，但是，*用户*是否有责任具体实现*等于*？最好的解决方案是，该标识符是与另一个标识符进行比较的标识符，也就是说，我们必须将 *id* 转换为 ***值对象*** 。

我们将按照创建*名称*的相同方式创建*用户 Id* ，我们要做的是创建一个具有两个静态方法的类，以生成/实例化用户 Id 并在我们的新类中实现 equals，我们需要为 equals 创建一个测试:

```
 export class UserId extends NonEmptyString {
  static new(id: string): UserId {
    return new UserId(id)
  }
  static generate(): UserId {
    return new UserId(v4());
  }

  equals(otherUserId: UserId): boolean {
    return this.value === otherUserId.value;
  }
}

describe("given UserId", () => {
  describe("when compare with same userId", () => {
    test("should return true", (done: any) => {
      const userId = UserId.new("someId");
      expect(userId.equals(userId)).toBeTrue();
      done();
    });
  });

  describe("when compare with other userId", () => {
    test("should return false", (done: any) => {
      const userId = UserId.new("someId");
      const otherUserId = UserId.new("otherId");
      expect(userId.equals(otherUserId)).toBeFalse();
      done();
    });
  });
}); 
```

在我们的类中，我们可以将等号的实现更改为如下形式:

```
 export class User {
  ...
  equals(other: User): boolean {
    return this.id.equals(other.id);
  }
} 
```

> 所有的测试仍然是绿色的，我们在短时间内实现了更好的设计和更好的解耦。

此时，我们要寻找的是创建一个*便笺*，那么让我们想一想，谁来创建便笺？怎么会？需要哪些数据？重要的是要理解实体通常不会自己创建，你必须知道[它们应该在哪里从](http://udidahan.com/2009/06/29/dont-create-aggregate-roots/)开始创建。注释不是单独创建的，它是由用户创建的，因此用户必须有一个 *createNote* 方法来生成*注释*，我们将创建我们用例的测试:

```
 describe("when create a note", () => {
  test("should return a note", (done: any) => {
    const authorId = UserId.new("someAuthorId");
    const user = User.new(authorId, "Oscar", "Galindo");

    const note = user.createNote("Some text");
    expect(note.toString()).toContain(`someAuthorId,Some text`);
    done();
  });
}); 
```

现在我们已经以所有可能的方式破坏了我们的测试，*注释*不存在，*创建注释*不是*用户*类的方法，另一方面，我们做的测试是 *toString ()* 包含我们的 *authorId* 和注释的文本，我们不能将它与特定的注释进行比较，因为 *NoteID* 不是确定性的，我们没有办法知道 Id 将会以下是测试这些案例的一些方法:

*   属性的访问器。一种方法是通过测试您创建的注释的属性，但是我认为通过对属性做 getters，我们打破了封装或者我们开始这样做(当您访问属性时封装被打破，这样我们就可以从外部应用某些行为，而不是当我们将属性公开时)。
*   每个实体都知道如何成为自己的 DTO。将实体转换为 DTO 并测试其属性，此时我们的应用程序中没有 dto，测试它们有些麻烦。
*   控制 id 生成的方式，使用我们已经完全耦合到 uuid 库的当前实现，如果我们制作库的包装器，我们可以做一个包装器存根并随时知道生成了什么 ID，当我们测试并验证用户创建的笔记与我们可以在测试中创建的笔记完全相同时，我们将获得我们所寻求的确定性。

> 我在[单元测试的艺术](https://www.manning.com/books/the-art-of-unit-testing)中读到了最后一个策略，它适用于*日期时间*，基本上我们所做的是在生成一个新的 Id tsaying 它必须返回时从等式中删除不确定性。我们创建了一个静态的*生成器*属性，它默认分配了我们用来生成 id 的库，并用一个返回我们知道的字符串的函数替换它。

一旦测试被解释，我们将开始实现我们的用例，我们将在*用户*中创建返回新的*注释*的方法。

一个*注释*是一个实体，每个实体由一个 ID 标识，就像*用户*一样。在这种情况下，*注释*将只有一个属性(删除其标识符)，该属性将是注释的文本( *text* ):

```
 export class NoteId {
  constructor(private value: string) {}

  static generate() {
    return new NoteId(v4());
  }
}

export class Note {
  constructor(private noteId: NoteId, private authorId: UserId, private text: string) {}

  static create(authorId: UserId, textNote: string): Note {
    return new Note(NoteId.generate(), authorId, textNote);
  }

  static new(id: string, authorId: UserId, text: string): Note {
    return new Note(new NoteId(id), authorId, text);
  }
}

export class User {
  ...
  createNote(textNote: string): Note {
    return Note.create(this.id, textNote);
  }
} 
```

我们已经有了一个知道如何创建*笔记*的*用户*，所有的测试都是绿色的……现在，是时候进行重构了。在 TDD 的这个阶段，我们可以遵循的两条规则(对我来说是主要的)是寻找相同类中的重复代码和不同类中的重复代码(可能的抽象)。( [TDD 清单](https://www.giorgiosironi.com/2010/03/tdd-checklist-red-green-refactor-in.html))

在 *Note* 类中，我们没有任何类型的重复，但是如果在 *NoteId* 和 *UserId* 类之间有重复，它们是标识实体的值对象，我们可以抽象一个 EntityId 类并扩展它的 NoteId 和 UserId。

```
 export abstract class EntityId extends NonEmptyString {
  equals(entityId: EntityId): boolean {
    return this.value === entityId.value;
  }
}

export class UserId extends EntityId {
  static new(id: string): UserId {
    return new UserId(id)
  }

  static generate(): UserId {
    return new UserId(v4());
  }
}

export class NoteId extends EntityId {
  static new(id: string): NoteId {
    return new NoteId(id)
  }

  static generate(): NoteId {
    return new NoteId(v4());
  }
} 
```

## 结论:TDD 和原始执念

在本章中，我们已经看到了如何利用值对象来增强实体的构造函数。我们已经消除了测试，因为编译完成了剩下的工作，我们已经消除了重复，这是在真正的 TDD 开发中必须遵循的过程，持续重构。我们的应用程序的重构不是可选的，随着一个进化的设计，我们得到健壮的应用程序工作。

在下一章中，我们将进入持久性的世界，我们将如何在基础设施问题上进行 TDD，尽可能与我们使用的数据库分离，等等。

如果你想了解更多关于 TDD 和原始执念的知识，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

## 如果你觉得这篇关于 TDD 和原始痴迷的文章很有趣，你可能会喜欢…

[软件开发中的 TDD 示例(第一部分)](https://dev.to/apium_hub/tdd-example-in-software-development-part-i-5bjn)

[TDD:第一周期(第二部分)](https://dev.to/apium_hub/tdd-first-cycle--part-2-34o2)

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

帖子 [TDD:原始执念(第三部)](https://apiumhub.com/tech-blog-barcelona/tdd-primitive-obsession/)最早出现在 [Apiumhub](https://apiumhub.com) 上。