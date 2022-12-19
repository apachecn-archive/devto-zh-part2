# 我终于明白了什么是班级

> 原文：<https://dev.to/chrisvasqm/how-i-finally-understood-what-a-class-is--24pl>

有句话说得好:

> “每个脑袋都是一个完全不同的世界。”

我觉得是`true`。有些人可能会认为某个老师不知道如何做他/她的工作，但也许是因为他/她教学的方式与你大脑的“连线”方式不兼容。我想这是那些不仅仅是对或错的事情之一。

许多事情都有不同的解释，这可能会导致宝贵信息的丢失。

所以，今天我想解释一下`class`的想法是如何在我脑海中闪现的。不仅仅是从概念上，还包括为什么它们在我们的代码中有用:

当我们开始编程时，我们首先要学习的是**变量**和**常量**。

所以，如果我们被赋予在控制台上打印你的`name`、`age`和`height`的任务，你很可能会这样做:

*注意:我决定不包括使用像`Scanner`这样的`class`来请求用户输入，以保持尽可能简单。*

```
// Java

public class Main {
    public static void main(String[] args) {
        String name = "Chris";
        int age = 23;
        double height = 1.85;
        System.out.println("name: " + name + ", age: " + age + ", height: " + height);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个会输出:

```
name: Chris, age: 23, height: 1.85 
```

Enter fullscreen mode Exit fullscreen mode

太好了！

但是，现在我们想让我们的变量名更具描述性，添加一个“chris”前缀怎么样？来看看:

```
// Java

public class Main {
    public static void main(String[] args) {
        String chrisName = "Chris";
        int chrisAge = 23;
        double chrisHeight = 1.85;
        System.out.println("chrisName: " + chrisName + ", chrisAge: " + age + ", chrisHeight: " + chrisHeight);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！

现在，让我们添加另一个人的信息:

```
// Java

public class Main {
    public static void main(String[] args) {
        String chrisName = "Chris";
        int chrisAge = 23;
        double chrisHeight = 1.85;
        System.out.println("chrisName: " + chrisName + ", chrisAge: " + age + ", chrisHeight: " + chrisHeight);

        String danielName = "Daniel";
        int danielAge = 27;
        double danielHeight = 1.71;
        System.out.println("danielName: " + danielName + ", danielAge:" + danielAge + ", danielHeight:" + danielHeight)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯...🤔

你开始看出规律了吗？

我们存储了来自两个不同人的相同的 3 个值...

这是一个`class`来拯救！

[![Superman on his way](../Images/874a3306870c1347d90a1fb82244ade0.png)T2】](https://i.giphy.com/media/a6b4OLwbXw4h2/giphy.gif)

让我们一步一步地重构我们的代码，一次只关注一个`person`。

# 克里斯班

在 OOP(面向对象编程)中，`classes`被用来用高级别的*内聚*来表示*状态*和*行为*。在英语中:这意味着给定的`class`的变量和方法是相互关联的。如果一个方法不使用`class`中的任何变量，这可能是它不属于这里的一个标志。

现在我们有了这个想法，我们可以用一个*默认构造函数*创建一个 Chris `class`，它将接受`chrisName`、`chrisAge`和`chrisHeight`的值:

> *一个`constructor`是一种特殊的函数，必须和它所属的`class`同名+没有返回值(甚至不需要`void`关键字)，通常用来确保那个`class`的一个实例在一个`valid state`中。*
> 
> *`Valid state`表示`object`具有预期的值。*
> 
> *An `object`是 a `class`的具体实现(稍后我们将看到它的实际应用)。*

所以，我们的克里斯`class`看起来会像这样:

```
// Java

public class Chris {
    private String name;
    private int age;
    private double height;

    public Chris(String name, int age, double height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 关键字`this`用来指代我们的`class`的全局范围，以区别`constructor`的`name`参数和实际全局范围的`name`。

现在我们可以重构我们的代码，以便使用我们新的闪亮的 Chris `class`！

所以，与其:

```
String chrisName = "Chris";
int chrisAge = 23;
double chrisHeight = 1.85; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以有:

```
Chris chris = new Chris("Chris", 23, 1.85); 
```

Enter fullscreen mode Exit fullscreen mode

> *关键字`new`用来指代我们克里斯`class`的`constructor`。*
> 
> *和`chris`(注意小写字母“c”)就是我们所说的`object`，因为它是我们的克里斯`class`的具体或“真实”实现。*

但是由于我们不再有`chrisName`、`chrisAge`和`chrisHeight`，我们的代码将无法正确编译。

有趣的...

我们如何解决这个问题？

好吧，如果我们回到我们的 Chris `class`实现，我们可以看到我们的全局作用域的变量(也称为`fields`、`private fields`或`member variables`)是`private`。所以，我们不能从外面接近他们。

为了能够做到这一点，我们必须添加能够帮助我们访问数据的`public`方法。

```
// Java

public class Chris {
    private String name;
    private int age;
    private double height;

    public Chris(String name, int age, double height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public double getHeight() {
        return height;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> *你注意到那些是怎么变的了吗...()方法不使用`this`关键字？这是因为没有匹配它们名字的参数，所以编译器知道我们引用的是全局范围的变量。*

现在我们有了这些方法，我们可以通过使用**点**来访问它们。)运算符，像这样:

```
// Java

public class Main {
    public static void main(String[] args) {
        Chris chris = new Chris("Chris", 23, 1.85);
        System.out.println("chrisName: " + chris.getName() + ", chrisAge: " + chris.getAge() + ", chrisHeight: " + chris.getHeight());

        String danielName = "Daniel";
        int danielAge = 27;
        double danielHeight = 1.71;

        System.out.println("danielName: " + danielName + ", danielAge:" + danielAge + ", danielHeight:" + danielHeight)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

那更好！

不，等等...

现在我们要为丹尼尔做同样的事情，让我们创造一个丹尼尔:

# 丹尼尔类

```
// Java

public class Daniel {
    private String name;
    private int age;
    private double height;

    public Daniel(String name, int age, double height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public double getHeight() {
        return height;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的主`class`将是:

```
// Java

public class Main {
    public static void main(String[] args) {
        Chris chris = new Chris("Chris", 23, 1.85);
        System.out.println("chrisName: " + chris.getName() + ", chrisAge: " + chris.getAge() + ", chrisHeight: " + chris.getHeight());

        Daniel daniel = new Daniel("Daniel", 27, 1.71);
        System.out.println("danielName: " + daniel.getName() + ", danielAge:" + daniel.getAge() + ", danielHeight:" + daniel.getHeight())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！

现在我们的主`class`比以前短了。

但是我们还需要做一件事...

如果你真的想一想，我们基本上是在做同样的逻辑在我们的克里斯和丹尼尔里面🤔。

这意味着我们在这个特殊的例子中没有使用正确的抽象。为了找到解决方案，我们需要知道克里斯和丹尼尔是什么...

没错！他们都是`Person`。

因此，如果我们删除丹尼尔`class`并将克里斯`class`重命名为“人”，我们的代码将变成:

```
// Java

public class Main {
    public static void main(String[] args) {
        Person chris = new Person("Chris", 23, 1.85);
        System.out.println("chrisName: " + chris.getName() + ", chrisAge: " + chris.getAge() + ", chrisHeight: " + chris.getHeight());

        Person daniel = new Person("Daniel", 27, 1.71);
        System.out.println("danielName: " + daniel.getName() + ", danielAge:" + daniel.getAge() + ", danielHeight:" + daniel.getHeight())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我们得到了什么好处？

*   我们的代码现在更短了，但没有失去它的意义。
*   我们将 2 个新`classes`的需求减少到 1 个。
*   我们减少了过多重复“克里斯”和“丹尼尔”的噪音。
*   我们设法在单个`class`中重用我们的逻辑。
*   现在我们可以很快知道`chris`和`daniel`都是同一个类的具体实现，或者我喜欢怎么称呼它们:兄弟。
*   现在我们甚至可以在其他项目中使用这个人，它仍然会工作得很好。

# 最后的话

读者先生/夫人，我希望这个例子可以帮助您理清关于什么是类以及为什么我们在面向对象编程中使用它们的思路:)

下期帖子再见！

# 奖励提示

这个来自于 [@alphashuro](https://dev.to/alphashuro) 下面的评论:另一个好处是，现在我们可以通过创建一个接受`Person`对象的函数来替换我们代码中打印`Person`信息的部分，就像这样:

```
// Java

public class Main {
    public static void main(String[] args) {
        Person chris = new Person("Chris", 23, 1.85);
        printPersonalInfo(chris);

        Person daniel = new Person("Daniel", 27, 1.71);
        printPersonalInfo(daniel);
    }

    public static void printPersonalInfo(Person person) {
        System.out.println("name: " + person.getName() + ", age:" + person.getAge() + ", height:" + person.getHeight());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过做这个“小”的改变，我们得到的好处是不必维护两行不同的代码。我们的代码中只有一个地方需要修改，以防我们需要以不同的方式呈现某人的信息。

感谢阿尔法提出这个问题。

现在，我要对我们的代码做另一个调整。

因为`printPersonalInfo()`函数只接受`Person`对象，这意味着这个方法直接依赖于`Person`类。也就是说，它实际上应该是其中的一部分！

所以让我们继续把我们的函数移到`Person`类，而不是把它放在我们的`Main` :
里

```
// Java

public class Person {
    private String name;
    private int age;
    private double height;

    public Person(String name, int age, double height) {
        this.name = name;
        this.age = age;
        this.height = height;
    }

    // Imagine we still have the getters here :P
    // this is just to make the code block shorter.

    public void printInfo() {
        System.out.println("name: " + name + ", age:" + age + ", height:" + height);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可能已经注意到我必须做一些调整:

*   移除`person`参数。
*   替换每个 Getter 方法调用的全局变量。
*   将方法从“printPersonalInfo()”重命名为“printInfo()”。

最后一点可以是可选的，也可以是个人喜好。就我个人而言，我发现名字中的“个人”部分有点多余，因为我们知道我们稍后会创建一个`Person`对象。

好了，现在我们也必须用这个新的实现对我们的`Main`类做一些调整:

```
// Java

public class Main {
    public static void main(String[] args) {
        Person chris = new Person("Chris", 23, 1.85);
        chris.printInfo();

        Person daniel = new Person("Daniel", 27, 1.71);
        daniel.printInfo();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想一想，我们甚至不需要 Getter 方法，这种做事情的方式与 OOP 的原则之一“封装”有关，这是另一篇文章的主题😉。

🎊如果你读完整本书，你会得到加分！🎉