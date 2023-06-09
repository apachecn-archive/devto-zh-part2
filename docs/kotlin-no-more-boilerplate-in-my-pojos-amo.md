# Kotlin:删除 POJO 样板文件

> 原文：<https://dev.to/cr0wst/kotlin-no-more-boilerplate-in-my-pojos-amo>

Kotlin 很快成为我一直以来最喜欢使用的语言之一。它是一种 JVM 语言，提供了静态类型、内置的空安全和一些强大的语法糖，有助于减轻 Java 应用程序中常见的膨胀。

我想在一系列简短的博客文章中强调一些我最喜欢的功能。

## POJO

POJO 是**P**lain**O**LD**J**ava**O**object 的缩写，是一个不绑定到任何特定框架或库的基线对象。它不应该特意去实现任何接口或扩展任何类。有人会说，它也不应该有任何专门的注释，如`@JsonProperty`或`@Entity`。我愿意牺牲定义的严格性来换取这些注释提供的灵活性。

### JavaBean

JavaBean 更进一步，它是:

1.  可序列化
2.  有一个无参数构造函数。
3.  通过遵循标准命名约定的 getters 和 setters 来访问属性。

虽然从技术上来说 POJOs 是可行的，但是 JavaBean 规范确实需要 serializable 接口的实现。这打破了 POJO 旅的反接口议程。

一个实例 bean 注意，只要没有定义其他构造函数，就提供无参数构造函数:

```
class Person implements Serializable {
    protected String name;

    protected int age;

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Java 的批评者和开发者会告诉你，这是一堆冗长的类和样板定义。他们没有错；Java *可能是一堆冗长的类和样板定义。然而，bean 规范有一些优点:*

1.  定义一个无参数构造函数保证了无意义的实例化。
2.  提供遵循标准命名约定`get`、`set`和`is`的 getters 和 setters 允许框架使用反射，而不需要任何额外的配置来访问属性。
3.  保持类的简单意味着它可以被重用，而不需要了解任何奇怪的依赖关系。

## 科特林波乔

Kotlin 通过自动包含大量样板代码来简化 POJO 过程。同一个 POJO 可以声明为:

```
class Person(var name: String = "", var age: Int = 0) : Serializable 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`class`关键字，以类似于 Java 的方式声明类。但是，不是用`implements`或者`extends`来声明一个接口或者父类；我们把接口放在了一个`:`之后。此外，请注意，属性的类型是在属性之后声明的。这是因为如果可以推断类型，则类型声明是可选的。

### 构造函数字段

Kotlin 允许你在类声明中声明字段，就像下面这样:

```
public Person(String name, int age) {
    this.name = name;
    this.age = age;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个构造函数是自动为您生成的。但是，除非为属性指定默认值，否则不会生成无参数构造函数。如果我们将该类声明为:

```
class Person(var name: String, var age: Int) : Serializable 
```

Enter fullscreen mode Exit fullscreen mode

只会创建上面的构造函数。

JavaBean 规范需要一个无参数的构造函数，因此需要提供默认值。当使用像 JPA 这样需要无参数构造函数的框架时，这会带来一些问题。Kotlin 有一些插件可以在不指定默认值的情况下为您创建这个构造函数。

参见: [Kotlin 编译器插件:无参数编译器插件](https://kotlinlang.org/docs/reference/compiler-plugins.html#no-arg-compiler-plugin)

### getter 和 Setters

Getters 和 setters 也是自动创建的，它们在直接处理属性时在内部使用。请注意，Kotlin 提供了两个初始化关键字，`var`和`val`。不可变的属性(或`final`)应该用`val`声明；Kotlin 将只为不可变属性生成一个 getter。

## 结论

我坚信应该尽量减少添加到代码中的冗长的样板文件。如果这是意料之中的事情，并且我的 IDE 可以自动完成，为什么我的语言不能做到呢？Kotlin 不仅为我做到了这一点，而且在从 Java 迁移时，它还以一种易于理解的方式做到了这一点。这些概念没有一个是不必要的外来概念；最终结果是简短易读的代码。