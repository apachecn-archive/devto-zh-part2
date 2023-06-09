# 如何有效地使用模型映射器

> 原文：<https://dev.to/isaacbroyles/how-to-effectively-use-model-mapper-2kb5>

我有幸修改了大量使用[模型映射器](http://modelmapper.org/)的大型代码库。我在盲目使用它的时候遇到了一些问题，而且我在寻找答案方面运气不佳。这篇博文中的大部分信息已经存在于其他地方，但令我惊讶的是，这些信息在谷歌上看不到。希望我们能纠正这一点。

以下是我关于有效使用模型映射器的提示。

## 使用并理解 PropertyMap

您首先应该理解配置 PropertyMap 的细微差别，我个人认为这并不直观。直到浏览了文档和一些写得很好的堆栈溢出帖子，一切才开始明朗起来。

如果你和我一样，在寻求帮助时，你会遇到的第一件事就是遇到这样一个不直观的例子

```
public class MyMapper extends PropertyMap<Source, Destination>{
    @Override
    protected void configure(){
        map(source.getProperty()).setOther(null);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这令人困惑的原因是，为什么我要将我的目标属性设置为空？

### configure()函数包含 EDSL

答案是，`configure()`方法中的代码实际上根本不是作为映射的一部分来执行的。它实际上是一种嵌入式领域特定语言(EDSL)。**这是让我对模型映射器的理解更加清晰的第一件事。**

你可以在官方 javadocs 中阅读更多关于 EDSL 如何工作的信息。

要点是这样的:

*   映射不是作为 Java 代码执行的。映射过程中没有执行`PropertyMap.configure()`。您可以通过在调试映射时在方法中设置断点来验证这一点。
*   使用`addMapping(...)`添加映射时，映射由 ModelMapper 解释。
*   `Converter.convert()`的**是运行时执行的**。所以你可以这样做:

```
public class MyMapper extends PropertyMap<Source, Destination>{
    private Converter<String, String> toUppercase = (c) -> {
        return c.getSource().toUpperCase();
    };

    @Override
    protected void configure(){
        using(toUppercase)
            .map(source.getProperty())
            .setOther(null);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每次映射对象时，都会执行`toUppercase`转换器中的代码。

### 定义映射时使用 TDD

我将在下一节中讨论单元测试，但是在这里提出它是因为它可以减少样板文件。实际上，在大多数情况下，模型映射器本身做得相当不错。如果你在测试中定义了你所期望的，这将迫使你在 PropertyMap 中做最基本的定义。

例如，如果您在没有运行任何测试的情况下天真地定义了一个`PropertyMap`，您可能会得到如下冗长的结果:

```
public class OverkillPropertyMap
        extends PropertyMap<PersonDto, Person> {

    @Override
    protected void configure() {
        map(source.getFirstName(), destination.getFirstName());
        map(source.getLastName(), destination.getLastName());
        map(source.getEmail(), destination.getEmail());
        map(source.getPhone(), destination.getPhone());
        map(source.getAddress1(), destination.getAddress1());
        map(source.getAddress2(), destination.getAddress2());
        map(source.getCity(), destination.getCity());
        map(source.getState(), destination.getState());
        map(source.getZip(), destination.getZip());
        map(source.getCountry(), destination.getCountry());

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个人为的例子，对于有经验的开发人员来说可能看起来很傻，但是您会惊讶于代码库中出现了这么多这样的映射。

让我们看看能否在下一节中通过单元测试来简化这一点。

## 单元测试

ModelMapper 官方文档声明您应该总是对映射进行单元测试，我完全同意。与大多数问题一样，在单元测试中发现问题要比在 SDLC 中更容易。

在映射单元测试中，有两件事需要注意:

1.  **总是**使用`ModelMapper.validate()`
2.  验证字段是否映射有 **assertEquals(…)**

### ModelMapper.validate()

这个简便的方法将验证所有的目标属性是否匹配。如果有人在添加后忘记映射目标属性，这将非常有用。换句话说，这将保护您的映射器免受未来对源/目标对象的更改。

如果您在这里得到误报，您可以跳过映射中的属性:

```
skip(destination.getPropertyNotMapped()); 
```

Enter fullscreen mode Exit fullscreen mode

### 有效单元测试的例子

回到上一节的例子，除了这次让我们从单元测试开始。

一个标准的单元测试应该是这样的:

```
public class PersonMapTests {
    private ModelMapper modelMapper;

    @Before
    public void setup(){
        modelMapper = new ModelMapper();
        modelMapper.addMappings(new PersonMap());
        modelMapper.getConfiguration()
            .setMatchingStrategy(MatchingStrategies.STRICT);
    }

    @Test
    public void map_ShouldValidate_IfObjectsValid(){
        var personDto = preparePersonDto();

        var personModel =
            modelMapper.map(personDto, Person.class);

        modelMapper.validate();

        assertEquals(
            personDto.getFirstName(), personModel.getFirstName());
        assertEquals(
            personDto.getLastName(), personModel.getLastName());
        assertEquals(
            personDto.getEmail(), personModel.getEmail());
        assertEquals(
            personDto.getPhone(), personModel.getPhone());
        assertEquals(
            personDto.getAddress1(), personModel.getAddress1());
        assertEquals(
            personDto.getAddress2(), personModel.getAddress2());
        assertEquals(
            personDto.getCity(), personModel.getCity());
        assertEquals(
            personDto.getState(), personModel.getState());
        assertEquals(
            personDto.getCountry(), personModel.getCountry());
        assertEquals(
            personDto.getZip(), personModel.getZip());
    }

    private PersonDto preparePersonDto() {
        return new PersonDto.Builder()
                .firstName("Isaac")
                .lastName("Broyles")
                .email("isaacbroyles@example.com")
                .phone("202-555-0129")
                .address1("123 Street")
                .address2("Apt 101")
                .city("Ville")
                .state("TX")
                .zip("12345")
                .country("USA")
                .build();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在运行这个单元测试时，我得到一个类似这样的错误:

```
1) Unmapped destination properties found in TypeMap[PersonDto -> Person]:

com.isaacbroyles.examples.modelmapperexamples.models.Person.setLastModified() 
```

Enter fullscreen mode Exit fullscreen mode

啊哦，`validate()`方法告诉我们，我们有一个没有映射的目标属性。所以让我们定义一个`PropertyMap`来解决这个问题。

下面是属性图，用最少的代码来修复我的错误:

```
public class PersonMap extends PropertyMap<PersonDto, Person> {
    @Override
    protected void configure() {
        map(source.getDateCreated(),
            destination.getLastModified());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我重新运行我的测试——哦，酷，它现在通过了。

将我们最后得到的`PropertyMap`与上一节中的`OverkillPropertyMap`进行比较。所有其他属性都是自动映射的，即使使用`MatchingStrategies.STRICT`。

## 结论

我对是否喜欢使用模型映射器有些矛盾。一方面，它提供了减少样板映射代码的好方法。另一方面，它的一些相当不直观的方面让我头疼。这不一定是对 ModelMapper 的批评，可能更多的是因为我对它缺乏经验，而不是工具本身的不足。很明显，它已经成为一种流行的工具，这是有原因的。

无论如何，我认为这篇文章对那些使用 ModelMapper 的代码库的人是有用的，并且会遇到同样的问题。

## 相关链接

*   [ModelMapper](http://modelmapper.org/) -官方 ModelMapper 网站。
*   [精彩的 StackOverflow 回答](https://stackoverflow.com/a/44534173) -我看到了这个很棒的回答，这促使我更深入地挖掘，以更多地了解 ModelMapper 的内部工作方式。