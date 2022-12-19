# 用 Java 生成数据类

> 原文：<https://dev.to/bertilmuth/generating-data-classes-in-java-4cef>

Kotlin 有一个简洁的语法来声明数据类:

```
data class User(val name: String, val age: Int) 
```

Enter fullscreen mode Exit fullscreen mode

等效的 Java 语法是冗长的。你必须创建一个带有私有字段的 Java 类。以及字段的 getter 和 setter 方法。以及类似于`equals()`、`hashCode()`和`toString()`的附加方法。

但是谁说你必须手工创建 Java 代码呢？在本文中，我将向您展示如何从一个 [YAML](https://en.wikipedia.org/wiki/YAML) 文件生成 Java 源文件。

下面是 YAML 文件的例子:

```
User:
    name: Name
    age: Integer

Name:
    firstName: String
    lastName: String 
```

Enter fullscreen mode Exit fullscreen mode

代码生成器的示例输出是两个 Java 源文件，`User.java`和`Name.java`。

`User.java`的内容:

```
public class User{
    private Name name;
    private Integer age;

    public User(){
    }

    public Name getName(){
        return name;
    }
    public void setName(Name name){
        this.name = name;
    }
    public Integer getAge(){
        return age;
    }
    public void setAge(Integer age){
        this.age = age;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Name.java`类似。

本文的要点是:您将学习如何从头开始编写代码生成器。很容易根据您的需求进行调整。

# 主法

[`main()`](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/Main.java) 方法做两件事:

*   第一步:读入 YAML 文件，转化为类规范
*   步骤 2:根据类规范生成 Java 源文件

它将读取和生成解耦。所以以后可以更改输入格式，或者支持更多的输入格式。

下面是`main()`方法:

```
public static void main(String[] args) throws Exception {
    // Make sure there is exactly one command line argument, the path to the YAML file
    if (args.length != 1) {
        System.out.println("Please supply exactly one argument, the absolute path of the YAML file.");
        return;
    }

    // Get the YAML file's handle, and the directory it's contained in
    // (generated files will be placed there)
    final String yamlFilePath = args[0];
    final File yamlFile = new File(yamlFilePath);
    final File outputDirectory = yamlFile.getParentFile();

    // Step 1: Read in the YAML file, into class specifications
    YamlClassSpecificationReader yamlReader = new YamlClassSpecificationReader();
    List<ClassSpecification> classSpecifications = yamlReader.read(yamlFile);

    // Step 2: Generate Java source files from the class specifications
    JavaDataClassGenerator javaDataClassGenerator = new JavaDataClassGenerator();
    javaDataClassGenerator.generateJavaSourceFiles(classSpecifications, outputDirectory);

    System.out.println("Successfully generated files to: " + outputDirectory.getAbsolutePath());
} 
```

Enter fullscreen mode Exit fullscreen mode

# 第一步:读入 YAML 文件，进入类规范

让我解释一下这一行发生了什么:

```
List<ClassSpecification> classSpecifications = yamlReader.read(yamlFile); 
```

Enter fullscreen mode Exit fullscreen mode

类规范是要生成的类及其字段的定义。
还记得示例 YAML 文件中的`User`吗？

```
User:
    name: Name
    age: Integer 
```

Enter fullscreen mode Exit fullscreen mode

当 [YAML 阅读器](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/read/YamlClassSpecificationReader.java)读取时，它将创建一个`ClassSpecification`对象，名为`User`。这个类规范将引用两个`FieldSpecification`对象，分别叫做`name`和`age`。

`ClassSpecification`类和`FieldSpecification`类的代码很简单。

[`ClassSpecification.java`](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/model/ClassSpecification.java) 的内容:

```
public class ClassSpecification {
    private String name;
    private List<FieldSpecification> fieldSpecifications;

    public ClassSpecification(String className, List<FieldSpecification> fieldSpecifications) {
        this.name = className;
        this.fieldSpecifications = fieldSpecifications;
    }

    public String getName() {
        return name;
    }

    public List<FieldSpecification> getFieldSpecifications() {
        return Collections.unmodifiableList(fieldSpecifications);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[`FieldSpecification.java`](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/model/FieldSpecification.java) 的内容:

```
public class FieldSpecification {
    private String name;
    private String type;

    public FieldSpecification(String fieldName, String fieldType) {
        this.name = fieldName;
        this.type = fieldType;
    }

    public String getName() {
        return name;
    }

    public String getType() {
        return type;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一步剩下的唯一问题是:如何从 YAML 文件获得这些类的对象？

[YAML 阅读器](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/read/YamlClassSpecificationReader.java)使用 [SnakeYAML](https://bitbucket.org/asomov/snakeyaml) 库解析 YAML 文件。
SnakeYAML 使得 YAML 文件的内容可以在地图和列表等数据结构中使用。对于这篇文章，你只需要了解地图。因为那是我们在 YAML 档案里用的。

再看例子:

```
User:
    name: Name
    age: Integer

Name:
    firstName: String
    lastName: String 
```

Enter fullscreen mode Exit fullscreen mode

你在这里看到的是两个嵌套的地图。
外层映射的关键是类名(就像`User`)。
当您获得`User`键的值时，您将获得该类的字段映射:

```
 name: Name
    age: Integer 
```

Enter fullscreen mode Exit fullscreen mode

这个内部映射的键是字段名，值是字段类型。

这是字符串到字符串的映射。理解 [YAML 阅读器](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/read/YamlClassSpecificationReader.java)的代码很重要。
以下是读入完整 YAML 文件内容的方法:

```
private Map<String, Map<String, String>> readYamlClassSpecifications(Reader reader) {
    Yaml yaml = new Yaml();

    // Read in the complete YAML file to a map of strings to a map of strings to strings
    Map<String, Map<String, String>> yamlClassSpecifications = 
        (Map<String, Map<String, String>>) yaml.load(reader);

    return yamlClassSpecifications;
} 
```

Enter fullscreen mode Exit fullscreen mode

以`yamlClassSpecifications`作为输入， [YAML 阅读器](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/read/YamlClassSpecificationReader.java)创建`ClassSpecification`对象:

```
private List<ClassSpecification> createClassSpecificationsFrom(Map<String, Map<String, String>> yamlClassSpecifications) {
    final Map<String, List<FieldSpecification>> classNameToFieldSpecificationsMap 
        = createClassNameToFieldSpecificationsMap(yamlClassSpecifications);

    List<ClassSpecification> classSpecifications = 
        classNameToFieldSpecificationsMap.entrySet().stream()
            .map(e -> new ClassSpecification(e.getKey(), e.getValue()))
            .collect(toList());

    return classSpecifications;
} 
```

Enter fullscreen mode Exit fullscreen mode

`createClassNameToFieldSpecificationsMap()`方法创建

*   每个类的字段规范，并基于这些规范
*   每个类名到其字段规范的映射。

然后， [YAML 阅读器](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/read/YamlClassSpecificationReader.java)为该地图中的每个条目创建一个`ClassSpecification`对象。

YAML 文件的内容现在可以以独立于 YAML 的方式用于步骤 2。我们完成了第一步。

# 第二步:从类规范生成 Java 源文件

Apache FreeMarker 是一个产生文本输出的 Java 模板引擎。模板是用 FreeMarker 模板语言(FTL)编写的。它允许将静态文本与 Java 对象的内容混合在一起。

下面是生成 Java 源文件的模板， [`javadataclass.ftl`](https://github.com/bertilmuth/javadataclass/blob/2c550c8ea0ab551d06eed342ea0013043f96f080/src/main/resources/javadataclass.ftl) :

```
public class ${classSpecification.name}{
<#list classSpecification.fieldSpecifications as field>
    private ${field.type} ${field.name};
</#list>

    public ${classSpecification.name}(){
    }

<#list classSpecification.fieldSpecifications as field>
    public ${field.type} get${field.name?cap_first}(){
        return ${field.name};
    }
    public void set${field.name?cap_first}(${field.type} ${field.name}){
        this.${field.name} = ${field.name};
    }
</#list>    
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来看第一行:

```
public class ${classSpecification.name}{ 
```

Enter fullscreen mode Exit fullscreen mode

您会看到它以类声明的静态文本开始:`public class`。有趣的部分在中间:`${classSpecification.name}`

当 Freemarker 处理模板时，它访问其模型中的`classSpecification`对象。它在其上调用`getName()`方法。

模板的这部分呢？

```
<#list classSpecification.fieldSpecifications as field>
    private ${field.type} ${field.name};
</#list> 
```

Enter fullscreen mode Exit fullscreen mode

起初，Freemarker 调用`classSpecification.getFieldSpecifications()`。然后，它遍历字段规范。

最后一件事。那行有点奇怪:

```
public ${field.type} get${field.name?cap_first}(){ 
```

Enter fullscreen mode Exit fullscreen mode

假设示例字段是`age: Integer`(在 YAML)。
Freemarker 将其翻译为:

```
public Integer getAge(){ 
```

Enter fullscreen mode Exit fullscreen mode

所以`?cap_first`的意思是:大写第一个字母，因为 YAML 文件中的`age`是小写字母。

关于模板已经说得够多了。你如何生成 Java 源文件？
首先，您需要通过创建一个`Configuration`实例来配置 FreeMarker。这种情况发生在 [`JavaDataClassGenerator`](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/generate/JavaDataClassGenerator.java) :
的构造函数中

```
public JavaDataClassGenerator() throws IOException {        
    configuration = new Configuration(Configuration.VERSION_2_3_28);

    // Set the root of the class path ("") as the location to find templates
    configuration.setClassLoaderForTemplateLoading(getClass().getClassLoader(), "");

    configuration.setDefaultEncoding("UTF-8");
    configuration.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW_HANDLER);
    configuration.setLogTemplateExceptions(false);
    configuration.setWrapUncheckedExceptions(true);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了生成源文件， [`JavaDataClassGenerator`](https://github.com/bertilmuth/javadataclass/blob/ee95965bc798ae5425083baf88c4750fb27ecf11/src/main/java/de/bertilmuth/javadataclass/generate/JavaDataClassGenerator.java) 遍历类规范，并为每个类生成一个源文件:

```
public void generateJavaSourceFiles(Collection<ClassSpecification> classSpecifications, File yamlFileDirectory) throws Exception {
    Map<String, Object> freemarkerDataModel = new HashMap<>();

    // Get the template to generate Java source files
    Template template = configuration.getTemplate("javadataclass.ftl");

    for (ClassSpecification classSpecification : classSpecifications) {
        // Put the classSpecification into the data model.
        // It can  be accessed in the template through ${classSpecification}
        freemarkerDataModel.put("classSpecification", classSpecification);

        // The Java source file will be generated in the same directory as the YAML file
        File javaSourceFile = new File(yamlFileDirectory, classSpecification.getName() + ".java");
        Writer javaSourceFileWriter = new FileWriter(javaSourceFile);

        // Generate the Java source file
        template.process(freemarkerDataModel, javaSourceFileWriter);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

# 结论

我向您展示了如何基于 YAML 文件构建 Java 源代码生成器。我选择 YAML 是因为它容易加工。因此易于教授。如果你认为合适，用另一种格式替换它。

你可以在 [Github](https://github.com/bertilmuth/javadataclass) 上找到完整的代码。

为了使代码尽可能容易理解，我采取了一些捷径:

*   没有像`equals()`、`hashCode()`和`toString()`这样的方法
*   没有数据类的继承
*   生成的 Java 类在默认包中
*   输出目录与输入目录相同
*   错误处理不是我关注的焦点

生产就绪解决方案需要处理这些问题。同样，对于数据类， [Project Lombok](https://projectlombok.org/) 也是一个替代方案。

所以把这篇文章当成一个开始，而不是结束。想象什么是可能的。

几个例子:

*   脚手架 JPA 实体类或 Spring 存储库
*   根据应用程序中的模式，从一个规范生成几个类
*   用不同的编程语言生成代码
*   制作文件

出于研究目的，我目前使用这种方法将自然语言需求直接翻译成代码。你会怎么做？