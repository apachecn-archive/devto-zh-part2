# 单元测试站点核心计算字段

> 原文：<https://dev.to/jermdavis/unit-testing-sitecore-computed-fields-3opg>

今天很快。最近，我正在为 Sitecore 计算索引字段编写一些代码，我花了比我认为应该花在如何用 FakeDB 编写单元测试以验证代码工作上更多的精力。如果你不想花时间去寻找，答案很简单:

当您声明一个计算字段时，您实现的关键方法如下所示:

```
public override object ComputeFieldValue(IIndexable indexable)
{
} 
```

但是当您使用 FakeDb 实现一个测试时，您处理的是`Item`对象:

```
[TestMethod]
public void ClassUnderTest_Description_OfTheTestCase()
{
  using (var db = ComputedFieldsFakeDb.Create())
  {
    var itm = Sitecore.Context.Database.GetItem("/sitecore/content/TheItemToIndex");

    var sut = new ComputedFieldUnderTest();
    var result = sut.ComputeFieldValue( /* what goes here to do the mapping? */ );

    Assert.AreEqual("somevalue", result);
  }
} 
```

那么如何将`Item`映射到`IIndexable`呢？

事实证明这很简单——有一种类型叫做`SitecoreIndexableItem`,它包装一个`Item`,给你一个`IIndexable`:

```
var result = sut.ComputeFieldValue(new Sitecore.ContentSearch.SitecoreIndexableItem(itm)); 
```

简单。