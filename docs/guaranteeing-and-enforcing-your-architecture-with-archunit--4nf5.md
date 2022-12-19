# 保证和加强您的架构(使用 ArchUnit)

> 原文：<https://dev.to/davidcyp_52/guaranteeing-and-enforcing-your-architecture-with-archunit--4nf5>

[![Guaranteeing and enforcing your architecture (with ArchUnit)](../Images/5c08574ddd6cee1de357db01ffbea7f1.png "Guaranteeing and enforcing your architecture (with ArchUnit)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NZR2Qpa5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frh5t6skv17nzv8mpx1v.jpeg)

最近，我在开发一个严重依赖各种数据源的应用程序。我们的应用程序实际上组合、解释和可视化了数据。

不是每个人都可以看到所有的数据。我们需要一种简单的方法来保护对我们资源的访问，尽管不仅仅是基于典型的用户和角色。对数据的访问应该在更精细的级别上受到保护。然而，虽然一方面访问规则是由一组互斥的参数定义的，但我们也希望尽可能保持代码的整洁和可维护性。

因为我们的应用程序已经依赖于 Spring Boot，所以我们选择了 spring-security，更准确地说是@PreAuthorize 注释。

我们可以选择复制/粘贴/修改包含的 SPeL 表达式，尽管这将很快引入维护问题，例如，假设我们只有两个不同的参数用于授权访问一段数据，我们仍然会在整个代码中复制 SPeL 表达式，每当访问规则改变时都会引入维护问题。

```
 @Component
   public class SomeSecuredResource {

    @PreAuthorize("hasAccess(#foo)")
    public SensitiveData showMe(Foo foo){
        return new SensitiveData();
    }

    @PreAuthorize("isAllowedToSee(#bar)")
    public SensitiveData showMeToo(Bar bar){
        return new SensitiveData();
    }
   } 
```

Enter fullscreen mode Exit fullscreen mode

所以我们为这个
创建了一个简单的注释

```
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@PreAuthorize(Sensitive.EXPRESSION)

public @interface Sensitive {

    String EXPRESSION = "#foo != null ? hasAccess(#foo) : (#bar != null ? isAllowedToSee(#bar) : false)";

} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以应用:

```
@Component
public class SomeSecuredResource {

    @Sensitivepublic SensitiveData showMe(Foo foo){
        return new SensitiveData();
    }

    @Sensitivepublic SensitiveData showMeToo(Bar bar){
        return new SensitiveData();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧。人们会认为，问题解决了...

那只是部分正确。好了，我们隔离了在给定特定方法参数的情况下授权访问方法的逻辑，但是我们引入了其他不太明显的问题。

假设一个新的开发人员加入了团队。他看到这些@敏感注释保护对资源的访问。他将它们应用于:

```
@Component
public class NewDevsClass {

    @Sensitivepublic SensitiveData doSomething(long someParameter){
       return new SensitiveData(); 
    }

    @Sensitivepublic SensitiveData doOtherStuff(String foo){
        return new SensitiveData();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

他打破了这里的潜规则。正如我们所看到的，我们的@Sensitive 注释的实现依赖于 Foo 或 Bar 类型的参数。

实际上他打破了我们的建筑规则，那就是:

用@Sensitive 批注批注的方法必须有一个 Foo 或 Bar 类型的参数。

那么，我们如何解决这个问题呢？在维基上保留一份详细的规则清单，让每个人都仔细检查？配置声纳规则并每天晚上收集报告？不，关于什么...将这些规则嵌入到快速执行的单元测试中，并获得即时反馈？

有请[首席](https://github.com/TNG/ArchUnit)

ArchUnit:一个 Java 架构测试库，用普通 Java
来指定和断言架构规则，所以让我们深入研究，并编写一个测试来确保正确使用我们的注释。

```
public class SensitiveAnnotationUsageTest {

    DescribedPredicate<JavaClass> haveAFieldAnnotatedWithSensitive =
            new DescribedPredicate<JavaClass>("have a field annotated with @Sensitive"){
                @Overridepublic boolean apply(JavaClass input) {
                    // note : simplified version which inspects all classesreturn true;
                }
            };

    ArchCondition<JavaClass> mustContainAParameterOfTypeFooOrBar =
            new ArchCondition<JavaClass>("must have parameter of type 'com.example.post.Foo' or 'com.example.post.Bar'") {
                @Overridepublic void check(JavaClass item, ConditionEvents events) {
                    List<JavaMethod> collect = item.getMethods().stream()
                            .filter(method -> method.isAnnotatedWith(Sensitive.class)).collect(Collectors.toList());

                    for(JavaMethod method: collect){

                        List<String> names = method.getParameters().getNames();

                        if(!names.contains("com.example.post.Foo") && !names.contains("com.example.post.Bar"))  {
                            String message = String.format(
                                    "Method %s bevat geen parameter met type 'Foo' of 'Bar", method.getFullName());
                            events.add(SimpleConditionEvent.violated(method, message));
                        }
                    }
                }
            };

    @Testpublic void checkArchitecturalRules(){
        JavaClasses importedClasses = new ClassFileImporter().importPackages("com.example.post");

        ArchRule rule = ArchRuleDefinition.classes()
                .that(haveAFieldAnnotatedWithSensitive)
                .should(mustContainAParameterOfTypeFooOrBar);

        rule.check(importedClasses);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的两个类上执行这个测试会产生以下结果:

```
java.lang.AssertionError: Architecture Violation [Priority: MEDIUM] - Rule 'classes that have a field annotated with @Sensitive should must have parameter of type 'com.example.post.Foo' was violated (2 times):
Method com.example.post.NewDevsClass.doOtherStuff(java.lang.String) bevat geen parameter met type 'Foo' of 'Bar
Method com.example.post.NewDevsClass.doSomething(long) bevat geen parameter met type 'Foo' of 'Bar 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我们有一个适当的测试，强制执行我们的架构规则的正确应用。

想了解更多关于 ArchUnit 的信息吗？请务必查看他们的[用户指南](http://archunit.org/)和[示例](https://github.com/TNG/ArchUnit)。