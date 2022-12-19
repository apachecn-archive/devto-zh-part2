# 用 Java 制作一个不可变的对象

> 原文：<https://dev.to/monknomo/make-an-immutable-object---in-java-480n>

*[原载 www.gunnargissel.com](https://www.gunnargissel.com/make-an-immutable-object-in-java.html)*

不可变对象是不会改变的对象。你制造了他们，然后你不能改变他们。相反，如果你想改变一个不可变的对象，你必须克隆它，并且在你创建它的时候改变这个克隆。

一个 Java 不可变对象的所有字段必须是内部的，`private final`字段。它不能实现任何 setters。它需要一个为每个字段取值的构造函数。

不可变对象在多线程环境和流中很方便。依靠中流不变的对象是很棒的。由一个线程改变另一个线程的对象而引起的错误通常是微妙的，并且非常难以追踪。不可变对象阻止了这一类问题的发生。

你不必相信我的话——看看[专家](https://www.yegor256.com/2014/06/09/objects-should-be-immutable.html) [身边](http://www.codebetter.com/patricksmacchia/2008/01/13/immutable-types-understand-them-and-use-them/) [the](http://www.codebetter.com/patricksmacchia/2008/01/13/immutable-types-understand-them-and-use-them/) [web](https://stackoverflow.com/questions/214714/mutable-vs-immutable-objects) 怎么说。

## 目录

1.  [制作不可变对象](#making-an-immutable-object)
2.  [常见问题](#gotchas)
    1.  [原语](#primitive-gotchas)
    2.  [收藏](#collections-gotchas)
    3.  [数组](#array-gotchas)
    4.  [物体](#object-gotchas)
3.  [如何改变不可变对象](#how-to-change-an-immutable-object)
    1.  [建筑商放在哪里？](#where-to-put-builder-objects)
4.  [处理不可变对象中的坏数据](#handle-bad-data)
5.  [总结](#wrapup)

## 明白了！

[![a lonely tree on a rock](../Images/e8ea568cb666b916899713b153c52553.png "a lonely tree on a rock")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--inUMkMi_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/51q4QPRl.jpg)

列表、数组、映射、集合和其他非不可变的对象可能会令人惊讶。没有 setter 的`private final`对象被固定到它最初被赋值的对象上，但是该对象内部的值是不固定的(除非该对象是不可变的)。

这意味着你可能有一个`ImmutableShoppingList myShoppingList = new ImmutableShoppingList(new String[] {"apples","anchovies","pasta"})`，并期望购物清单上总是有“苹果”、“凤尾鱼”和“意大利面”。

有人可能会打电话把你的清单改成“糖果棒”、“凤尾鱼”和“意大利面”，这是不健康的，显然不是你想要的。

### 原语

好消息！原语是不可变的，所以你不需要做任何特别的事情。

### 收藏

好消息！ [java.util.Collections](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html) 提供了许多方便的方法，使得将集合转换为不可修改的集合变得轻而易举。

检查:

```
Collections.unmodifiableCollection
Collections.unmodifiableList
Collections.unmodifiableMap
Collections.unmodifiableNavigableMap
Collections.unmodifiableNavigableSet
Collections.unmodifiableSet
Collections.unmodifiableSortedMap
Collections.unmodifiableSortedSet 
```

Enter fullscreen mode Exit fullscreen mode

我建议您将字段存储为泛型集合(List，而不是 ArrayList)，并使其在构造函数中不可修改，如下所示:

```
public class ImmutableShoppingList {

    private final List<String> list;

    public ImmutableShoppingList(List<String> list){
        this.list = Collections.unmodifiableList(list);
    }

    public List<String> getList(){
        return list;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许您使用 IDE 代码生成来生成 getters，这很好，并且在一个地方包含了所有的输入修饰符，这也很好。

坏消息！如果在创建集合时保留了对该集合的引用，即使在内部将其存储为不可修改的集合，也仍然可以对其进行修改。这里有一个例子:

```
List<String> originalList = new ArrayList<>();
theList.add("apple");
ImmutableShoppingList blah = new ImmutableShoppingList(originalList);
originalList.add("candy bar"); 
```

Enter fullscreen mode Exit fullscreen mode

据称不可改变的购物清单从一个苹果开始，并在创建后添加了一个糖果条。对此我们能做些什么？

克隆列表！

```
public class ImmutableShoppingList {

    private final List<String> list;

    public ImmutableShoppingList(List<String> list){
        List<String> tmpListOfHolding = new ArrayList<>();
        tmpListOfHolding.addAll(list);
        this.list = Collections.unmodifiableList(tmpListOfHolding);
    }

    public String[] getList(){
        return (String[]) list.toArray();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们创建不可变对象时，我们深度克隆集合，这切断了到原始引用的连接。现在，当我们运行“潜入一个糖果条”的例子时，“糖果条”被添加到`originalList`，而不是`ImmutableShoppingList`。

### 阵列

坏消息！Java 没有任何方便的方法来防止数组被修改。最好的办法是隐藏原始数组并总是返回一个克隆，或者在底层实现中不使用数组，而是将集合对象转换为数组。

我更喜欢坚持使用集合，但是如果你必须在你的对象的 api 中有一个数组，这是我会采用的方法:

```
public class ImmutableShoppingList {

    private final List<String> list;

    public ImmutableShoppingList(String[] list){
        this.list = Collections.unmodifiableList(Arrays.asList(list));
    }

    public String[] getList(){
        return (String[]) list.toArray();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 物体

对象字段可能很简单。如果子对象也是不可变的，这是个好消息！你不必做任何特别的事情。

如果子对象不是不可变的，它们很像一个集合。您需要对它们进行深度克隆，否则原始引用可能会改变您认为不可变的数据。

通常，您最终会使用预先存在的可变对象，或者在您的代码库中，或者在库中。在这种情况下，我喜欢创建一个不可变的对象包装类，*扩展了*可变类。我发现静态的`getInstance(MutableObject obj)`方法很有用，但是构造函数`ImmutableObject(MutableObject obj)`也很有用。

## 当我想改变一个不可变的对象时怎么办？

[![a woman having a good time in a shop](../Images/97935e211f94ca8b0e4354cda4344c8a.png "a woman having a good time in a shop")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lVJEnC0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IHsv75ol.jpg)

每个人都会这样。你需要一个 an 对象，但是你并不了解这个对象的一切。你不能完全信任一个不可变的对象。

在这种情况下，我使用构建器模式。

构建器模式创建一个临时对象，该对象具有与所需对象相同的字段。它有所有字段的 getters 和 setters。它还有一个创建所需对象的`build()`方法

想象一个小的不可变对象:

```
class ImmutableDog {
    private final String name;
    private final int weight

    public ImmutableDog(String name, int weight){
        this.name = name;
        this.weight = weight;
    }

    public String getName(){
        return this.name;
    }

    public int getWeight(){
        return this.weight;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是构建器的样子:

```
class ImmutableDogBuilder {
    private String name;
    private int weight;

    public ImmutableDogBuilder(){}

    public ImmutableDog build(){
        return new ImmutableDog(name, weight);
    }

    public ImmutableDogBuilder setName(String name){
        this.name = name;
        return this;
    }

    public ImmutableDogBuilder setWeight(int weight){
        this.weight = weight;
        return this;
    }

    public String getName(){
        return this.name;
    }

    public int getWeight(){
        return this.weight;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意 setter**我真的很喜欢这种在构建器类的每个 setter 上返回`this`的模式，因为它创建了一个非常流畅的 api。你可以这样使用这个`ImmutableDogBuilder`:

```
ImmutableDogBuilder dogBuilder = new ImmutableDogBuilder().setName("Rover").setWeight(25); 
```

Enter fullscreen mode Exit fullscreen mode

你可以想象在有更多字段的类中，这会大大压缩你的代码。

### 把构建器放在哪里？

这里有两派思想。

一方面，您可以为构建器创建一个单独的类。这很简单，非常常规，您的 IDE 可能会将这些类组合在一起，因为它们可能有相似的名称。

另一方面，您可以将 builder 类作为公共静态内部类嵌入到不可变对象类中。

我更喜欢在不可变对象中嵌入构建器类，因为我将构建器视为不可变对象的助手，而不是独立的东西。这使它们紧密结合在一起。

## 有坏数据的不可变对象怎么办？

[![a very dirty car](../Images/ddf042d724e5c50c5cc00a089668f2b1.png "a very dirty car")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o_ECRiWc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cqrtgTXl.jpg)

每个人都会遇到这种情况，尤其是当你接受输入的时候。坏数据！

坏数据是不好的，但是*不可变的*坏数据似乎尤其错误——你甚至不能修复它！

我使用两种方法来防止坏数据变成不可变对象。

我的主要方法是一套业务规则，用于测试合理的、允许的数据。业务规则关注构建器，如果构建器通过，我认为创建不可变对象是可以的。

我的第二种方法是在不可变对象中嵌入少量业务逻辑。我不允许必填字段为空，任何可空的字段都是一个`Optional`。

例如:

```
class ImmutableDog {
    private final String name;
    private final Optional<int> weight

    public ImmutableDog(String name, Optional<int> weight){
        Objects.requireNonNull(name);
        this.name = name;
        this.weight = weight;
    }

    public String getName(){
        return this.name;
    }

    public Optional<int> getWeight(){
        return this.weight;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个需要名字，但不需要权重的`ImmutableDog`。知道如何称呼一只狗是很重要的，但是知道毛毛重 15 磅并不是绝对必要的。

如果没有提供名称，`Objects.requireNonNull`将立即抛出一个`NullPointerException`。这可以防止创建无意义的不可变对象。它还允许不可变对象(如流或函数)的用户跳过处理空值。这里没有空值。

使用`Optional<int>`使`ImmutableDog`的消费者立即意识到他们可能必须处理一个空值。提供`Optional` api 为下游用户提供了一种简单、实用的处理空值的方式。

## 综述

[![a colorful celebration](../Images/68dd6023e8284414f3343bb073806257.png "a colorful celebration")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DSaTe7Jr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fiWbSvbl.jpg)

不可变对象需要一些特殊的关心和处理，但是它们的效用是值得的。

要记住的主要原则是:

1.  克隆不可变对象内部的数组、集合和对象
2.  当你需要一个可变的对象时，使用构建器
3.  使用 Optional 在对象的 api 中指示可为空的字段
4.  在错误的数据对象上快速失败

前进，停止变异

你已经读到这里，请订阅我的邮件列表，获取更多相同的内容。一个月一次，你可以随时退订，我保证不垃圾你。T3】