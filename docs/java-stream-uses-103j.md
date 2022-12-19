# Java 流使用

> 原文：<https://dev.to/cr0wst/java-stream-uses-103j>

Java 8 中引入的 [Java Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) 向集合、数组和其他可迭代对象添加了函数操作。流是*而不是*集合或数据结构，但是它们被用来增强现有的结构。

下面是一些如何使用 Stream API 的例子。

## 第一个例子:迭代一个元素列表，打印出所有其他元素。

这是一个奇怪的例子，然而这是我在一些场合需要的东西。假设你有一个朋友列表，你想打印出每个人和他们的朋友。本质上，您需要对列表中的每个元素进行操作，这些元素是列表中的当前元素。

### 没有溪流

如果没有流，你可以这样来理解它:

```
for (String person : FRIENDS) {
    System.out.println("Friends of " + person + ":");
    for (String other : FRIENDS) {
        if (!person.equals(other)) {
            System.out.println(other);
        }
    }
} 
```

如果你想在线打印它们，任务会有点不同:

```
for (String person : FRIENDS) {
    List<String> friends = new ArrayList<>(FRIENDS);
    friends.remove(person);
    System.out.println("Friends of " + person + ": " + String.join(", ", friends));
} 
```

这看起来不太混乱，因为我们正在处理一个`String`列表。

### 同溪流

使用流，我们可以做以下事情:

```
FRIENDS.forEach(person -> {
    System.out.println("Friends of " + person + ":");
    FRIENDS.stream().filter(other -> !person.equals(other)).forEach(System.out::println);
}); 
```

如果你想内联打印它们，看起来会像这样:

```
FRIENDS.forEach(person -> {
        String friends = FRIENDS.stream().filter(other -> !person.equals(other)).collect(Collectors.joining(", "));
        System.out.println("Friends of " + person + ": " + friends);
    }); 
```

#### 这是怎么回事？

我们使用的是`filter`方法，它基于给定的谓词创建一个新的流(有点像条件)。谓词`other -> !person.equals(other)`指示新的流不要包含列表中与当前迭代的元素相匹配的元素。

在第一个例子中，我们使用了`forEach`方法，该方法接受一个 lambda 表达式，并对每个元素调用它。

在第二个例子中，我们使用了`collect`方法，这是一个终止方法(结束流)。通过传入`Collectors.joining`，我们告诉`collect`方法我们希望返回一个连接的`String`。

## 第二个例子:所有元素乘以五。

假设我们想要在不改变原始列表的情况下操作列表中的每个元素。在这个例子中，我们将获取一个`Integer`列表，并将它们都乘以 5。

### 没有溪流

下面是我们在没有流的情况下解决这个问题的方法:

```
List<Integer> multiplied = new ArrayList<>();
for (Integer number : NUMBERS) {
    multiplied.add(number * 5);
}

// To print the results you most likely used something like StringUtils.join, or Guava's Joiner
// Or you did messy things like this (not recommended because of the replacement):
String output = Arrays.toString(multiplied.toArray()).replace("[", "").replace("]", "");
System.out.println(output); 
```

在前面的例子中，我提到了如何显示信息。这并不是一个困难的问题，因为我们加入的元素已经是`String` s 了。在当前的例子中，这是一个更大的挑战。

你可能熟悉[阿帕奇社区](https://commons.apache.org/)的`StringUtils`或者[谷歌番石榴](https://github.com/google/guava)的`Joiner`。正如您将看到的，在使用流时，它们是不必要的。

### 同溪流

下面是 me 如何使用 streams api 解决这个问题:

```
List<Integer> multiplied = NUMBERS.stream().map(i -> i * 5).collect(Collectors.toList());

String output = multiplied.stream().map(Object::toString).collect(Collectors.joining(", "));
System.out.println(output); 
```

#### 这是怎么回事？

这一次我们使用了与`forEach`相似的`map`函数，它在流的每个元素上调用一个 lambda。但是，*有一个重要的*关键区别:

*   `forEach`在每个元素上调用一个 lambda。
*   `map`在每个元素上调用一个 lambda，并返回一个结果的*新*流。

这意味着当使用`map`时，我们可以改变元素并将结果复制到新的`List`中。我们既用它来应用将每个元素乘以 5 的λ`i -> i * 5`，又用它来应用λ`Object::toString`将每个`Integer`转换成用于连接的字符串。

我们还使用了带有`Collectors.toList()`的`collect`方法，它指示`collect`创建一个`List`并终止流。

## 第三个例子:从元素列表中创建属性列表。

假设我们有一个包含姓名和年龄属性的人员列表。大概是这样:

```
private static final List<Person> PEOPLE = List.of(new Person("Sarah", 29),
                                                    new Person("John", 16),
                                                    new Person("Mary", 21),
                                                    new Person("Susan", 55));

private static class Person {
    private String name;
    private int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    String getName() {
        return name;
    }

    int getAge() {
        return age;
    }
} 
```

如果我们想创建两个单独的列表呢？一个列表包含姓名，另一个包含年龄。

### 没有溪流

如果没有流，这可能看起来像:

```
List<String> names = new ArrayList<>();
List<Integer> ages = new ArrayList<>();

for (Person person : PEOPLE) {
    names.add(person.getName());
    ages.add(person.getAge());
}

// To print the results you most likely used something like StringUtils.join, or Guava's Joiner
// Or you did messy things like this:
System.out.println("Names: " + String.join(", ", names.toArray(new String[0])));
System.out.println("Ages: " + Arrays.toString(ages.toArray()).replace("[", "").replace("]", "")); 
```

### 同溪流

使用流，我们可以做以下事情:

```
List<String> names = PEOPLE.stream().map(Person::getName).collect(Collectors.toList());
List<Integer> ages = PEOPLE.stream().map(Person::getAge).collect(Collectors.toList());

System.out.println("Names: " + names.stream().collect(Collectors.joining(", ")));
System.out.println("Ages: " + ages.stream().map(Object::toString).collect(Collectors.joining(", "))); 
```

#### 这是怎么回事？

这实际上只是我们在前面的例子中讨论的内容的总结。我们利用了这样一个事实:`map`创建了一个新的流，并调用每个人的`getName`和`getAge`属性。

## 结论

希望这分享了在您的代码中使用流的一些价值。与 JavaScript 或 Kotlin 中的一些产品相比，我仍然觉得它们有点笨拙。然而，我也认为这是朝着正确方向迈出的一步。

每个例子的完整代码可以在 GitHub 的 [cr0wst/java-sandbox](https://github.com/cr0wst/java-sandbox/tree/master/src/main/java/net/smcrow/sandbox/streams) 找到