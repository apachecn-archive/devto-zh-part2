# 每个测试一个断言——这是怎么回事？

> 原文：<https://dev.to/donut87/one-assert-per-test---what-is-that-about-4l75>

我团队以外的同事要求我和其他同事审阅合并请求。尽管我们试图磨练我们的评审技巧，但我们邀请特定团队之外的同事进行评审的情况并不常见。这是其中一个案例，我很高兴有机会发表我的意见。

当这位同事过来询问我的第一印象时，我正在进行评估。我简单地查看了一下资料来源，所以我的回答也很简单:“我不喜欢你的单元测试”。当他进一步问我为什么不喜欢他们时，我的回答是“因为他们是狗屎。”我知道这听起来很刺耳——我不是一个刻薄的人，但我很直率，而且倾向于夸大其词。谢天谢地，我的同事知道这一点，我们同意等我有足够的时间弄清楚为什么我不喜欢这些测试时再谈。

我坐下来，花了一些时间来找出困扰我的问题，我想与你分享。

# 每个测试不止一个断言

当我查看代码时，有一件事对我来说很清楚，那就是在每个单元测试中使用了不止一个断言。我不喜欢没有理由的规则，所以我必须仔细看看，以确保这不是直觉。这是一个广泛讨论的话题，但我想补充我的两点意见。

似乎每次互联网讨论这个话题时都会出现的一个原因是:

> 如果第一个断言失败，您不会看到第二个断言是否有问题。

[看到这里](http://programmaticallyspeaking.com/one-assertion-per-test-please.html)

虽然这个论点是正确的，但我发现它很弱，因此倾向于不使用它。我不认为这是个问题。我对此有不同的看法，我举了两个例子来说明这一点。为了隐私和简洁，我简化了测试，但保留了本质。

```
public void testIfRegistryGetsValuesCorrectly() {
  Registry a = new Registry();
  // Imagine some setup code here.
  // originally there was a before-each method

  assertThat(a.getEntryByKey("RandOMstRIng"), equals(a.getEntryByKey("randomstring")));
  assertThat(a.getEntryByKey("   rand   oms.tri ng"), equals(a.getEntryByKey("randomstring")));
  assertThat(a.getEntryByKey("randomst37ring"), equals(a.getEntryByKey("randomstring")));
} 
```

测试工作正常，符合要求。在询问值之前，去掉键的所有非字符并使其小写。一切似乎都很好。
当您接触到被测试的方法并打破“证明注册获取值正确”测试时，问题就变得很清楚了。

作为一名致力于实现的开发人员，您不会再发现什么地方不工作了。至少第一眼不会。
你会明白测试破了，这是名字告诉你的。

我建议将测试重构为下面的代码。

```
public void testIfRegistryIgnoresCases() {
  Registry a = new Registry();
  // Imagine some setup code here.
  // originally there was a before-each method

  assertThat(a.getEntryByKey("RandOMstRIng"), equals(a.getEntryByKey("randomstring")));
}

public void testIfRegistryIgnoresNonCharacters() {
  Registry a = new Registry();
  // Imagine some setup code here.
  // originally there was a before-each method

  assertThat(a.getEntryByKey(" 3  ra8nd   oms.tri ng"), equals(a.getEntryByKey("randomstring")));
} 
```

有了这些改变，你很快就会明白有问题的行为。现在允许点号了？你知道该怎么做:

1.  你破坏了一个定义明确的测试
2.  你根据新的需求修正测试
3.  你*重命名*测试

# 明确命名

在继续之前，我想举第二个例子来说明。提醒你:我简化了测试，但保留了本质。

```
public void testConstructorOneWorksCorrectly() {
  SpecialObjectClass a = new SpecialObjectClass("someID", "attribute1", "attribute2");

  assertThat(a.getID(), equals("someID"));
  assertThat(a.getFirstAttribute(), equals("attribute1"));
  assertThat(a.getSecondAttribute(), equals("attribute2"));

}

public void testConstructorTwoWorksCorrectly() {
  SpecialObjectClass a = new SpecialObjectClass("someID", "attribute1:attribute2");

  assertThat(a.getID(), equals("someID"));
  assertThat(a.getFirstAttribute(), equals("attribute1"));
  assertThat(a.getSecondAttribute(), equals("attribute2"));
}

public void testConstructorTwoWorksCorrectlyWithoutSecondParameter() {
  SpecialObjectClass b = new SpecialObjectClass("someID", "attribute1");

  assertThat(b.getID(), equals("someID"));
  assertThat(b.getFirstAttribute(), equals("attribute1"));
  assertThat(b.getSecondAttribute(), equals("attribute1"));

} 
```

上面例子的问题类似，但不明显。复杂的逻辑在于第二个和可选的第三个参数。ID 参数(“someID”)是微不足道的，在每个测试用例中，它会分散更多的注意力。如果 ID 参数的处理出现问题，三个单元测试也会因为同样的原因而中断。

对此我有两个问题:

1.  它违背了单元测试背后的思想，即测试单个孤立的单元
2.  这让糟糕的开发人员在将来更改测试过的代码时感到困惑。(几个月后他也可能是作者本人)

我建议重构为以下形式:

```
public void testConstructorSetsID() {
  SpecialObjectClass a = new SpecialObjectClass("someID", "attribute1", "attribute2");

  assertThat(a.getID(), equals("someID"));
}

public void testConstructorSetsBothAttributes() {
  SpecialObjectClass a = new SpecialObjectClass("someID", "attribute1", "attribute2");

  assertThat(a.getFirstAttribute(), equals("attribute1"));
  assertThat(a.getSecondAttribute(), equals("attribute2"));
}

public void testConstructorTwoSplitsAttributesAtTheColon() {
  SpecialObjectClass a = new SpecialObjectClass("someID", "attribute1:attribute2");

  assertThat(a.getFirstAttribute(), equals("attribute1"));
  assertThat(a.getSecondAttribute(), equals("attribute2"));
}

public void testConstructorTwoSetsSecondAttributeSameValueAsTheFirstIfNotGiven() {
  SpecialObjectClass b = new SpecialObjectClass("someID", "attribute1");

  assertThat(b.getFirstAttribute(), equals("attribute1"));
  assertThat(b.getSecondAttribute(), equals("attribute1"));

} 
```

我在这里的命名可能不完美，但它表达了我的意思。正如你可能已经注意到的，在*所有*单元测试中出现的*一个*字现在不见了:“正确地说是*”*

 *这是我小小的顿悟。作为一个词，“正确地”不应该成为测试用例名称的一部分。它根本不提供任何信息。

在发现了我对测试的两点不满后，我为自己想出了一个模式:

*   具有多个断言的测试用例->注意这个危险的词("*正确地*")，并相应地改变测试用例
*   标题中的危险词->查看断言，找出这个测试做什么，并更改名称

这就是我今天学到的:有些词不应该出现在任何测试用例的名称中，那就是:“正确地”。

可能还有更多。小心他们！

附注:我在这篇文章中提到的同事被要求校对文章。除了在正确的英语方面帮助很多，他还加入了自己的想法。“作为开发人员，我们经常考虑命名，有时我们似乎错过了最明显的点——或者我们倾向于变得懒惰。”*