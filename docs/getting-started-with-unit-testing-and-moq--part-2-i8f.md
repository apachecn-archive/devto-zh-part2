# 单元测试和 Moq 入门—第 2 部分

> 原文：<https://dev.to/kritner/getting-started-with-unit-testing-and-moq--part-2-i8f>

我在我的博客上将我的 RSS 订阅改成了 medium，但是这些还是会弹出来...所以转贴(虽然我可能已经改变了一些内容)-而且，哦，该死，代码片段不能在两者之间很好地翻译 D:

在[之前的帖子](http://kritner.blogspot.com/2015/11/getting-started-with-unit-testing-and.html)中，我开始了一个[项目](https://github.com/Kritner/RussUnitTestSample/tree/8a35155511e9dc579dc6e675241187d7cca189b5)，开始学习单元测试，特别是针对 moq 和 WCF 服务。到目前为止，我们只对一段没有外部依赖的代码实现了基本的单元测试，这不需要模仿。

这一次，我将探索模仿和测试依赖于 IDbGetSomeNumbers 和 INumberFunctions 接口的对象。提醒一下，那些接口和相应的类被定义为:

```
namespace RussUnitTestSample.Business.Interface
{

    /// <summary>
    /// Interface for number functions
    /// </summary>
    public interface INumberFunctions
    {
        /// <summary>
        /// Add numbers together
        /// </summary>
        /// <param name="numbers">The numbers to add.
        /// <returns>The sum</returns>
        double AddNumbers(double[] numbers);
    }

    /// <summary>
    /// Interface to get some numbers from the database
    /// </summary>
    public interface IDbGetSomeNumbers
    {

        /// <summary>
        /// Get an array of doubles from the database
        /// </summary>
        /// <returns></returns>
        double[] GetSomeNumbers();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以及使用它们的类:

```
namespace RussUnitTestSample.Business
{

    /// <summary>
    /// Get numbers and then add them together
    /// </summary>
    public class GetNumbersAndAddThem
    {

        #region Private
        private readonly IDbGetSomeNumbers _dbGetSomeNumbers;
        private readonly INumberFunctions _numberFunctions;
        #endregion Private

        #region ctor

        /// <summary>
        /// Constructor - provide dependencies
        /// </summary>
        /// <param name="dbGetSomeNumbers">THe IDbGetSomeNumbers implementation.
        /// <param name="numberFunctions">The INumberFunctions implementation.
        public GetNumbersAndAddThem(IDbGetSomeNumbers dbGetSomeNumbers, INumberFunctions numberFunctions)
        {
            if (dbGetSomeNumbers == null)
                throw new ArgumentNullException(nameof(dbGetSomeNumbers));

            if (numberFunctions == null)
                throw new ArgumentNullException(nameof(numberFunctions));

            this._dbGetSomeNumbers = dbGetSomeNumbers;
            this._numberFunctions = numberFunctions;
        }

        #endregion ctor

        #region Public methods

        /// <summary>
        /// Get the numbers and add them.
        /// </summary>
        /// <returns></returns>
        public double Execute()
        {
            var numbers = _dbGetSomeNumbers.GetSomeNumbers();

            return _numberFunctions.AddNumbers(numbers);
        }

        #endregion Public methods

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数中，我接受了`IDbGetSomeNumbers`和`INumberFunctions`的实现。我这样做是因为它们不依赖于类的功能，因此它们的实现并不重要。更确切地说，它*是*重要的，但不是为了这个类的测试。正如单元测试定义所述:单元测试是一个软件开发过程，在这个过程中，一个应用程序的最小的可测试部分，称为单元，被单独和独立地仔细检查，以确保正确的操作。

所以接口实现*确实*需要测试(已经完成了)，但是*不*需要测试。从这个角度来看，唯一需要测试的是该类的构造是否正确，以及 Execute“从数据库中获取数字”然后“将它们相加”。

因为我使用了类构造函数来获取类依赖:

```
/// <summary>
/// Constructor - provide dependencies
/// </summary>
/// <param name="dbGetSomeNumbers">THe IDbGetSomeNumbers implementation.
/// <param name="numberFunctions">The INumberFunctions implementation.
public GetNumbersAndAddThem(IDbGetSomeNumbers dbGetSomeNumbers, INumberFunctions numberFunctions)
{
    if (dbGetSomeNumbers == null)
        throw new ArgumentNullException(nameof(dbGetSomeNumbers));

    if (numberFunctions == null)
        throw new ArgumentNullException(nameof(numberFunctions));

    this._dbGetSomeNumbers = dbGetSomeNumbers;
    this._numberFunctions = numberFunctions;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以测试的第一件事是`dbGetSomeNumbers`和`numberFunctions`不为空。这可以这样实现:

```
/// <summary>
/// Ensure ArgumentNullException thrown when no IDbGetSomeNumbers implementation is provided
/// </summary>
[ExpectedException(typeof(ArgumentNullException))]
[TestMethod]
public void GetNumbersAndAddThem_Constructor_NullIDbGetSomeNumbers()
{
    // Arrange / Act / Assert
    GetNumbersAndAddThem obj = new GetNumbersAndAddThem(null, _mockNumberFunctions.Object);
}

/// <summary>
/// Ensure ArgumentNullException thrown when no NumberFunction implementation is provided
/// </summary>
[ExpectedException(typeof(ArgumentNullException))]
[TestMethod]
public void GetNumbersAndAddThem_Constructor_NullNumberFunctions()
{
    // Arranage / Act / Assert
    GetNumbersAndAddThem obj = new GetNumbersAndAddThem(_mockIDbGetSomeNumbers.Object, null);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于测试执行，我们终于可以达到最低起订量！模仿与单元测试密切相关，正如模仿的一个[定义](https://msdn.microsoft.com/en-us/library/ff650441.aspx)所说:

> 很难用实时的系统级测试来测试错误条件和异常。通过用模拟对象替换系统组件，可以在单元测试中模拟错误情况。一个例子是当业务逻辑处理由依赖服务抛出的数据库满异常时。

因此，因为`IDbGetSomeNumbers`和`INumberFunctions`的实现无关紧要，所以我们不想(必须)使用它们的真实实现。这是因为它们可能会潜在地影响系统或数据，这是我们不想做的，因为我们计划在每次构建时运行这些测试…并且在每次构建时编辑应用程序数据将是…不好的。无论如何，通过模仿，我们可以告诉被调用的接口返回一个特定的响应。这意味着我们可以让 Execute 功能使用完全模拟的依赖实现，只需测试 Execute 函数接收并传回适当类型的值。嘲讽设置:

```
/// <summary>
/// Unit tests for GetNumbersAndAddThem
/// </summary>
[TestClass]
[ExcludeFromCodeCoverage]
public class GetNumbersAndAddThemTests
{

  #region Private
  Mock<inumberfunctions> _mockNumberFunctions;
  Mock<idbgetsomenumbers> _mockIDbGetSomeNumbers;
  #endregion Private

  #region Public methods

  /// <summary>
  /// Setup mock objects
  /// </summary>
  [TestInitialize]
  public void Setup()
  {
      _mockNumberFunctions = new Mock<inumberfunctions>();
      _mockIDbGetSomeNumbers = new Mock<idbgetsomenumbers>();
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

字段`_mockNumberFunctions`和`_mockIDbGetSomeNumbers`被设置为`Mock<interface>`。在`Setup`中，我们只是简单地更新它们。现在到好的部分，利用模拟的测试:

```
/// <summary>
/// Tests that GetNumbersAndAddThem.Execute gets numbers and then adds them.
/// </summary>
[TestMethod]
public void GetNumbersAndAddThem_Execute()
{
    // Arrange
    double[] numbersToUse = { 1, 2, 3, 4, 5 };
    double expected = numbersToUse.Sum();

    _mockIDbGetSomeNumbers.Setup(s => s.GetSomeNumbers()).Returns(numbersToUse);
    _mockNumberFunctions.Setup(s => s.AddNumbers(It.IsAny<double>())).Returns(expected);

    GetNumbersAndAddThem obj = new GetNumbersAndAddThem(_mockIDbGetSomeNumbers.Object, _mockNumberFunctions.Object);

    // Act
    var result = obj.Execute();

    // Assert
    Assert.AreEqual(expected, result);
} 
```

Enter fullscreen mode Exit fullscreen mode

在`_mockIDbGetSomeNumbers.Setup(…).Returns(…)`中，我们声明当`GetSomeNumbers()`函数被调用时，它需要返回`numbersToUse`。相当花哨！我们没有依赖于必须发送到数据库的`IDbGetSomeNumbers`的具体实现，而是告诉它使用这个定义好的数字列表，这个列表已经由 mock 的设置详细说明了。现在我们可以绝对肯定地说 numbersToUse 的总和将是多少，因为我们知道每次将提供什么数字，因为它们不是从数据库中提取的。希望这一切都有意义。反正对我有意义！:O

下一次我希望进入 WCF 创作和测试。

*原载于 2015 年 12 月 16 日*[*【kritner.blogspot.com】*](https://dev.to/kritner/getting-started-with-unit-testing-and-moq---part-2-406)*。*