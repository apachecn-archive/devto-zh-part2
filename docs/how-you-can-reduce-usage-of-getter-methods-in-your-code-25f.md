# 如何在代码中减少 getter 方法的使用

> 原文：<https://dev.to/aleksikauppila/how-you-can-reduce-usage-of-getter-methods-in-your-code-25f>

这是一个已经写了很多的主题，所以我不打算在这篇文章中重复所有这些论点。你可以在[这里](https://www.javaworld.com/article/2073723/core-java/why-getter-and-setter-methods-are-evil.html)和[这里](https://dev.to/scottshipp/avoid-getters-and-setters-whenever-possible-c8m)读到它们。相反，我将给出一些例子，说明如何至少在代码中减少 getter 方法的使用。

在我看来，不得不在对象中使用 get 方法是大量使用 setting 方法导致的症状。(你可以在这里看到我对 setters [的想法。)当我们遇到一个状态可能无效的对象时，我们必须提出问题来确定我们是否可以处理该对象。我们依赖于物体的属性，这使得我们的日常工作变得很困难。](https://dev.to/aleksikauppila/discarding-setters-4oe4)

**排序列表**

对雇员列表进行排序可能需要一个`getId()`方法来确保只有唯一的雇员。解决方法:写一个`equals()`方法。这允许您封装对一个类的两个实例进行唯一区分的问题。

**订购清单**

也许您需要根据某些标准来排列员工列表。或许是指名道姓？所以你需要一个`getLastName()`和`getFirstName()`方法。也许不是。如果有一个默认的方法来排序对象列表，那么就写一个`compareTo()`方法。这将把逻辑放在一个它自然属于的地方。

**确定一个物体是否是其他物体的一部分**

需要知道员工是否属于某个公司吗？为员工编写一个`belongsTo()`方法。这将允许您在 employee 对象中隐藏公司。

**表示视图中的对象**

您需要在视图中打印员工姓名吗？简单，写个`__toString()`方法。现在你不必将`$firstName`或`$lastName`暴露在公众面前。至少对于这种情况...

```
class Employee
{
    private $id;
    private $firstName;
    private $lastName;

    /** @var Company */
    private $company;
    //...
    public function equals(Employee $employee): bool
    {
        return $this->id === $employee->id;
    }

    public function compareTo(Employee $employee): int
    {
        if (($this->lastName <=> $employee->lastName) === 0) {
            return $this->firstName <=> $employee->firstName;
        }
        return $this->lastName <=> $employee->lastName;
    }

    public function belongsTo(Company $company): bool
    {
        return $this->company->equals($company);
    }

    public function __toString(): string
    {
        return sprintf("%s, %s", $this->lastName, $this->firstName);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

当然，在其他情况下，编写一个公共访问器可能会很有吸引力。是的，有时候解决问题最简单的方法就是编写一个 getter。但是在编写 get 方法之前，检查所有其他选项是非常重要的。如果您的客户端代码开始依赖一个对象提供的数据，那么您就会被它困住。

我介绍的这些方法都没有公开内部属性。使它们有用的另一件事是它们返回非常明确的值。`equals()`和`belongsTo()`返回一个布尔值。一种“是”或“不是”的回答。我发现提供显式方法来返回布尔值给常见问题非常有用，如`$contract->isTerminated()`或`$contract->isCurrentlyValid()`等。这比隐式查询终止日期和法令要好得多。当然，它允许我们做适当的数据隐藏。

顺便说一句，PHP 允许在同一个对象的类中询问私有属性。当我们想要保持我们的公共 API 干净和最小化时，这非常方便。如果你知道如何在其他语言中处理这个问题，请写下评论！此外，如果您知道一些概念可以帮助减少 getters 的其他用例，请告诉我！