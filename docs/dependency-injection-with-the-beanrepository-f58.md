# 使用 BeanRepository 进行依赖注入

> 原文：<https://dev.to/tinosteinort/dependency-injection-with-the-beanrepository-f58>

哦，不，又发生了！这就是另一个依赖注入框架...

# 为什么？

过去，我想启动一个应该可以在 java 沙箱中运行的项目，我想使用依赖注入。首先，我认为我可以只使用 Spring 的依赖机制(`spring-context`)，但事实并非如此:Spring 广泛使用反射，这在沙盒中是不允许的。

我下一次尝试的结果几乎相同:Guice。现在，当我根据自己的需要尝试这样做时，一些系统属性出现了错误，不允许从沙箱中读取。今天我不能够准确地记住哪个属性和错误。

在这一点上，我不想尝试其他框架，但我知道有很多这样的框架。当然也有不使用反射的框架。我认为创建一个自己的 DI 框架，看看我需要解决什么问题，以符合我的期望，这可能会很酷。还有一点:*编码很有趣*。

# 最初的期望

*   某种依赖注入
*   单一 beanss 和原型 bean
*   没有反射和注释
*   java 代码中的配置

这些期望过一会儿就会扩大。我也完成了启动`BeanRepository`的项目。

# 发生了什么？

然而，第一个结果是一个服务定位器。一颗豆子必须主动请求另一颗豆子。随着 API 的发展，现在，我想我可以说它是服务定位器和依赖注入框架的混合体。这取决于你想做什么，以及你如何去做。在[这一环节](https://martinfowler.com/articles/injection.html)中，马丁·福勒描述了他对这两种方法的想法。

# 我能看看一些代码吗？

这是一个非常简单的例子，有三个类。这个程序的用例只是打印出命令行参数。

这是我们的用例，只是一个服务:

```
public class ArgumentPrinter {

    private final ArgsProvider argsProvider;

    /**
     * The ArgsProvider is responsible for getting the command line arguments,
     *  so we need it as a dependency. The ArgsProvider bean is provided by
     *  the BeanRepository.
     */
    public ArgumentPrinter(final ArgsProvider argsProvider) {
        this.argsProvider = argsProvider;
    }

    public void printCommandLineArgs() {

        // Isn't it a nice use case? :-)
        System.out.println("Commandline args[]:");

        for (String arg : argsProvider.get()) {
            System.out.println(" * '" + arg + "'");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`BeanRepository`是一种容器，所以我们需要一个入口点来执行我们自己的代码:

```
/**
 * Every bean, that implements ApplicationStartedListener, will be triggered
 *  right after the BeanRepository is built. This should be used to execute code
 *  on startup.
 */
public class StartupListener extends ApplicationStartedListener {

    private final ArgumentPrinter argumentPrinter;

    public StartupListener(final ArgumentPrinter argumentPrinter) {
        this.argumentPrinter = argumentPrinter;
    }

    @Override
    public void onEvent(final ApplicationStartedEvent event) {

        // the entry point

        System.out.println("Application started");

        argumentPrinter.printCommandLineArgs();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们只错过了一个入门类，以及`BeanRepository`的配置。

```
public class SimpleExampleApp implements BeanRepositoryConfigurator {

    public static void main(String[] args) {

        // Set args manually without configuring IDE run configs
        // -> not needed for real applications
        args = new String[] {"argument 1", "argument 2", "value"};

        // Start the application with dependency injection support
        //  1.: parameter: commandline args[]
        //  2.: parameter: Some class that implements BeanRepositoryConfigurator
        BeanRepositoryApplication.run(args, new SimpleExampleApp());
    }

    @Override
    public void configure(final BeanRepository.BeanRepositoryBuilder builder) {

        // This method is used to configure the available beans an how
        //  they depends on each other.
        // The scheme of the singleton method is:
        //  1.: type of the bean
        //  2.: reference to the constructor of the bean
        //  3., 4., 5., 6., 7.: the types of the needed beans

        builder
                           // type of the bean    // constructor ref    // type of reference
                .singleton(StartupListener.class, StartupListener::new, ArgumentPrinter.class)
                           // type of the bean    // constructor ref    // type of reference
                .singleton(ArgumentPrinter.class, ArgumentPrinter::new, ArgsProvider.class);
                // the ArgsProvider is a bean which is provided by the BeanRepository, to get
                //  the command line args
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

可以引用的 beans 是有限的。目前，一个 bean 只能有 5 个或更少的依赖项。因为没有使用反射，所以东西是通过手工编码实现的。在这种情况下:用一个具有不同数量参数的方法创建接口。对我来说，这就足够了。

另一点是，不直接支持循环引用。这是可能的，但需要一些额外的编码。见[此例](https://github.com/tinosteinort/beanrepository/tree/master/src/test/java/com/github/tinosteinort/beanrepository/example/_03_cyclicreferenceexample)。

# 其他东西

还有其他的功能，在这篇文章中没有列出。

*   其他范围
*   提供者
*   工厂
*   别名
*   ...

# 哪里可以拿到？

试试看:

```
<dependency>
  <groupId>com.github.tinosteinort</groupId>
  <artifactId>beanrepository</artifactId>
  <version>1.7.0</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

获得见解:

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [蒂诺斯坦诺尔](https://github.com/tinosteinort) / [豆库](https://github.com/tinosteinort/beanrepository)

### 依赖注入/服务定位器库

<article class="markdown-body entry-content container-lg" itemprop="text">

# BeanRepository 依赖注入/服务定位器

这个框架是服务定位器模式和依赖注入模式的混合实现。马丁·福勒在本文中描述了这些模式。`BeanRepository`不使用反射来注入 beans。因为这个事实，它可以在 Java 沙箱中使用，在那里不允许反射。

## 特征

*   Java 代码中简单、不言自明且故障安全的配置
*   不使用反射或注释
*   构造函数注入
*   支持单件、原型和实例
*   供应者
*   工厂
*   beans 的别名
*   启动时快速失败
*   在 bean 初始化后执行代码(后构造)
*   可配置单例是否惰性初始化
*   检测特定类型的 beans
*   模块化是可能的

## 限制

*   不直接支持循环引用。但是如果需要，请参见[cyclireferenceexampleapp](https://raw.githubusercontent.com/tinosteinort/beanrepository/master//src/test/java/com/github/tinosteinort/beanrepository/example/_03_cyclicreferenceexample)以获得解决方案
*   没有请求或会话范围
*   构造函数中不允许有初始化代码
    *   构造函数可能被调用…

</article>

[View on GitHub](https://github.com/tinosteinort/beanrepository)