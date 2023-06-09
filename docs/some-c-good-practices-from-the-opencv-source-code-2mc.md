# OpenCV 源代码中的一些 C++良好实践

> 原文：<https://dev.to/codergears/some-c-good-practices-from-the-opencv-source-code-2mc>

[OpenCV](http://opencv.org/) (开源计算机视觉)是一个主要针对实时计算机视觉的编程函数库，由下诺夫哥罗德的英特尔俄罗斯研究中心开发。该库是跨平台的。它主要关注实时图像处理。

OpenCV 被广泛使用，在世界各地被采用，对于最终用户来说，它非常成熟和强大，对于开发人员来说，它被很好地实现和设计。OpenCV 开发人员使用非常基本的原理，这使得理解和维护非常简单。

让我们来发现一些 OpenCV 设计选择:

## 模块化

**1-基于库的架构**

基于库的架构使得所提供功能的重用和集成更加灵活，并且更容易集成到其他项目中。此外，基于库的架构鼓励干净的 API 和分离。因此让开发人员更容易理解，因为他们只需要理解大图的一小部分。

OpenCV 采用这种方法并定义了许多库，每个库都有特定的职责，并且所有库都使用 opencv_core 库。

[![Alt text of image](img/d0d51663b1e251c331d46b04560903a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHXEwbCw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv1.png)

**2-通过名称空间模块化**

OpenCV 广泛使用名称空间来模块化其代码库，以下是 opencv_core 项目的名称空间示例:

[![Alt text of image](img/93bd2c99efaa4e4e3ee57ba1d0e14b95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UECHqyIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv2.png)

OpenCV 使用“按特性命名空间”的方法。逐个特性的命名空间使用命名空间来反映特性集。它将与单个特性相关的所有项目(且仅与该特性相关)放入单个名称空间中。这导致名称空间具有高内聚和高模块化，并且名称空间之间的耦合最小。紧密配合的项目会并排放置。

在 OpenCV 中，使用名称空间有三个主要原因:

*   模块化库。
*   隐藏细节，比如对于“cv::detail”命名空间，如果我们想告诉库用户他不需要在这个命名空间中直接使用类型，这种方法会非常有趣。对于 C#来说,“internal”关键字完成了这项工作，但是在 C++中，没有办法对库用户隐藏公共类型。
*   匿名命名空间:没有名称的命名空间。它避免了使全局成为静态变量。您创建的“匿名”命名空间只能在您创建它的文件中访问。

**将数据模型定义为 POD 类型**

每个项目都有其数据模型，建议将这些数据定义为 [POD](http://en.wikipedia.org/wiki/Plain_old_data_structure) 类型。

让我们在 OpenCV 代码库中搜索没有方法而只有字段的结构。为此，CQLinq 将用于查询代码库。

[![Alt text of image](img/3a57fe11e27fc54af99a00c7206e318c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_hi1cYap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv3.png)

这个查询的结果涉及 OpenCV 项目中定义的 25%的类型。OpenCV 在只有字段的结构中定义了几乎所有的数据模型。

**避免多重继承**

使用多重继承可能会使设计变得复杂，调试器可能会遇到困难，因此许多 C++专家都不建议这样做。

让我们在 OpenCV 代码库中搜索哪些类继承了多个具体的基类。

[![Alt text of image](img/671fd825d5c8cd7c48b9a1d4696e242e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bIK1x2br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv4.png)

只有测试项目中的一些类使用多重继承，这个概念在整个 OpenCV 代码库中是避免的。

**避免定义复杂的函数**

存在许多度量来检测复杂函数，NBLinesOfCode、参数数目和局部变量数目是基本的度量。

还有其他有趣的度量来检测复杂函数:

*   圈复杂度是一个流行的过程化软件度量，等于一个过程中可以做出的决策的数量。
*   嵌套深度是在方法上定义的度量，相对于方法体中嵌套更深的范围的最大深度。
*   最大嵌套循环等于函数中循环嵌套的最大级别。这些指标的最大容许值更多地取决于团队的选择，没有标准值。

让我们在 OpenCV 代码库中搜索被认为复杂的方法。

[![Alt text of image](img/5e160955fcf04881fa9c8e57fce7df2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JqIMaUjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv5.png)

只有 1%可以被重构以最小化它们的复杂性。

## 耦合

低耦合是可取的，因为应用程序的一个方面的变化在整个应用程序中需要较少的变化。从长远来看，这可以减少与修改应用程序和向应用程序添加新功能相关的大量时间、精力和成本。

低耦合可以通过使用抽象类来实现。使用它们有三个主要好处:

*   抽象类提供了一种定义促进重用的契约的方法。如果一个对象实现了一个抽象类，那么这个对象就符合一个标准。使用另一个对象的对象称为消费者。抽象类是对象和它的消费者之间的契约。
*   抽象类还提供了一个抽象层次，使得程序更容易理解。抽象类允许开发人员开始谈论代码行为的一般方式，而不必进入许多详细的细节。
*   抽象类加强了组件之间的低耦合，这使得保护抽象类消费者免受实现抽象类的类中的任何实现变化的影响变得容易。

让我们搜索 OpenCV 定义的所有抽象类:

[![Alt text of image](img/7462426500bc328675e74570e8e0a16e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BhTcr59Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv6.png)

如果我们的主要目标是实施低耦合，那么在使用抽象类时会有一个常见的错误，那就是扼杀使用它们的实用性。使用具体的类而不是抽象的类，为了更好地解释这个问题，让我们举下面的例子:

类 A 实现了包含 calculate()方法的抽象类 IA，消费者类 C 是这样实现的

公丙类
{
…。
公:
虚空算()
{
……..
m_a- >计算()；
…。
}
a* m _ A；
}；
类 C 没有引用抽象的类 IA，它引用了类 A，在这种情况下，我们失去了低耦合的好处，这种实现有两个主要缺点:

*   如果我们决定使用 IA 的另一种实现，我们必须更改 C 类的代码。
*   如果将一些 IA 中不存在的方法添加到 A 中，并且 C 使用它们，我们也会失去使用接口的契约好处。

C#为语言引入了[显式接口实现](http://msdn.microsoft.com/en-us/library/aa288461(v=vs.71).aspx)功能，以确保 IA 中的方法永远不会从对具体类的引用中调用，而只会从对接口的引用中调用。这种技术对于保护开发人员不失去使用接口的好处非常有用。

## 衔接

单一责任原则规定一个类不应该有一个以上的改变理由。这样的类被称为内聚类。高 LCOM 值通常指出一个内聚性差的类。有几个 LCOM 指标。LCOM 的取值范围为[0-1]。LCOM 房协(房协代表汉德森-卖方)的取值范围为[0-2]。高于 1 的 LCOM HS 值应被视为警报。以下是计算 LCOM 指标的方法:

LCOM = 1-(总和(MF)/M * F)
LCOM HS =(M–总和(MF)/F)(M-1)

其中:

*   m 是类中方法的数量(静态和实例方法都计算在内，它还包括构造函数、属性 getter/setter、事件添加/移除方法)。
*   f 是类中实例字段的数量。
*   MF 是访问特定实例字段的类的方法的数量。Sum(MF)是该类的 MF 总体实例字段的总和。

这些公式背后基本思想可以陈述如下:如果一个类的所有方法都使用它的所有方法，使用它的所有实例字段，则该类是完全内聚的，这意味着 sum(MF)=M*F，然后 LCOM = 0，LCOMHS = 0。

LCOMHS 值高于 1 应视为报警。

[![Alt text of image](img/742ba4c7749e3e6cf91e1028d72641a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5_WzYl7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.codergears.com/Blog/wp-content/uploads/opencv7.png)

只有少数类型不具有内聚性。

## 结论

如果你看一看 OpenCV 源代码，你会惊讶于它实现的简单，没有使用先进的设计概念，没有过度工程化，只是应用了一些基本原则。