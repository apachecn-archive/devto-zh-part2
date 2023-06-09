# Java 在类字段中可选？为什么不呢？

> 原文：<https://dev.to/piczmar_0/java-optional-in-class-fields-why-not-40df>

[可选的](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)是在 Java 8 中引入的，根据文档,
应该限制从方法返回的`null`的使用。

`Optional`类是不可序列化的，因此它不打算用作 Java beans 中的字段
。

正如 Brian Goetz 在 [Stack Overflow](https://stackoverflow.com/questions/26327957/should-java-8-getters-return-optional-type/26328555#26328555)
中所说的那样，“通常使用它作为 getters 的返回值肯定会被过度使用。”

我同意这样的说法，即应该谨慎使用它们，而绝对不是解决所有问题的灵丹妙药。

我碰巧看到在下面这样的上下文中使用`Optional`来避免使用 if-else 语句，以支持更函数式的编程流程:

```
Optional.ofNullable(aField)
  .map(this::doSthWithTheField)
  .orElse(this::doSthElse) 
```

对比旧的好的命令式风格:

```
if(aField==null){
  doSthWithTheField(aField);
}else{
  doSthElse(aField);
} 
```

`Optional`的这种用法是否已经是过度使用？

我认为这是一个团队中的品味、实用主义和协议的问题。在我看来，if somestimes if-else 和有时像上面这样的结构用在单个项目课程代码周围有点混乱。

我个人更喜欢有`Optional`的版本。

在你的域名转移对象(DTO)中使用选项的疯狂想法怎么样？

或者根本没有那么疯狂？

请继续阅读，看看这种方法对你是否有意义。

假设您有一个业务域，其中有一个类`User`，如下所示:

```
class User{
  private String name;
  private String secondName;

  public String getName(){
    return name;
  }
  public Optional<String> getSecondName(){
    return Optional.ofNullable(name)
  }

  // skipped setters for readability
} 
```

您的`User`具有像`name`这样的属性，它是必需的(非空)，而`secondName`可以是空的。
有了这个，你在任何地方都不用担心你会得到第二个名字`null`。你可以这样使用它:

```
User user = ...
String userName = user.getName();
String displayedName = user.getSecondName()
  .map(s-> userName + ", " + s)
  .orElse(userName); 
```

而不是:

```
User user = ...
String userName = user.getName();
String second = user.getSecondName();
String displayedName = null;
if(second != null){
  displayedName = userName + ", " + second;
} else {
  displayedName = userName;
} 
```

看出区别了吗？此外，当您查看域类 getters 时，您已经知道哪些字段是可选的，您的代码是自文档化的。

现在，如果您需要在控制器的响应中传递这样的域对象，该怎么办呢？

通常你可能想把它包装在一个域转移对象中，比如`UserResponse`，但是为了简单起见，让我们假设你将在控制器中返回`User`。

Spring 将如何知道如何将`Optional`值转换成 JSON？
嗯..默认不会。它将获取`Optional`类的公共 getter 方法，即`isPresent()`，并在 JSON 中使用它，例如:

```
{"name":"Marcin","secondName":{"present":true}} 
```

为了允许正确的转换，Jackson 库有一个专用于 Java 8 类型的模块。
使用 Maven，我们需要添加这个依赖:

```
<dependency>
   <groupId>com.fasterxml.jackson.datatype</groupId>
   <artifactId>jackson-datatype-jdk8</artifactId>
   <version>2.9.6</version>
</dependency> 
```

并像这样在`ObjectMapper`中注册:

```
ObjectMapper mapper = new ObjectMapper();
mapper.registerModule(new Jdk8Module()); 
```

好消息是，如果您使用的是具有`spring-boot-starter-web`依赖性的 Spring Boot，那么它已经配置好了一切，可以使用了:)

[![Spring dependencies](img/cf801934d5b149c05180eac1d4681738.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eEKu4ALT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uo1cinj62lx2cta81ia5.png)

这里的是这篇文章的演示。

如果你喜欢它，你可能也喜欢其他参考:

*   [https://dzone . com/articles/optionals-npes-and-design-practices](https://dzone.com/articles/optionals-npes-and-design-practices)
*   [https://www.baeldung.com/jackson-optional](https://www.baeldung.com/jackson-optional)
*   [https://spring . io/blog/2014/12/02/latest-Jackson-integration-improvements-in-spring](https://spring.io/blog/2014/12/02/latest-jackson-integration-improvements-in-spring)
*   [https://github . com/witchtrollest/lombok/issues/1214】的缩写](https://github.com/rzwitserloot/lombok/issues/1214)