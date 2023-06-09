# Java 10 的新特性

> 原文：<https://dev.to/jeetmp3/whats-new-in-java-10-43hf>

所以终于等完了，Java 10 现在 GA 了。这个建筑的酷功能已经在讨论中了。没错，我说的就是`var`。在这篇博客中，我将和全能的`var`一起讨论一些新特性。

### 1。可选的# orElseThrow()

`Optional.get()`的同义词但更直观。它将抛出`java.util.NoSuchElementException`与`get()`相同，但是清楚地知道如果值不在那里，方法可能抛出异常，所以开发者也将考虑处理异常情况。

```
class OptionalDemo {
    public void perform() {
        Optional<String> result = calculate();
        // It'll throw the exception 
        String result1 = result.get();

        // It'll also throw the exception but by it's name
        // developer might think of handling the exception
        String result1 = result.orElseThrow();
    }

    public Optional<String> calculate() {
        return Optional.empty();
    }
} 
```

### 2。在列表、集合和映射中复制 Of()

在列表、集合和映射中添加了 copyOf()，以便从给定集合创建 unmodifiableCollections()。该方法检查集合是否已经是类型为`java.util.ImmutableCollections.AbstractImmutableMap`的集合，然后简单地返回，否则从给定的集合创建一个新的 ImmutableCollection。

```
class UnmodifiableCollectionDemo {

    public void usingList() {
        List<Integer> list = List.of(1, 2, 3, 4, 5);

        // already ImmutableCollection so simply returns
        List<Integer> unmodifiable1 = List.copyOf(list); 

        List<Integer> list1 = new ArrayList();
        list1.add(1);
        list1.add(2);

        // Creates new ImmutableCollection and returns
        List<Integer> unmodifiable = List.copyOf(list1); 
    } 
} 
```

### 3。收集器中添加的新方法

为了将流结果收集为不可修改的集合，在`java.util.stream.Collectors`类中添加了 3 个新方法。

1.  toUnmodifiableList
2.  通穆迪法布勒塞特
3.  toUnmodifiableMap

```
class StreamsDemo {
    public void convertToUnmodifiableCollections(List<String> input) {

        // List demo
        List<String> list = input.stream()
                                .map(String::toUpperCase)
                                .collect(Collectors.toUnmodifiableList());

        // Set demo
        Set<String> set = input.stream()
                                .map(String::toUpperCase)
                                .collect(Collectors.toUnmodifiableSet());

        // Map demo
        Map<String, Integer> map = input.stream()
                                .map(String::toUpperCase)
                                .collect(Collectors.totoUnmodifiableMap(String::toString, String::length));
    }
} 
```

### 4。var -局部变量类型推理

lambdas 引入了类型推理。现在在 Java 10 中引入了一个新的关键字`var`用于局部变量类型推断。这将删除初始化局部变量的仪式代码，并保持 java 的静态类型。编译器将在编译时推断出该类型。

```
public void varDemo() {
    int i = 10;
    List<String> list = new ArrayList<>();
    Map<String, Integer> map = new HashMap<>();

    // With var above code can be written as

    var i = 10; 
    var list = new ArrayList<String>();
    var map = new HashMap<String, Integer>();
} 
```

类型推断只能用于局部变量，一旦类型被推断出来，就不能更改。

```
public void demo() {
    var name = "Jitendra";
    name = 3; // Not allowed
} 
```

目标类型的所有方法都将通过声明的变量来访问。

```
public void demo() {
    var list = new ArrayList<String>();
    list.add("Jitendra");
    list.add("Singh");
} 
```

快乐编码😀😀😀！！！如果您有任何反馈，请在下面评论。