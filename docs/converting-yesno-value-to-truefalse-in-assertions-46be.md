# 将断言中的“是”/“否”值转换为真/假

> 原文：<https://dev.to/klamping/converting-yesno-value-to-truefalse-in-assertions-46be>

我最近写了一个测试，需要比较“是/否”显示值和真/假数据库值。

<figure>

[![](../Images/8c5ef078c625bf60073c8484eed40b3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jbSsTnCO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tansx2adnfozdhausxj4.png)

<figcaption>Example HTML table mapping to the Database Schema.</figcaption>

</figure>

这不是一个完全琐碎的任务，所以我决定分享我想到的解决方案。

这个例子使用了 [Chai 的‘expect’断言语法](https://www.chaijs.com/api/bdd/)。

这是我最初的尝试，**它是不完整的，原因我将在下面解释。**

```
const displayedValue = browser.getText('.is-allowed'); // returns 'Yes'

// pretend 'db' is some magic database call that's all wired up
const databaseValue = db.getValue('isAllowed'); // returns true

expect(displayedValue === 'Yes').to.equal(databaseValue, 'Displayed value matches database') 
```

这很好，但是如果`databaseValue`是`false`，就不会在显示值中发现问题。

例如，如果我们的`isAllowed`数据库值是`false`，那么显示值中除了‘Yes’之外的任何值都将导致通过测试(例如，拼写错误的无效值，如‘Yes’，仍然会通过)。

相反，这里有一个更有弹性的变化，我们根据数据库值来验证文本是“活动”还是“非活动”:

```
const displayedValue = browser.getText('.status'); // returns 'Inactive'

const databaseValue = db.getValue('isActive'); // returns false

expect(displayedValue).to.equal(isActive ? 'Active' : 'Inactive'); 
```

该版本有助于锁定可能显示的允许值。只有当文本与任一数据库值完全匹配时，它才会通过。

注意，我们也不需要添加断言消息，因为我们传入的值就是我们需要的所有信息。

如果你对结尾的三进制陈述不熟悉，可以看看[这篇颇有见地的文章](https://scotch.io/tutorials/understand-the-javascript-ternary-operator-like-abc#toc-ternary-operator)。

乔恩·泰森在 [Unsplash](https://unsplash.com/search/photos/yes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的标题照片