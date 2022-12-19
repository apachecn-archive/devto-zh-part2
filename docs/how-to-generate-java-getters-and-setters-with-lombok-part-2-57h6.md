# 如何用 Lombok 生成 Java Getters 和 Setters(第 2 部分)

> 原文：<https://dev.to/andremare/how-to-generate-java-getters-and-setters-with-lombok-part-2-57h6>

### 快速小结

Lombok 项目是一个 java 库，它帮助开发人员生成样板代码，比如针对普通旧 Java 对象(POJOs)的“getter”和“setter”方法。通过简单地将 Lombok 库添加到您的 IDE 和构建路径中，Lombok 库将根据注释自动生成 Java 字节码，放入。类文件。

这篇文章将关注如何在类的成员属性上使用@Getter 和 [@setter](https://dev.to/setter) 注释来为这些字段生成 mutator 和 accessor 方法。这篇文章还将描述如何为 mutator 和 accessor 方法定义访问级别。

### 要求

下面的列表定义了我用来实现示例代码的技术和库:

*   龙目岛图书馆版本 1.16.20。[(下载)](https://projectlombok.org/download)
*   [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
*   Spring 工具套件[(下载)](https://spring.io/tools/sts/all)

### 多个帖子

这将是关于如何使用 Lombok java 库为 java 类自动生成 getter 和 setter 方法的系列文章的一部分。

*   [如何用 Lombok 生成 Java Getters 和 Setter(第一部分)](https://www.code2bits.com/how-to-generate-java-getters-and-setters-with-lombok-part-1/)
*   [如何用 Lombok 生成 Java Getters 和 Setter(第二部分)](https://www.code2bits.com/how-to-generate-java-getters-and-setters-with-lombok-part-2/)

### 介绍

您可以用@Getter 和/或 [@setter](https://dev.to/setter) 注释任何字段，Lombok 库将为该特定成员属性生成 access & mutator 方法。这允许您更具体地指定哪些字段需要访问器或赋值器方法，甚至能够为特定方法指定 Java 访问修饰符。

Java 访问修饰符指定了成员属性、方法、构造函数和类的范围(可访问性)。Java 中有 4 种类型的访问修饰符，即私有、默认、受保护和公共。

*   **public** 访问修饰符指定可以从任何地方访问成员和/或方法。
*   **protected** 访问修饰符指定成员和/或方法只能在它自己的包中访问(如同 package-private ),此外，还可以被另一个包中它的类的子类访问。
*   **默认**访问修饰符(package-private 或未指定修饰符)指定成员和/或方法仅在其自己的包中可见。
*   **private** 访问修饰符指定成员和/或方法只能在它自己的类中被访问。

### 例子

#### 具有 AccessLevels 的字段上的 Getter 和 Setter

可以为@Getter 和/或 [@setter](https://dev.to/setter) 注释指定访问级别，以使用适当的 Java 访问修饰符自动生成访问器和赋值器方法。这允许您通过 Lombok 库控制方法的可访问性。

除非您明确指定 AccessLevel 作为注释的一部分，否则生成的访问器/赋值器方法将是公共的。下面的例子说明了如何使用 AccessLevels 为每个方法指定 Java 访问修饰符。

通过使用特殊的 AccessLevel，您总是可以手动禁用任何字段的 getter/setter 生成。无访问级别。

[![Student_Code](../Images/0ab07935c9ea60065002e34b162f8a31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EH2zpT2Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yc2a2j2wr7e3je7dse80.png)

下表说明了 Lombok AccessLevel 将如何映射到用于 Access 和 mutator 方法的 Java 访问修饰符。

| 龙目岛访问级别 | Java 访问修饰符 |
| AccessLevel。私人的 | 私人的 |
| AccessLevel。保护 | 保护 |
| AccessLevel。公众的 | 公众的 |
| AccessLevel。包裹 | 系统默认值 |
| AccessLevel。组件 | 系统默认值 |
| AccessLevel。没有人 | 方法未生成！！！ |
| 未规定的 | 公众的 |

您应该在终端中使用“javac”命令来编译学生类。要成功运行该命令，应该确保 Lombok Jar 文件和 Student.java 文件位于执行该命令的同一个目录中。编译完学生类后，您应该使用“javap”命令反汇编学生 Java 类文件。

```
$ javac -cp lombok.jar Student.java
$ javap Student.class

```

javap 命令打印出传递给它的类的 package、protected 和 public 字段和方法。javap 命令将其输出显示到 stdout。因此，没有打印出 Student 类的私有成员属性，但是现在可以看到如何为该类的每个非静态私有成员创建 getter 和 setter 方法，这取决于作为注释的一部分设置的 AccessLevel。

[![Student_Decompile](../Images/898cbcc4cb673c3716b186232fac48f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V9g6oA2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/13pr9n64tdkrv4un3vta.png)

基于这个例子，下面是 lombok 用不同的 java 访问修饰符生成方法的原因:

**成员属性:id**
因为没有指定 AccessLevel，所以访问器方法是公共的。mutator 方法是私有的，因为 AccessLevel 被设置为 private。

**Member Attribute:first name**
由于没有指定 AccessLevel，所以访问器方法是公共的。mutator 方法受到保护，因为 AccessLevel 被设置为 protected。(见第 5 行)

**Member Attribute:last name**
由于没有指定 AccessLevel，所以访问器方法是公共的。mutator 方法是公共的，因为 AccessLevel 被设置为 public。(见第 7 行)

**成员属性:性别**
由于没有指定 AccessLevel，所以访问器方法是公共的。mutator 方法是“默认”的，因为 AccessLevel 被设置为 PACKAGE。(见第 9 行)

**成员属性:生日**
因为没有指定 AccessLevel，所以访问器方法是公共的。mutator 方法是“默认”的，因为 AccessLevel 被设置为 MODULE。(见第 11 行)

**成员属性:contactEmail**
因为没有指定 AccessLevel，所以访问器方法是公共的。由于 AccessLevel 设置为 NONE，因此未生成 mutator 方法。

**成员属性:isFirstYear**
因为没有指定 AccessLevel，所以访问器方法是公共的。因为未指定 AccessLevel，所以访问器方法是公共的。

### 摘要

这篇文章展示了如何利用 Lombok 库以及@Getter 和 [@setter](https://dev.to/setter) 注释来生成方法的另一个例子。它进一步展示了如何为每个生成的方法指定预期的 java 访问修饰符。本系列的下一篇文章将说明如何利用 lombok config 来改变方法的生成方式。