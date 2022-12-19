# 单元测试和 Moq 入门—第 1 部分

> 原文：<https://dev.to/kritner/getting-started-with-unit-testing-and-moq--part-1-5bo6>

我在我的博客上将我的 RSS 订阅改成了 medium，但是这些还是会弹出来...所以转贴(虽然我可能已经改变了一些内容)-而且，哦，该死，代码片段不能在两者之间很好地翻译 D:

[![](../Images/dd0582eae2cc564887e80cc8fea16dc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gDb6ZSYt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2APC7skh24BEQjcTqF) 

<figcaption>【使用笔记本电脑编程的人】作者[史蒂夫·哈拉马](https://unsplash.com/@steve3p_0?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)T8】</figcaption>

我们最近有了一个新的团队领导，他似乎在我只是模糊熟悉的领域有相当多的经验，主要是通过阅读。首先被推动的事情之一是集中在单元测试上。虽然几个月前我确实开始对我们的代码库进行一些测试(到目前为止大约有 250 个)，但我觉得还有很长的路要走。幸运的是克里斯在这里帮助我们传授知识，万岁！

所以从单元测试开始——首先应该定义什么是单元测试[https://en.wikipedia.org/wiki/Unit_testing](https://en.wikipedia.org/wiki/Unit_testing):

> *在计算机编程中，* ***单元测试*** *是一种软件* ***测试*** *方法，通过该方法对源代码的单个***单元、一个或多个计算机程序模块的集合以及相关的控制数据、使用过程和操作过程进行测试，以确定它们是否适合使用。**

 *给定以下类/方法:

```
namespace  RussUnitTestSample.Business.Interface
{

    /// <summary>
    /// Interface to get some numbers from the database
    /// </summary>
    public  interface IDbGetSomeNumbers
    {

        /// <summary>
        /// Get an array of doubles from the database
        /// </summary>
        /// <returns></returns>
        double [] GetSomeNumbers ();
    }

    /// <summary>
    /// Interface for number functions
    /// </summary>
    public  interface INumberFunctions
    {
        /// <summary>
        /// Add numbers together
        /// </summary>
        /// <param name="numbers">The numbers to add.</param>
        /// <returns>The sum</returns>
        double  AddNumbers ( double [] numbers);
    }
}

namespace  RussUnitTestSample.Business
{

    /// <summary>
    /// Get numbers and then add them together
    /// </summary>
    public  class  GetNumbersAndAddThem
    {

        #region Private
        private  readonly IDbGetSomeNumbers _dbGetSomeNumbers;
        private  readonly INumberFunctions _numberFunctions;
        #endregion Private

        #region ctor

        /// <summary>
        /// Constructor - provide dependencies
        /// </summary>
        /// <param name="dbGetSomeNumbers">THe IDbGetSomeNumbers implementation.</param>
        /// <param name="numberFunctions">The INumberFunctions implementation.</param>
        public  GetNumbersAndAddThem (IDbGetSomeNumbers dbGetSomeNumbers, INumberFunctions numberFunctions)
        {
            if (dbGetSomeNumbers == null )
                throw  new  ArgumentNullException (nameof(dbGetSomeNumbers));

            if (numberFunctions == null )
                throw  new  ArgumentNullException (nameof(numberFunctions));

            this._dbGetSomeNumbers = dbGetSomeNumbers;
            this._numberFunctions = numberFunctions;
        }

        #endregion ctor

        #region Public methods

        /// <summary>
        /// Get the numbers and add them.
        /// </summary>
        /// <returns></returns>
public  double  Execute ()
        {
var numbers = _dbGetSomeNumbers.GetSomeNumbers();

return _numberFunctions.AddNumbers(numbers);
        }

        #endregion Public methods

    }

} 
```

注意，在上面，我使用接口来允许依赖项的注入(一般来说，这是模拟单元测试的一个重要部分)。基本的想法是，您向整体的相关部分提供样本(不重要的)实现，这些部分当前没有被测试，因此对测试不重要——至少在测试“把所有的放在一起”的方法时。我看到了以下需要测试的东西—很可能还有更多，但这里只是一目了然:

*   `INumberFunctions.AddNumbers`
*   `IDbGetSomeNumbers.GetSomeNumbers`
*   `GetNumbersAndAddThem.Execute`

还有一些其他的问题将变得很明显(如果它们还没有出现的话),比如在构造函数中测试空参数，测试空数组以增加数字，等等。对于`INumbersFunctions.AddNumbers`，我们当然需要检查数字是否被正确地相加。我通过以下测试实现了这一点:

```
using  System;
using  System.Diagnostics.CodeAnalysis;
using  System.Linq;
using  Microsoft.VisualStudio.TestTools.UnitTesting;

namespace  RussUnitTestSample.Business.Tests
{
    [TestClass]
    [ExcludeFromCodeCoverage]
    public  class  NumberFunctionTests
    {

        #region Public methods

        /// <summary>
        /// Test exception thrown when numbers provided is null
        /// </summary>
        [ExpectedException(typeof(ArgumentNullException))]
        [TestMethod]
        public  void  AddNumbers_NullParameterNumbers ()
        {
            // Arrange / Act / Assert
            NumberFunctions nf = new NumberFunctions();
            var result = nf.AddNumbers( null );
        }

        /// <summary>
        /// Test exception thrown when 0 numbers provided in array
        /// </summary>
        [ExpectedException(typeof(ArgumentException))]
        [TestMethod]
        public  void  AddNumbers_EmptyArrayNumbers ()
        {
            // Arrange / Act / Assert
            NumberFunctions nf = new NumberFunctions();
            var result = nf.AddNumbers( new  double [] { });
        }

        /// <summary>
        /// Add two positive numbers
        /// </summary>
        [TestMethod]
        public  void  AddNumbers_TwoNumbers ()
        {
            // Arrange
            double [] numbers = { 1 , 2 };
            NumberFunctions nf = new NumberFunctions();

            // Act
            var result = nf.AddNumbers(numbers);

            // Assert
            Assert.AreEqual(numbers.Sum(), result);
        }

        /// <summary>
        /// Add 10 numbers mixed positive and negative
        /// </summary>
        [TestMethod]
        public  void  AddNumbers_TenNumbersWithPositiveAndNegatives ()
        {
            // Arrange
            double [] numbers = { 1 , - 2 , 3 , 4 , - 5 , 6 , 7 , - 8 , 9 , 10 };
            NumberFunctions nf = new NumberFunctions();

            // Act
            var result = nf.AddNumbers(numbers);

            // Assert
            Assert.AreEqual(numbers.Sum(), result);
        }

        [TestMethod]
        public  void  AddNumbers_ProvideOneNumber ()
        {
            // Arrange
            double [] numbers = { 1 };
            NumberFunctions nf = new NumberFunctions();

            // Act
            var result = nf.AddNumbers(numbers);

            // Assert
            Assert.AreEqual(numbers.Sum(), result);
        }

        #endregion Public methods
    }
} 
```

在一些测试中肯定会有一些重叠，但是对于如此简单的东西，还是有一些！我觉得 NumberFunctions 的实际实现并不重要，因为我正专注于测试。它负责完成非 Moqed 测试的类。在下一篇文章中(我希望很快会写),我会讲述我是如何用 Moq 完成第一个单元测试的。如果我能有足够好的节奏，我希望涵盖莫青 WCF 服务电话——因为这是我们在工作中与我们的数据库沟通时使用的，所以能够减少订单也将是有益的。

包括最小批量单元测试的完整代码可以在:[https://github.com/Kritner/RussUnitTestSample](https://github.com/Kritner/RussUnitTestSample)找到

*原载于 2015 年 11 月 30 日*[*【kritner.blogspot.com】*](https://dev.to/kritner/getting-started-with-unit-testing-and-moq-52jb)*。**