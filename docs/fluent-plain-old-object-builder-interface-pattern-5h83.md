# 流畅简单的旧对象构建器界面模式

> 原文：<https://dev.to/elmuerte/fluent-plain-old-object-builder-interface-pattern-5h83>

这篇文章的标题实际上是我想说的以下几个概念的组合:

*   普通的旧物件
*   构建器模式
*   流畅的界面

这三者是密切相关的，并且经常混合在一起，但通常不会同时发生。所以让我们来谈谈它们到底是什么。

注意:本文使用 Java 作为它的基础语言，但是这些思想适用于任何面向对象的环境。

# 平平淡淡的老物件

这是一个只保存数据的对象，它不包含任何业务逻辑。在 Java 世界中，这种类型的对象通常被称为 POJO(普通旧 Java 对象)。

```
public class Email {
    String subject;
    String body;
    String sender;
    String receiver;

    public String getSubject() {
        return subject.
    }

    public void setSubject(String aSubject) {
        subject = aSubject;
    }

    ...
} 
```

它只包含字段和读写这些字段的方法。一个普通的旧对象不应该包含包含所谓业务逻辑的方法。例如，它不应该有像`send()`这样的方法来发送电子邮件，或者`delete()`来删除方法。这些方法意味着普通旧对象了解通信和持久存储机制。

它可以包含一些方法来告诉我们一些关于数据的基本信息。例如，方法`boolean hasBody()`不会被认为是业务逻辑。只要普通对象的方法只与对象本身交互，就不会被认为是业务逻辑。(当你想把这个逻辑从这个对象中转移到一个面向业务逻辑的对象中时，总会有一个问题。但那是不同的话题。)

普通旧对象通常用于模型-视图-控制器设置中的模型、持久化数据和[参数对象](https://refactoring.com/catalog/introduceParameterObject.html)。

# 构建器模式

[构建模式](https://en.wikipedia.org/wiki/Builder_pattern)是一种所谓的创造性[软件设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)。它基本上是一个系统，允许你给它提供原料，并最终创建一个对象的实例。

```
public class EmailBuilder {
    String subject;
    String body;
    String sender;
    String receiver;

    public Email build() {
        Email email = new Email();
        email.setSubject(subject);
        ...
        return email;
    }

    public void withSubject(String aSubject) {
        subject = aSubject;
    }

    ...
} 
```

使用构建器背后的主要思想是，您可以隐藏对象的具体实现，但仍然能够通过公共接口创建它的实例。

例如，构建器将允许您使 *Email* 对象的公共接口不可变，但是仍然允许框架的内部作为普通的旧对象与具体的实现一起工作。

另一个用例是构建器可以返回一个不同的具体实现。

```
public class SecureEmailBuilder extends EmailBuilder {
    public Email build() {
        SecureEmail email = new SecureEmail();
        email.setSubject(subject);
        ...
        return email;
    }
} 
```

调用逻辑不知道，也不关心，当调用`build()`方法时，它收到哪种*电子邮件*子类型。

# 流畅的界面

一个[流畅的界面](https://en.wikipedia.org/wiki/Fluent_interface)不像前两个科目，是一个类型构造。它定义了一种定义方法接口的方式。

在 fluent 接口中，方法执行某个动作并返回一个对象以继续。最终结果将是一连串的方法调用，读起来很像一个句子。

通常，fluent 方法在对象上写一个字段，然后返回对象本身。但是也可以返回不同的 fluent 对象来继续。

## 流畅的构建器

例如，如果我们让上面提到的构建器成为一个流畅的构建器，我们将能够写出下面的代码。

```
emailBuilder.withSubject("Hello World").withSender("Me").withReceiver("You").build() 
```

生成器方法的接口被更改为返回生成器本身。

```
public class EmailBuilder {
    ...

    public EmailBuilder withSubject(String aSubject) {
        subject = aSubject;
        return this;
    }

    ...
} 
```

您可以使这个流畅的构建器不那么冗长，并去掉前缀`with`(或者`set`，如果您喜欢这种风格的话)，然后您将得到:

```
emailBuilder.subject("Hello World").sender("Me").receiver("You").build() 
```

最后一个方法`build()`是一个终止方法，因为它停止了 fluent 接口。

这种结构经常在提供流畅 API 的框架中使用。Java 世界中一些很好的例子是 jOOQ 或者 T2 Apache Camel。但是 Java 8 的流 API 也遵循这种风格。或者说在前端世界，jQuery 就是一个很好的例子。

## 通顺质朴的老物件

我们也可以改变普通的旧对象，使其具有流畅的界面。

```
public class Email {
    ...

    public Email setSubject(String aSubject) {
        subject = aSubject;
        return this;
    }

    ...
} 
```

现在，您可以编写一个漂亮的一行程序，用您想要的所有数据创建一个 *Email* 对象。

```
new Email().setSubject("Hello World").setSender("Me") ... 
```

但这是**而不是**好。set 方法不再具有 set 方法通常具有的接口签名，因为它不再返回 void。这可能会破坏与许多框架的互操作性，这些框架期望 set 方法不返回任何内容。

为了让它玩得更好，我们基本上需要为每个 setter 添加第二个方法。

```
public class Email {
    ...

    public void setSubject(String aSubject) {
        subject = aSubject;
    }

    public Email withSubject(String aSubject) {
        setSubject(aSubject);
        return this;
    }

    ...
} 
```

遗憾的是，这使得这个类有点臃肿，同时也增加了可维护性。

## 流畅的物体

我们也可以使我们的普通旧对象完全流畅。不过，它将不再是一个普通的老物件。但是对于各种用例来说，这可能更有趣。

```
public class Email {
    String subject;
    String body;
    String sender;
    String receiver;

    public String subject() {
        return subject.
    }

    public Email subject(String aSubject) {
        subject = aSubject;
        return this;
    }

    ...
} 
```

set 和 get 方法都不再有前缀。无参数的`subject()`方法是 get 方法，单参数的`subject(String)`方法是 set 方法，它也返回 Email 对象，因此您可以链接另一个方法调用。

# 我的“准则”

上面提到的结构没有一个比另一个更好。总有你需要考虑的取舍。

## 只是普通的旧物件

我通常坚持使用普通的旧对象作为数据容器，我希望这些数据容器被保存在数据库中，或者被转换成静态形式以便传输(XML、JSON 等)。).原因很简单，它将拥有最好的框架支持。

## 参数对象

对于参数对象，我通常更倾向于使用更流畅的对象。但是经常用一些方法重复以不打乱整体风格

```
public class ParamObject {
    ...

    public void setFoo(String aFoo) {
        foo = aFoo;
    }

    public foo(String aFoo) {
        setFoo(aFoo);
        return this;
    }

    public String getFoo() {
        return foo;
    }

    ...
} 
```

这个对象看起来像一个普通的旧对象，但也允许它被流畅地使用

```
doSomething(new ParamObject().foo("Hello").bar("world")) 
```

## 建设者

对于构建器，我通常使用为*流畅构建器*提供的方法，使用*和*作为所有写数据方法的前缀。这样，执行动作的方法和只写数据的方法之间就有了明显的区别。

我没有使用 *set* 作为前缀，因为我认为这是设置构建器的属性，而不是定义我正在创建的对象的值。

## 组件

组件不在本文讨论范围内，但是我还是想在我的指南中提到它们。组件的一个例子是 UI 元素，例如表单上的按钮和表单本身。

组件也是对象，但绝对不是普通的旧对象。组件显然具有属性。对于 set 方法，我总是返回组件，这样我可以在一行中链接不同的 set 动作。

```
form.add(new Button("save").setEnabled(false).setType(ButtonType.SUBMIT)); 
```

## 流畅的 API

显然，当你创建一个流畅的 API 时，你会变得非常流畅。这也意味着在这些 fluent 中返回的对象应该尽可能的纯 fluent。所以没有用 *set* 或 *get* 的方法来写字段。只有终止调用会返回一个“正常”对象。