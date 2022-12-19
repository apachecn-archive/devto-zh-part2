# 今天我学会了那个元组！=元组 2

> 原文：<https://dev.to/nancyd/today-i-learned-that-tuple--tuple2-1opi>

很久以前，我编写了一个服务类，可以生成成对的用户。我心想，“哦！这个我知道！我需要返回一个元组列表！”

```
List<Tuple<User, User>> getUserMatches() {
  List<Tuple<User, User>> returnVal = []
  while (moreUsers()) {
    User a = getUserA()
    User b = getUserB() 
    returnVal.add(new Tuple[a, b])
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

测试代码。代码运行。一切都好。向前看。

稍后，一位同事问道:“为什么测试没有运行？”什么？不，我的测试运行，我敢肯定！仔细观察，不仅测试没有通过，而且无法编译。我摇摇头。我无法想象...更糟糕的是，IntelliJ 显示绝对没有编译问题。没有编译错误，很难修复问题。我得到的是一个异常:

```
java.lang.reflect.MalformedParameterizedTypeException
    at sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl.validateConstructorArguments(ParameterizedTypeImpl.java:60)
    at sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl.<init>(ParameterizedTypeImpl.java:53)
    at sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl.make(ParameterizedTypeImpl.java:95)
    at sun.reflect.generics.factory.CoreReflectionFactory.makeParameterizedType(CoreReflectionFactory.java:105)
    at sun.reflect.generics.visitor.Reifier.visitClassTypeSignature(Reifier.java:140)
[...]
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at org.codehaus.groovy.grails.cli.support.GrailsStarter.rootLoader(GrailsStarter.java:236)
    at org.codehaus.groovy.grails.cli.support.GrailsStarter.main(GrailsStarter.java:264) 
```

Enter fullscreen mode Exit fullscreen mode

一些谷歌搜索引导我写了这篇博文。啊，好的，我需要找出我错误地参数化一个类的地方。

只有在编译测试时才会出现异常，所以在我看来。我试图让系统只编译一些测试，以找出是哪个导致了问题。最后发现上面的测试对我的服务是负责任的。请注意，我仍然不知道它出了什么问题。我只知道当我试图编译它时，它会以可怕的`java.lang.reflect.MalformedParameterizedTypeException`失败。

我注释掉了文件的一半。问题仍然存在。我注释掉另一个季度。又是八分之一。直到我只剩下

```
@TestFor(UserMergeService) 
class UserMergeServiceSpec extends Specification {
} 
```

Enter fullscreen mode Exit fullscreen mode

它仍然会使编译器崩溃。我注释掉注释，它就编译了！我把整个文件放回去，只要注释被注释掉，它就能工作。

这实在令人费解。

我试着从另一端接近它。我在抛出`MalformedParameterizedTypeException`的地方设置了一个断点，沿着堆栈向上走，查看局部值，然后...这是我的元组。我看定义:

```
public class Tuple extends AbstractList {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯。没有参数化。那么，如何参数化一个元组呢？

就在那时，我发现[这篇博文](http://mrhaki.blogspot.ca/2016/03/groovy-goodness-using-tuples.html)澄清了一切。

Tuple 是不可变的项目列表，并且不可参数化。Tuple2 是一对两个东西，那两个东西的类可以参数化。

我不知道为什么只有在运行测试时这才是一个问题；代码自己编译和运行得很好，并且“坏代码”在服务类中，而不是在测试中，但是测试一定有什么问题...至少修好了！