# 测试时不要再欺骗自己

> 原文：<https://dev.to/klamping/stop-lying-to-yourself-when-testing-42bk>

不久前,《纽约时报》做了一个小测验，题目很简单:找出一系列数字背后的数学规律。

作为一名读者，你可以输入自己的数字，看看它们是否通过了你对规则的猜测。

在我看来，这与我们进行单元测试的方式非常相似。提供输入，验证输出。这里，您提供输入(数字)，输出是数字序列是否匹配规则。

你可以想象一下对它的单元测试:

```
function checkRule (numbers) {
    // some magical formula we’re supposed to figure out
};

describe('my mathematical equation', function () {
    it('should pass when doubling each number', function () {
        expect(checkRule([1,2,4])).toBeTrue;
        expect(checkRule([2,4,8])).toBeTrue;
        expect(checkRule([3,6,12])).toBeTrue;
        expect(checkRule([5,10,20])).toBeTrue;
    });
}) 
```

Enter fullscreen mode Exit fullscreen mode

查看这段代码，很容易假设规则是“每个数字都应该是前一个数字的两倍”。毕竟，我们的四个断言通过了，所以我们得到了绿色测试！

这个小测验的诀窍在于数学等式非常简单:每个数字都必须大于前一个数字。

这条宽泛的规则意味着人们很容易假设他们的复杂解决方案是正确的。他们为验证规则而给出的每一个输入都返回 true，所以它一定是正确的。

然而，正如这篇文章指出的，这种测试方法有一个缺陷:

> 值得注意的是，到目前为止，玩这个游戏的人中有 78%在没有听到一个“不”字的情况下就猜到了答案。只有 9%的人听到了至少三个“不”——尽管被告知“不”字不会受到惩罚或付出代价，但每个人听到“不”字时都会感到小小的失望

文章将此归因于部分适用的“[确认偏差](http://rationalwiki.org/wiki/Confirmation_bias)”。但是一个更好的描述是[一个不太为人所知的偏差](http://rationalwiki.org/wiki/List_of_cognitive_biases)叫做一致性偏差。(在我听说这篇关于[怀疑论者宇宙指南](http://www.theskepticsguide.org/)的文章之前，我并不知道这件事)。

这种偏见是“倾向于只通过直接测试来测试假设，而不是测试可能的替代假设。”

在我们上面的测试中，我们只检查阳性结果。我们从来不会问“如果我提供了与规则相矛盾的数据，这是否会失败？”

每个单元测试套件都应该有否定检查。一个简单的`expect(passesRule([2,4,6])).toNotBeTrue;`将会破坏我们的测试，向我们显示尽管 6 不是 4 的两倍，规则仍然通过。

再次，从文章中:

> 当你想测试一个理论时，不要只是寻找证明它的例子。当你在考虑一个计划的时候，仔细想想它可能会出什么问题。

第二部分对测试来说尤其如此。

很容易假设，因为您的测试通过了，所以代码和测试按预期工作。但是我们必须记住埃德格·迪克斯特拉很久以前说过的话:

> 测试显示了缺陷的存在，而不是不存在。

下次测试代码时，考虑确认和一致性偏差。

请记住“快速失败”这句话。证明你的代码确实是它所说的那样，并在编码时始终保持怀疑的心态。

不要等到为时已晚才明白残酷的事实。

用理查德·费曼的话说:

> 第一个原则是你不能欺骗自己——而你是最容易被欺骗的人。

关于测验本身的更多分析，以及对一致性偏差的思考，请查看以下讨论:

*   [SGU 成绩单](http://www.sgutranscripts.org/wiki/SGU_Episode_361#Congruence_Bias_.2853:19.29)
*   [关于此事的神经学博文](http://theness.com/neurologicablog/index.php/a-quick-logic-lesson/)

汉斯-彼得·高斯特在 [Unsplash](https://unsplash.com/) 拍摄的标题照片