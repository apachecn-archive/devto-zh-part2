# 一个 c#开发人员开始学习 Java 课程

> 原文：<https://dev.to/alexs1305/a-c-dev-starting-a-java-course-2k1b>

我在开放大学开设了一个模块，通过学习和使用 Java 来学习面向对象的概念。作为个人文档以及任何人的兴趣，我决定记录我的旅程。

在我们开始之前，我在过去 4 年左右的时间里一直在我的职业中使用 c#，首先作为一名自动化测试人员，然后作为一名软件工程师，所以我熟悉 OO 原则和概念。尽管我承认我的理论有所欠缺，因为在我现在从事的工作之前，我没有上过大学，也没有学习过任何计算机科学课程。我使用过其他语言，但 Java 并不是我真正接触过的语言，但它与 c#非常相似，我多次听到人们谈论它，所以我至少对学习它时会面临的一些差异有所了解。

以下是我开始学习这门课程时的第一个发现。

### 我要我的 IDE

大学使用的工具是 BlueJ。虽然我可以看到对那些正在学习 OO 和 Java 的人的吸引力，但我正在经历一场来自 Visual Studio 和 VS 代码的真正战斗。我的智能感知呢！这感觉像是一种倒退，是一种与现实世界不相关的体验。

### 收藏难了很多！

好吧，我承认也许 Linq 宠坏了我，但使用集合似乎是不必要的困难。看起来创建一个新的列表并遍历它需要很多代码。也许这只是我的想法，我希望几个星期后我会对此一笑置之，并在评论中夸夸其谈，说这实际上是多么容易。

### 我的切入点在哪里

当我想创建一个 hello world 控制台应用程序时，我遇到了这个问题。快速搜索之后，发现这就是方法

```
public static void main() { 
...
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以在任何班级进行。我想这既有利也有弊。

### 总是键入

我非常喜欢使用 c#中的 *var* ,以及几乎所有通用的、能让编译器计算出类型的关键字。但是在 Java 中，你必须每次都设置类型。没什么大不了的，在某些情况下，确保变量绝对是你期望的类型是有益的

```
String name  = "John Doe"; 
```

Enter fullscreen mode Exit fullscreen mode

### 大写字符串和布尔，小写 int？

从我所能收集到的信息来看，这些类型的大小写各不相同。在 c#中，几乎都是小写字母

```
string Name;
int Age; 
boolean IsVerified; 
```

Enter fullscreen mode Exit fullscreen mode

但是在 Java 中它们是

```
String name;
int age; 
Boolean isVerified 
```

Enter fullscreen mode Exit fullscreen mode

### 更多大小写问题

这一点我已经习惯了，因为我曾经使用过 JS 和 TS，也只是知道它，但是方法名和实例变量(在 c#中称为属性)以小写字母
开始

```
 private String name;

public void setName(String name){
 this.name = name;
}
... 
```

Enter fullscreen mode Exit fullscreen mode

### 显式 Getters 和 Setters

当被告知必须为我想要公开和操作的每个实例变量创建和公开 getters 和 setters 方法时，我感到非常震惊。我更喜欢 c#处理这个问题的方式

c#

```
public string Name { get; set; } 
```

Enter fullscreen mode Exit fullscreen mode

Java

```
private String name;

public String getName(){
   return name;
}

public void setName(String name){
   this.name = name
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总而言之

看起来好像我在抱怨 Java，也许我是一个 c#粉丝，但是你真的能期待什么呢，这是我的世界，已经有几年了，所以需要一些调整。随着我对 Java 世界的深入研究，我希望继续记录我的学习，毕竟它仍然如此受欢迎是有原因的！