# 科特林密封类:如何用真理测试它们

> 原文：<https://dev.to/dbottillo/kotlin-sealed-classes-how-to-test-them-with-truth-5hjj>

在我的 Deliveroo 团队中，我们最近引入了用于单元测试的真理库，我们对此非常满意:我们喜欢它的语法、可读性和可扩展性。例如在处理密封类时，单元测试并不是最好的；让我们假设有一个非常简单的密封类:

```
sealed class Food {
    class Pizza(val slices: Int): Food()
    class Sushi(val pieces: Int): Food()
} 
```

在一个典型的单元测试中，如果你想测试一些食物是比萨饼并且有 4 片，你可以这样写:

```
@Test
fun `food is pizza and have 4 slices`() {
    val underTest = getFood()

    assertThat(underTest is Food.Pizza).isTrue()
    assertThat((underTest as Food.Pizza).slices).isEqualTo(4)
} 
```

那不优雅！使用 is 和 as 会使代码变得难看，可读性更差，而且在 Kotlin 契约稳定之前，我们无法做太多改进，除非我们创建一个自定义主题。这个想法是扩展 Truth 以获得更流畅的语法:

```
assertThat(underTest).isPizza(slices = 4) 
```

那就更有道理了！让我们看看如何创建这个自定义断言，Truth 需要两个组件:一个主题和一个主题工厂。主题是断言所在的地方，工厂用于公开您的自定义主题:

```
class FoodSubject(metadata: FailureMetadata, actual: Food) : Subject<FoodSubject, Food>(metadata, actual) {}
fun food(): Subject.Factory<FoodSubject, Food> {
    return Subject.Factory<FoodSubject, Food> { metaData, target -> FoodSubject(metaData, target) }
} 
```

主题的构造函数是您接收测试实际目标:食物的地方，在主题内部，您可以定义自己的断言:

```
fun isPizza(slices:Int) {
    if (actual() !is Food.Pizza) {
        failWithoutActual(Fact.simpleFact("expected to be a Pizza!"))
    } 
    val target = actual() as Food.Pizza
    if (target.slices != slices) {
        failWithActual(Fact.simpleFact("expected to be have '$slices' slices but have '${target.slices}' instead"))
    }
} 
```

actual()方法返回 food test 对象，您可以在其上检查您感兴趣的任何内容，在本例中，该方法测试类型是否为 Pizza 以及切片数量是否相同。要在单元测试中使用此方法，您可以这样做:

```
@Test
fun `food is pizza and have 4 slices`() {
    val underTest = generateFood()

    assertWithMessage("food should be pizza with 4 slices")
            .about(food())
            .that(underTest)
            .isPizza(slices = 4)
} 
```

这非常接近我们一开始想要的！但是还有一个小的改进我们可以做到。about(food())。(欠测试)可以提取到主题定义中的实用方法:

```
fun assertThat(@Nullable food: Food): FoodSubject {
    return assertAbout(food()).that(food)
} 
```

这会将测试更改为:

```
@Test
fun `food is pizza and have 4 slices`() {
    val underTest = generateFood()
    assertThat(underTest).isPizza(slices = 4)
} 
```

这正是我们想要的:)

你可以找到 GitHub 的完整例子:[https://github.com/dbottillo/Blog/tree/truth_assertion](https://github.com/dbottillo/Blog/tree/truth_assertion)

测试愉快！