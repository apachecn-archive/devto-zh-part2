# PlugFace 重生 Java 插件系统

> 原文：<https://dev.to/matteojoliveau/plugface-reborn---the-java-plugin-system-18bm>

*访问[https://plugface.matteojoliveau.com](https://plugface.matteojoliveau.com)获取完整文档*

一年前，我发现需要动态地将代码加载到我的 Java 应用程序中，而不必每次都重新编译或打包我的软件。基本上，我在寻找一个插件系统。

当时只有一个值得注意的开源插件系统， [PF4J](https://github.com/pf4j/pf4j) ，但是我感觉有点笨重。我想要非常简单的东西，可以让我在几分钟内变得高效。所以我决定自己写。

输入[插头面](https://github.com/matteojoliveau/plugface)。
第一次迭代非常糟糕，因为当时我只是公司的实习生，还在学习真实世界的 Java，不具备编写一个好的、可用的框架的知识。

# 丑陋

第一个实现并不像我希望的那么简单。有接口要实现，反射要使用，配置文件要写，沙盒权限系统，插件间依赖注入的半工作/半失败的尝试，我为编写和使用它付出了很多汗水和泪水。
不是世界上最伟大的软件。
这是一个老插件的例子:

```
package org.plugface.demo.plugins.greet;

import org.plugface.Plugin;
import org.plugface.PluginConfiguration;
import org.plugface.PluginStatus;
import org.plugface.demo.app.sdk.Greeter;
import org.plugface.impl.DefaultPluginConfiguration;

import java.util.Collections;

public class GreeterPlugin implements Plugin<String[], String>, Greeter{

    private final String name;
    private PluginConfiguration configuration;
    private PluginStatus status;
    private boolean enabled;

    public GreeterPlugin () {
        name = "greeter";
        configuration = new DefaultPluginConfiguration();
        status = PluginStatus.READY;
        enabled = false;
    }

    @Override
    public void start() {
        throw new UnsupportedOperationException("This plugin operates in single mode only");
    }

    @Override
    public void stop() {
        throw new UnsupportedOperationException("This plugin operates in single mode only");
    }

    @Override
    public String execute(String[] parameters) {
        return greet();
    }

    @Override
    public PluginConfiguration getPluginConfiguration() {
        return (PluginConfiguration) Collections.unmodifiableMap(configuration);
    }

    @Override
    public void setPluginConfiguration(PluginConfiguration configuration) {
        this.configuration = configuration;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public PluginStatus getStatus() {
        return status;
    }

    @Override
    public void setStatus(PluginStatus pluginStatus) {
        status = pluginStatus;
    }

    @Override
    public void enable() {
        this.enabled = true;
    }

    @Override
    public void disable() {
        this.enabled = false;
    }

    @Override
    public boolean isEnabled() {
        return enabled ;
    }

    @Override
    public String greet() {
        return "Hello PlugFace!":
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这些东西是怪物，界面很大，充满了有争议的有用特性(比如状态和名字),而且实现起来并不实际。
如果你想要比‘开始’、‘停止’和‘执行’更复杂的行为，你必须添加你自己的接口并将你的插件转换成那种类型，以便使用它或者通过反射访问方法。

然后我想到了一个主意。当所有的框架都可以处理动态类加载时，为什么还要限制用户与插件交互的方式呢？

# 好人

在 0.6 版本中，框架复活了。这(几乎)是完全重写的。我抛弃了整个接口故事，取而代之的是一个更加干净的注释，对你的插件应该如何运行没有任何意见。简单地让它实现一些你的应用程序知道的接口，然后通过它访问它。

这是同一个插件，但是有了新的系统:

```
package org.plugface.demo.plugins.greet;

import org.plugface.core.annotations.Plugin;
import org.plugface.demo.app.sdk.Greeter;

@Plugin("greeter")
public class GreeterPlugin implements Greeter {

    @Override
    public String greet() {
        return "Hello PlugFace!";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它实现了我的应用程序提供的`Greeter`接口。现在，我的代码可以简单地检索插件，并把它放入 Greeter 类型的变量中，而不必知道`GreeterPlugin`类。整洁！

这是我的应用程序代码。

```
final PluginManager manager = PluginManagers.defaultPluginManager();

manager.loadPlugins(PluginSources.jarSource("path/to/my/plugin/jars"));

final Greeter greeter = manager.getPlugin(Greeter.class);

greeter.greet(); // => "Hello PlugFace!" 
```

Enter fullscreen mode Exit fullscreen mode

`PluginManager`是一个实用程序类，用于从各种来源加载插件，这是对旧版本的另一个改进，旧版本只提供硬编码的 JAR 加载，并访问`PluginContext`来通过名称和类型添加/获取/删除插件。

我还完全抛弃了沙盒功能，沙盒功能利用 Java SecurityContext 特性来限制插件的权限。由于这是一个巨大的痛苦处理，我宁愿完全删除它，以专注于这个新版本的核心功能。如果我能找到一种方便的方式来实现它，我可能会在未来的版本中重新引入它。

# 依赖注入完成正确

旧系统试图引入的一个特性是插件可以互相插入。它通过在`PluginConfiguration`对象(基本上是一个美化的`Map<String, Object>`)中放入插件来实现这一点，它可以工作，但使用起来相当不实际。

PlugFace 0.6 通过构造函数引入了一个完全成熟的依赖注入系统，使用标准的`@javax.inject.Inject`注释(如果存在单个构造函数，它将在未来成为可选的),这样插件类可以用于任何标准的 DI 框架，比如 [Spring](https://spring.io) 或 [Guice](https://github.com/google/guice) 。
它支持使用[拓扑排序](https://en.wikipedia.org/wiki/Topological_sorting)的复杂依赖图，具有循环依赖检测和所有这些好东西。

```
package org.plugface.demo.plugins.math;

import org.plugface.core.annotations.Plugin;
import org.plugface.demo.app.sdk.Mathematics;

import javax.inject.Inject;

@Plugin("math")
public class MathPlugin implements Mathematics {

    private final SumPlugin sum;
    private final MultPlugin mult;

    @Inject
    public MathPlugin(SumPlugin sum, MultPlugin mult) {
        this.sum = sum;
        this.mult = mult;
    }

    @Override
    public Integer sum(int a, int b) {
        return sum.sum(a, b);
    }

    @Override
    public Integer mult(int a, int b) {
        return mult.mult(a, b);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 春集成

Spring 框架是 Java 中一个巨大的工具包。它几乎什么都有，但它的核心特性显然是依赖注入系统。因为我在几乎所有的项目中都经常使用 Spring，所以我希望 PlugFace 能与它很好地集成。
如果你想使用普通的 PlugFace，你可以导入`plugface-core`模块，但是如果使用 Spring，你可能想切换到特殊的`plugface-spring`模块。
它不仅为 PluginManager 和 PluginContext 提供了自动配置的 bean，还增加了对从`ApplicationContext`中检索插件的支持(这意味着如果一个对象没有作为插件被找到，它将在 Spring bean 中被查找)，也许更重要的是，它使 Spring bean 成为插件中依赖注入的可行目标。

没错，使用`plugface-spring`你不仅可以在你的插件内部注入其他插件，还可以在你的应用中注册任何 Spring bean。

你的插件可以访问服务类、存储库、实用程序单例，基本上任何东西都存在于 Spring 上下文中。

```
package org.plugface.demo.plugins.user;

import org.plugface.core.annotations.Plugin;
import org.plugface.demo.app.sdk.Greeter;
import org.plugface.demo.app.sdk.TestService;

import javax.inject.Inject;

@Plugin("greeter")
public class UserPlugin implements UserDetails {

    private final UserService userService; //this is a Spring service

    @Inject
    public GreeterPlugin(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String getUsername() {
        final User user = userService.getUserById(0L);
        return user.getUsername();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 从此

将来，我还想集成其他 DI 框架，比如 Guice，如果 Java 插件出现了新的用例，就扩展当前的特性集。在那之前，谢谢大家的关注，祝编码愉快！