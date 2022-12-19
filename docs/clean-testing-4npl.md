# 干净测试

> 原文：<https://dev.to/franiglesias/clean-testing-4npl>

一些策略，我试图使我的测试变得越来越干净和可读，从而能够更好地交流我编写的软件所做的事情。

我在工作中写了很多测试，而且我倾向于在 TDD 模式下尽可能地做。尽管如此，我并不是特别为我的测试感到自豪，我认为我在这方面有很大的改进空间。我特别关注两个方面:

*   第一，我发现我还遗漏了很多案例，特别是在整合方面，这些案例最终导致软件缺陷。对此，我需要进一步思考，采取更好的设计策略。
*   第二个问题是测试代码的质量。最后，测试仍然是一个代码，应应用设计原则和良好做法，以确保其可维护性、可读性和有用性。而且，在我看来，即使是对不懂编程语言的人来说，它们也应该是可读的。

从第二个方面来看，这就是本文所涉及的问题，我试图在其中反映我最近正在处理的一些想法。

其中很大一部分是根据我的同事和团队领导在[【holaluz】](https://twitter.com/holaluzeng?lang=es)、[【mavi Jiménez】](https://www.youtube.com/watch?v=yvMGWCYqT04)中的谈话进行的，这非常好地解释了我们的测试为什么以及如何必须是坚实的，除了扎实的外，还提出了

[看完演讲稿](https://www.youtube.com/watch?v=6t2Z7uf16F4)，你可以继续阅读。

## 命名德洛考

我越来越多地尝试写一些测试，甚至在不知道编程的情况下也能读出其名称，这样我就能理解什么是测试，从而使我未来的自我以及面临这一守则的同伴的生活变得更容易。我希望我能把这变成一种习惯。

### 使用抽象

例如，以下测试表示，测试的类或方法不应接受超过一定字符数的字符串。

```
// Not so good

public function testShouldNotAcceptStringsLongerThanNineCharacters()

// Better

public function testShouldNotAcceptStringsLongerThanAMaximumOfCharacters() 
```

我不放具体数字的原因是这个数字是一个可以改变的细节。在措辞*较好*中，我指的是概念而不是具体，因此即使上限发生变化或可配置，测试也会继续进行。

我尽可能避免输入技术细节，虽然有时会很复杂，就像我前面提到的字符串一样，但我更喜欢编写不会透露实现情况的测试。

假设在教育管理应用程序中有一个学生存储库:

```
// Not so good

public function testFindAllByClass()

// Better

public function testShouldRetrieveStudentsInAGivenClass() 
```

案件*不那么好*有两个主要缺陷:

*   首先说测试方法`FindAllByClass`，假设存在这种方法。如果我们更改了它的名称，测试将开始说谎。
*   其次，它没有说明预期会发生什么，这就是考试的理由。他只是说，无论是学生返回，还是哭泣或地铁票，都可以证明这一点。

案件*较好*同时解决两个问题:

*   不绑定到方法的任何特定实现。
*   它说明了如果测试通过应该会发生什么。

### 测试表明应该发生什么

使用应作为测试名称的一部分值得一提。事实上，我希望能够删除 test 前缀，这使得“~~test framework〖t1〗〗phpunt 确定了要执行的方法。有几个解决方案:~~

使用我不喜欢的注释:

```
/** @test */
public function shouldRetrieveStudentsInAGivenClass() 
```

通过 PHPUnit 的插件设置一个[替代前缀](https://github.com/unfunco/phpunit-alternative-test-prefix)，这也不能使我信服，因为公约已经非常确立，所以我可以写下上述内容而无需注释:

```
public function shouldRetrieveStudentsInAGivenClass() 
```

同样，我喜欢以`it`为前缀的 **phpspec** 语法，这比`test`更自然:

```
public function it_should_create_a_user_from_retrieved_data(); 
```

不管怎么说，我喜欢应该作为前缀，因为它意味着□，表明被证明的单位应该做些什么，因此，如果不做的话就是错的。

```
public function testShouldCalculateTheDiscountedPrice() 
```

我看到了一些建议，希望用" Should "作为" TestCase "的后缀，我不确定这是否能在 PHPUnit 起作用，但我必须承认，它会影响:

```
class MyClassShould extends TestCase
{
    /** @test */
    public function doThatThing()
    {
        // given, when, then code...
    }
} 
```

### 测试证明我们遵守了业务规则

我喜欢的另一个命名策略是遵守业务规则和不变量。

也就是说，我们应该有一个测试来验证是否符合定义我们的域对象可以和不能执行的操作的业务规则，或使域对象有效的条件。

例如，`Commercial`系数的此测试相当清楚:

```
public function testShouldNotCreateASCommercialManagerWithoutCommercialAdmin
{
} 
```

O este:

```
public function testShouldNotAllowNifsLongerThanMaxCharacters
{
} 
```

因此，解决这一问题的一个好方法是起草一份“T0”的支配规则和不变规则，指导我们决定在给定时间要测试什么

## 去掉神奇数字

我希望在测试中避免的一个缺陷是，这些例子的价值显然具有任意性，而且缺乏意义。我的意思是，对于一个阅读测试的人来说，一读起来可能很难理解为什么我们选择测试一种价值观而不是另一种价值观，以及它们有什么意义。

有一些方法可以选择我们测试时使用的值，例如*等值类分区*或*边界值分析*，这些方法用于确保测试复盖所有可能的方案，且测试次数最少，从而通过设置以下各项来解决第一个问题

例如，**等值类分区**是一种非常简单的技术，我们用它将所有可能的情况归入**类**类，这样一个类的所有值都会彼此相等，所以其中任何一个值都代表着它被分类到的类。因此，我们可以测试这些类中的每一类，而不是尝试测试所有可能的值。

想象一下，一个按年龄收费的制度，在博物馆和其他文化机构中相当普遍，因此门票的价格是:

*   长达 7 年:0 欧元
*   德 8 一 15: 6
*   德 16 a 25: 9
*   德 26 a 64: 12
*   一个 65: 10 的聚会

也就是说，所有可能年龄的范围都分为五类或五类，我们只需为每一类选择一个例子。显然没有必要逐一测试所有 0 至 110 岁的病例。

所以我们可以做一个这样的案例表:

| 上课了 | 英勇 | 普雷西奥 |
| --- | --- | --- |
| 7 o 月 | five | Zero |
| 8 - 15 | Ten | six |
| 16 - 25 | Eighteen | nine |
| 26 - 64 | Thirty | Twelve |
| 65 岁以上 | Seventy | Ten |

所以我们可以做这样的测试:

```
public function testCalculatesPriceForSevenOrLess()
{
    $priceCalculator = new PriceCalculator();
    $this->assertEquals(0, $priceCalculator->forAge(5));
} 
```

好吧，测试是正确的，但是，∞它让你口中的味道不好？

如果我们仔细想想数据，很容易看出，我们可以给类赋予一个有意义的名称:

| 上课了 | 间隔 | 英勇 | 普雷西奥 |
| --- | --- | --- | --- |
| 儿童 | 7 o 月 | five | Zero |
| 青少年 | 8 - 15 | Ten | six |
| 年轻的 | 16 - 25 | Eighteen | nine |
| 成人 | 26 - 64 | Thirty | Twelve |
| 长辈 | 65 岁以上 | Seventy | Ten |

并在测试的所有元素中使用，应用所谓的无处不在的语言:

```
public function testCalculatesPriceForAChild()
{
    $childAge = 5;
    $expectedPrice = 0;
    $priceCalculator = new PriceCalculator();
    $this->assertEquals(
        $expectedPrice, 
        $priceCalculator->forAge($childAge)
    );
} 
```

这已经好多了。现在测试更具交际性。但我认为如果我们使用常数会更好；

```
public function testCalculatesPriceForAChild()
{
    $priceCalculator = new PriceCalculator();
    $this->assertEquals(
        self::PRICE_FOR_CHILDREN, 
        $priceCalculator->forAge(self::CHILD)
    );
} 
```

当然，我们可以使用数据提供程序，但这并不能消除以下情况:

```
/** @dataProvider agePriceProvider */
public function testCalculatesPriceForAAge(int $ageGroup, int $expectedPrice)
{
    $priceCalculator = new PriceCalculator();
    $this->assertEquals(
        $expectedPrice, 
        $priceCalculator->forAge($ageGroup)
    );
}

public function agePriceProvider()
{
    return [
        'Child' => [self::CHILD, self::PRICE_FOR_CHILDREN],
        'Teen' => [self::TEEN, self::PRICE_FOR_TEENS]
        //... you see the point
    ];
} 
```

## 提取方法，也在测试中

另一件让我很困扰的事是，所有必要的程式码，用来准备一个不会传达测试本身的场景。使测试很难阅读，甚至知道实际发生了什么。

毕竟，测试的基本结构非常简单:

*   **给出**:给出了情景和初步数据
*   **When** :被测对象
*   **那么**:那么我们有一些效果

这种结构应始终清晰可见，但在某些情况下(如前一个例子)，这种结构并不十分明确。事实上，我们可以这样写这个例子，让它更清楚地展现出来。

```
public function testCalculatesPriceForAChild()
{
    $priceCalculator = new PriceCalculator();

    $priceForAChild = $priceCalculator->forAge(self::CHILD);

    $this->assertEquals(
        self::PRICE_FOR_CHILDREN, 
        $priceForAChild
    );
} 
```

但是，在某些情况下，场景并不简单，需要更详细的准备工作，例如我们构建双测试时(在本示例中，我们已经将双测试构建到外部类中):

```
public function testShouldRetrieveStudentsInAGivenClass()
{
    $studentsRepository = $this->StudentsRepositoryDoubleBuilder()
        ->loadWithFixtureDataFromFile('../students.yml')
        ->assertFindByClass()
        ->build();

    $classRepository = $this->classRepositoryDoubleBuilder()
        ->loadWithFixtureDataFromFile('../classes.yml')
        ->assertFindByName('Class A')
        ->build();

    $getStudentsInClass = new GetStudentsInClass(
        $studentsRepository,
        $classRepository
    );

    $request = new GetStudentsInClassRequest('Class A');
    $listOfStudents = $getStudentsInClass->execute($request);

    $this->assertCount(self::STUDENTS_COUNT_IN_CLASS_A, $listOfStudents);
} 
```

虽然测试也不复杂，但包括紧固件负载在内的方案准备是一个实施细节，不一定有助于了解正在发生的情况。

什么不按以下方式做？

```
public function testShouldRetrieveStudentsInAGivenClass()
{
    $studentsRepository = $this->prepareStudentRepository();
    $classRepository = $this->prepareClassRepository();

    $getStudentsInClass = new GetStudentsInClass(
        $studentsRepository,
        $classRepository
    );

    $request = new GetStudentsInClassRequest('Class A');
    $listOfStudents = $getStudentsInClass->execute($request);

    $this->assertCount(self::STUDENTS_COUNT_IN_CLASS_A, $listOfStudents);
}

public function prepareStudentsRepository()
{
    return $this->StudentsRepositoryDoubleBuilder()
        ->loadWithFixtureDataFromFile('../students.yml')
        ->assertFindByClass(123)
        ->build();
}

public function prepareClassRepository()
{
    return $this->classRepositoryDoubleBuilder()
        ->loadWithFixtureDataFromFile('../classes.yml')
        ->assertFindByName('Class A')
        ->build();
} 
```

我们所做的是用自己的方法从存储库中提取双份准备，使我们能够更简洁明了地编写测试。

很明显，在实际项目中，我们可以从设置方法的准备工作中提取很大一部分，包括服务实例化，甚至以某种方式参数化方法`prepare*`，但我认为测试主体具有相同抽象水平的线这一想法是清楚的。

最后，双建筑商本身也值得一篇文章，也许有一天会写出。

## 期待例外

很久以前，我决定尽量减少在代码中使用注释，因为这样会给我带来一定的不安全感。因此，我不会将测试标记为预期异常，而是在代码中明确预期:

```
/** @expectException InvalidArgumentException */
public function testShouldNotAllowTooLongStrings()
{
    $nif = new NIF(self::TOO_LONG_STRING);
}

// vs

public function testShouldNotAllowTooLongStrings()
{
    $this->expectException(InvalidArgumentException::class);

    $nif = new NIF(self::TOO_LONG_STRING);
} 
```

相反的是，我并不完全赞同这种期望。注释位于测试的开头，但 give-> when-> then 结构告诉我们应该在测试的结尾:

```
public function testShouldNotAllowTooLongStrings()
{
    $nif = new NIF(self::TOO_LONG_STRING);

    $this->expectException(InvalidArgumentException::class);
} 
```

部分地，我更倾向于第一种选择，恰恰是因为其特殊性。

## 梅托多斯断言

有时，如果我需要做一些复杂的断言或需要某种准备，我会写一个名为 assert 的方法来封装它。例如，此方法用于比较两个阵列而不考虑顺序:

```
protected function assertEqualsArrays($expected, $actual, $message = null)
{
    sort($expected);
    sort($actual);
    $this->assertEquals($expected, $actual, $message);
} 
```

这使我想到，测试中的一些三角形也可以封装在一种方法中:

```
protected function assertValidCommercial(Commercial $commercial)
{
    $this->assertEquals(CommercialType::fromString('admin'), $commercial->type());
    $this->assertEquals(null, $commercial->parent());
    $this->assertEquals([CommercialRole::ROLE_ADMIN], $commercial->getRoles());
} 
```

## 总结

到目前为止，有很多想法可以写更干净、更易读、更可持续的测试。

测试愉快！(但别忘了考德也有悲伤的经历。)

## 脚注