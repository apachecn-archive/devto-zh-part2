# 深度学习所需的矩阵演算(来自特伦斯·帕尔和杰里米的一篇论文的注释…

> 原文：<https://dev.to/rohitpatil5/the-matrix-calculus-you-need-for-deep-learning-notes-from-a-paper-by-terence-parr-and-jeremy-59n7>

### 深度学习需要的矩阵演算(摘自[特伦斯·帕尔](http://parrt.cs.usfca.edu/)和[杰瑞米·霍华德](http://www.fast.ai/about/#jeremy)的一篇论文)

[![](../Images/f53a6641925a01af478d14bcef48745c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zMKIKCQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Al_ZFM3-vJMBVcZacHwCX-Q.jpeg)

#### **目录**

*   [复习:标量导数规则](https://medium.com/p/4f4263b7bb8#48c0)
*   [向量微积分和偏导数介绍](https://medium.com/p/4f4263b7bb8#d5f1)
*   [矩阵演算](https://medium.com/p/4f4263b7bb8#3c66)
*   [雅可比矩阵的推广](https://medium.com/p/4f4263b7bb8#efec)
*   [向量元素式二元算子的导数](https://medium.com/p/4f4263b7bb8#59c5)
*   [向量和缩减](https://medium.com/p/4f4263b7bb8#641f)
*   [连锁规则](https://medium.com/p/4f4263b7bb8#ada3)
*   [资源](https://medium.com/p/4f4263b7bb8#3788)

Jeremy 的课程展示了如何通过利用现代深度学习库中内置的自动微分，仅用最低水平的标量微积分就成为世界级的深度学习实践者。但是，如果你真的想真正了解这些库的内部发生了什么，并搜索讨论模型训练技术最新进展的学术论文，你需要了解矩阵微积分领域的某些知识。

### 复习:标量导数规则

希望你们记得一些主要的标量导数规则。如果你在这方面的记忆有点模糊，看看[可汗学院关于标量导数规则的视频](https://www.khanacademy.org/math/ap-calculus-ab/ab-derivative-rules)。

[![](../Images/9743c79331076a87973f80da8fc38ccb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xrQOMWtU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZR50K2cDpl1um4S-aOeWQw.png) 

<figcaption>衍生品的基本规则</figcaption>

三角学、指数学等还有其他规则。，可以在[可汗学院微分学课程](https://www.khanacademy.org/math/differential-calculus)找到。

### 向量微积分与偏导数导论

神经网络各层不是单一参数的单一函数，***【f(x)***。所以，让我们继续讨论多参数函数，例如 ***f(x，y)*** 。比如 xy 的导数是多少(即 x 和 y 的乘积)？

嗯，这取决于我们是在改变 *x* 还是 *y* 。我们一次计算一个变量(参数)的导数，为这个双参数函数给出两个不同的*偏导数*(一个用于 *x* ，一个用于 *y* )。偏导数运算符不是使用运算符 d/dx，而是使用 **∂/ ∂x** (一个程式化的 *d* 而不是希腊字母δ)。所以 **∂(xy)/ ∂x** 和 **∂(xy)/ ∂y** 是 *xy* 的偏导数；通常，这些被称为*分音*。

关于 *x* 的偏导数只是通常的标量导数，简单地将方程中的任何其他变量视为常数。考虑函数 f(x，y) = 3x y，关于 *x* 的偏导数写成 **∂(** 3x y* *)/ ∂x.* *从 **∂/ ∂x** 的角度看有三个常数:3，2， *y* 。因此，∂(3x y)/ ∂x = 3y∂(x )/ ∂x = 3y(2x) = 6xy。相对于 *y* 的偏导数将 *x* 视为常数，我们得到∂(3x y)/ ∂y = 3x。你可以在[汗学院关于偏音的视频](https://www.khanacademy.org/math/multivariable-calculus/multivariable-derivatives/partial-derivative-and-gradient-articles/a/introduction-to-partial-derivatives)中了解更多。

所以根据上面的例子，如果 f(x，y) = 3x y，那么

[![](../Images/92bc02a1f06fc2734c287f679ada8e16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RGg7FVdZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/657/1%2A8a9yL0FQmO70v-Juo5c3Bg.png)

<figcaption>*f(x，y)的渐变*</figcaption>

所以 f(x，y)的 ***梯度只是它的偏导数的一个向量。*T3】**

### 矩阵演算

当我们从一个函数的导数转移到多个函数的导数时，我们从向量微积分的世界转移到矩阵微积分。让我们再来一个函数 g(x，y) = 2x + y⁸.所以 g(x，y)的梯度是

[![](../Images/b804f0cc5db0952fe5a63c4b1258e294.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBc7kWkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/194/1%2AIUpOnL8zNi_fPcDKcFP3BQ.png)

梯度向量组织了特定标量函数的所有偏导数。如果我们有两个函数，我们也可以通过堆叠梯度将它们的梯度组织成一个矩阵。当我们这样做时，我们得到了 ***雅可比矩阵*** (或者仅仅是 ***雅可比矩阵*** )，其中梯度是行:

[![](../Images/26bdbd4e5ae1bf0f85475d9426f67477.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_DB94Oyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/549/1%2A7xKV9D7qXX44GQQbvYlBgA.png) 

<figcaption>[雅可比的分子布局](https://en.wikipedia.org/wiki/Matrix_calculus#Layout_conventions)</figcaption>

### 雅可比的概括

为了更一般地定义雅可比矩阵，我们把多个参数组合成一个向量自变量: *f* ( *x，y，z* ) = > *f* ( **x** )。粗体小写字母如 **x** 是向量，斜体小写字母如 *x* 是标量。 *xi* 是向量 **x** 的第 I 个元素，用斜体表示是因为单个向量元素是标量。我们还必须为向量 **x** 定义一个方向。我们假设所有向量都是垂直的，默认大小为 *n* X 1:

[![](../Images/0d6fea627de5b2094fb5e0c67d47380b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qIod3W29--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/127/1%2ATnlD_RDMVQhGizRLp3jd-Q.png)

对于多个标量值函数，我们可以将它们组合成一个向量，就像我们对参数所做的那样。设 **y = f(x) b** e 一个由 *m* 个标量值函数组成的向量，每个函数取一个长度为 n= | **x** 的向量 **x** ，其中| **x** |是 **x** 中元素的基数(计数)。 **f** 中的每个 *fi* 函数都返回一个标量，就像上一节一样

[![](../Images/07d296993ffea2ce17fdf76f0354e699.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ECo0ePJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/177/1%2AwyqevFkocbibjZjs1E3MOA.png)

> 不过一般来说，雅可比矩阵是所有***m***X***n***可能偏导数的集合( *m* 行和 *n* 列)，是 *m* 梯度相对于 **x** 的叠加:

[![](../Images/45a63124009a06f8e28e5dccd863c7a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Er8rgAs6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/762/1%2Af75-0xoIgirN-kkL2d_vEg.png)

### 向量元素式二元算子的导数

“按元素的二元运算”简单地说就是将一个运算符应用于每个向量的第一项以获得输出的第一项，然后应用于输入的第二项以获得输出的第二项，依此类推。我们可以用符号 y= f(w) O g(x)来概括元素级的二元运算，其中***m***=***n***= |***y*|**| = |**|*w***| = |***x***|。O 符号代表任何元素操作符(如+)，而不是 O 函数复合操作符。

[![](../Images/d5ed11d9535fd0cbaa5a6622e1906c56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gSwEkR-2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6SVHGcQcijq6aEYUoC_yZw.png)

这是一个很复杂的问题，但幸运的是，雅可比矩阵通常是一个对角矩阵，一个除了对角线以外处处为零的矩阵。

### 向量求和还原

对向量的元素求和是深度学习中的一个重要操作，例如网络损失函数，但我们也可以将其作为一种方法来简化计算向量点积的导数以及其他将向量归约为标量的操作。

设**y = sum(f(x))**=σ***fi***(**x**)。注意，我们在这里很小心地将参数留为向量 **x** ，因为每个函数 *fi* 都可以使用向量中的所有值，而不仅仅是 *xi* 。总和超过函数的结果**而不是参数的结果**。向量求和的梯度(**1**X**T19】nT21】雅可比)为:**

[![](../Images/8dcffcf4fb4c2fe0a5acf56e15bee652.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nPP6mBzr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A-MEhl1gPbjiIjXyla9C3wQ.png)

### 连锁规则

我们不能仅仅使用基本的矩阵计算规则来计算非常复杂的函数的偏导数。我们在这里的部分目标是清楚地定义和命名三个不同的链规则，并指出它们适用于哪种情况。

链规则在概念上是一种分而治之的策略(就像快速排序一样),它将复杂的表达式分解成更容易计算导数的子表达式。它的强大之处在于，我们可以孤立地处理每个简单的子表达式，但仍然可以组合中间结果来获得正确的整体结果。

当我们需要由嵌套子表达式组成的表达式的导数时，链规则就发挥作用了。例如，当面对像 d(sin(x ))/dx 这样的表达式时，我们需要链式法则。

*   [**单变量链式规则**](http://m.wolframalpha.com/input/?i=chain+rule) ** :-**链式规则通常以嵌套函数的形式定义，如***y***=***f*(*u*)**其中***u*=*g*(*x*)so*y*=*f*(**

[![](../Images/78547454b65411394c8668bbf8d16ff7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cNfWIG2n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/160/1%2Au3k7gS3vAPEXukvkWmyDzg.png) 

<figcaption>单变量链式法则的制定</figcaption>

要部署单变量链规则，请执行以下步骤:

1.  为嵌套的子表达式以及二元和一元运算符的子表达式引入中间变量；比如，X 是二进制，***【sin(X)】***等三角函数通常是一元的，因为有单个操作数。这一步将所有方程标准化为单个运算符或函数应用。
2.  计算中间变量相对于其参数的导数。
3.  将中间变量的所有导数相乘，得到总的结果。
4.  如果导数方程中引用了中间变量，则将其替换回。

*   **单变量全导数链规则:-** 全导数假设所有变量潜在地相互依存，而偏导数假设除了 *x* 之外的所有变量都是常数。

[![](../Images/cdf932cb7672409995b2337df91b97c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uJDjhT8v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/905/1%2ANxfy9U1Eh7_skLznwc3U-w.png)

当所有中间变量都是单个变量的函数时，这种考虑总导数的链式法则退化为单变量链式法则。

> 关于网络术语的警告。不幸的是，本节给出的基于全微分的链式法则，在微积分讨论中被普遍称为“多变量链式法则”，这是非常误导人的！只有中间变量是多元函数。

*   **向量链规则:-** 函数和单参数向量的向量链规则反映了单变量链规则。

如果***y*=*f*(*g*(*x*)**和 **x** 是矢量**。**向量 **y** 相对于标量 *x* 的导数是一个垂直向量，其元素使用单变量全导数链规则计算。

[![](../Images/8d57dbec3323b9c36e4ff7d2835e897a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CBYKWzVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/412/1%2AjtPtLuhQTBVK_0Be5rIpXQ.png)

目标是将上述标量操作的向量转换为向量操作。所以上述 RHS 矩阵也可以实现为向量乘法的乘积。

[![](../Images/ec95111176c4adbf38d68c9145d36606.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ewqf-cau--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/317/1%2AAIrI5dTFm_X_ybGL0UqayA.png)

这意味着雅可比矩阵是另外两个雅可比矩阵的乘积。为了使这个公式适用于多个参数或向量 **x** ，我们只需将等式中的 *x* 改为向量 **x** 。效果是 **∂g/ ∂x** 和产生的雅可比矩阵、 *∂f/ ∂x ** ，现在是矩阵而不是垂直向量。我们完整的*向量链规则*是:

[![](../Images/705ab13e47d30ca80aac3f7691409234.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nXHdyB7c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/223/1%2AsGJplnNYP7leOS-lNmRNFg.png)

> 请注意，矩阵乘法不可互换，**∂f/ ∂x)(∂g/ ∂x 的顺序很重要。

为了完整起见，这里有两个雅可比分量

[![](../Images/bd7258f4c9f0352a7c92bc8a995528bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vO-bS5EV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/666/1%2Ag5ph9kQOynRPVYfr1nYytA.png)

其中***m***= |**|*f***|**|*n***= |***x***|和*| k*= |**|*g***|。由此得到的雅可比矩阵是***m***X***n***。(一个***m***X***k***矩阵乘以一个***k***X**_ n _**矩阵)。

我们可以进一步简化，因为对于许多应用来说，雅可比矩阵是正方形的(***【m】***=***n***)，非对角线的项为零。

### 资源

1.原[纸](http://parrt.cs.usfca.edu/doc/matrix-calculus/index.html#sec3)。

1.  有一些在线工具可以帮助您区分矩阵:

*   [Wolfram Alpha](http://www.wolframalpha.com/input/?i=D%5B%7Bx%5E2,+x%5E3%7D.%7B%7B1,2%7D,%7B3,4%7D%7D.%7Bx%5E2,+x%5E3%7D,+x%5D)
*   [矩阵微积分微分器。](http://www.matrixcalculus.org/)

1.  更多[矩阵演算](https://atmos.washington.edu/~dennis/MatrixCalculus.pdf)。

[![](../Images/a34f9d87f995e8c77eb9959158d5b93e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IZl3bwWV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/201/1%2AxhCj5o5sYa_8VBUw_mR_Xg.jpeg)