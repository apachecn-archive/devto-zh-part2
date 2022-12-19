# Java 面向对象编程

> 原文：<https://dev.to/wathsara/java-object-oriented-programing-oop-32ao>

OOP 是什么？面向对象编程(OOP)是一种围绕对象构建的软件编程模型。该模型将数据划分为对象(数据字段)，并通过声明类(方法)来描述对象内容和行为。面向对象的编程特性包括以下内容。
1。封装:这使得程序结构更容易管理，因为每个对象的实现和状态都隐藏在明确定义的边界后面。*
2。多态性:这意味着抽象实体以多种方式实现。*
3。继承:这是指实现片段的层次安排。*
面向对象编程允许简化编程。它的好处包括可重用性、重构、可扩展性、可维护性和效率。
OOP 中的类是什么？在面向对象编程中，类是创建对象(特定的数据结构)、提供状态初始值(成员变量或属性)和行为实现(成员函数或方法)的蓝图。Java 为我们提供了自己的一套预定义类，但我们也可以自由地创建自己的自定义类。

```
class Animal{

} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子创建了一个 Animal 类。因此，我们可以宣布动物的行为和特征。
OOP 中什么是构造函数？构造函数是面向对象编程中类或结构的一种特殊方法，用于初始化该类型的对象。构造函数是一种实例方法，通常与类同名，可用于将对象成员的值设置为默认值或用户定义的值。在 Java 中如果我们不创建一个类构造函数，Java 提供了一个不允许你设置初始信息的构造函数。

```
class Animal {
    Animal(){
     }

} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们创建了 Animal 构造函数。创建 Animal 类实例时可以使用这个构造函数。我们也可以创建带参数的构造函数。

```
class Animal {
    int legs;

    Animal(int a){
        legs =a;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们用 int 数据类型创建了名为 legs 的实例变量。我们还创建了一个带参数的构造函数，它允许用指定的属性创建数据类型。我们在类构造函数中添加一个名为 a 的 int 参数。分支的值等于我们第一次使用这个类构造函数时指定的 int 值。让我们学习一下 Java 中的 main 方法。public static void main(String[]args)Java main 方法是任何 Java 程序的入口点。它的语法总是 public static void main(String[]args)。您只能更改字符串数组参数的名称，例如，您可以根据需要将 args 更改为 s、myString 或变量。

```
 public class Animal {
    int legs;
    Animal(int a ){
    }

    public static void main(String[] args) {

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

当 Java 运行你的程序时，main 方法中的代码被执行。
Public 在 Main 方法中有什么用？这是 main 方法的访问修饰符。它必须是公共的，这样 java 运行时才能执行这个方法。请记住，如果您将任何方法设为非公共的，那么它就不允许被任何程序执行，会有一些访问限制。这意味着 main 方法必须是公共的。让我们看看如果我们将 main 方法定义为非 public 会发生什么。
静态在 Main 方法中有什么用？当 java 运行时启动时，不存在该类的对象。这就是为什么 main 方法必须是静态的，以便 JVM 可以将类加载到内存中并调用 main 方法。如果 main 方法不是静态的，JVM 将不能调用它，因为不存在该类的对象。让我们看看当我们从 java main 方法中移除 static 时会发生什么。
主法中的虚空是什么？Java 编程要求每个方法签名都提供返回类型。Java main 方法不返回任何东西，这就是为什么它的返回类型是 void。这样做是为了保持简单，因为一旦 main 方法执行完毕，Java 程序就会终止。所以返回什么都没有意义，Java 虚拟机对返回的对象什么都做不了。如果我们试图从 main 方法返回一些东西，它会给出编译错误作为意外的返回值。
OOP 中的对象是什么？在基于类的面向对象编程范例中，“对象”是指一个类的特定实例，其中对象可以是变量、函数和数据结构的组合。下面是一段代码，展示了如何用 Java 创建一个类对象。

```
public class Animal {

    int legs;
    Animal(int a ){
        legs=a;

    }
    public static void main(String[] args) {
        Animal x = new Animal(4);

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们创建了一个名为 x 的动物对象。在创建 x 时，我们使用了类构造函数，并为所需的 int 参数 a 指定了一个值。在这里，动物 x 的腿数是 4。
什么是方法？面向对象编程(OOP)中的方法是与消息和对象相关联的过程。一个对象主要由数据和行为组成，它们形成了一个对象呈现给外界的接口。方法是一组预定义的指令。方法是在类中声明的。Java 提供了一些所有类都可用的预定义方法，但是我们也可以创建自己的方法。

```
public class Animal {

    int legs;
    Animal(int a ){
        legs=a;

    }

    void run(){

    }

    public static void main(String[] args) {
        Animal x = new Animal(10);

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们添加了一个名为 ruc 的方法。当该方法被使用时，它将打印出动物能跑。所以现在的问题是如何在 Java 中使用该方法。为了使用这个方法，我们使用之前创建的对象。所以我们必须使用 main 方法中的对象来调用 run 方法。下面给出的代码显示了如何做到这一点。

```
public class Animal {

    int legs;
    Animal(int a ){
        legs=a;

    }

    void run(){
        System.out.println("Animals Can Run");

    }
    public static void main(String[] args) {
        Animal x = new Animal(10);
        x.run();

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

方法也像构造函数，我们可以用参数来创建它们。
举个例子

```
public class Animal {

    int legs;
    Animal(int a ){
        legs=a;

    }

    void run(int speed){
        System.out.println("Animals Can Run with the speed of "+speed);

    }
    public static void main(String[] args) {
        Animal x = new Animal(10);
        x.run(50);

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这将给出动物能以 50 的速度奔跑时的输出。面向对象程序设计中的一个主要概念是继承。什么是继承？继承指的是 Java 编程的一个特性，它允许您创建从其他类派生的类。基于另一个类的类继承另一个类。被继承的类是父类、基类或超类。

```
 public class Animal {

    int legs;
    Animal(int a){
        legs=a;

    }

    void run(int speed){
        System.out.println("Animals Can Run with the speed of "+speed);

    }
    public static void main(String[] args) {
        Animal x = new Animal(10);
        x.run(50);

    }

}

class Dog extends Animal{

    public Dog(int a) {
        super(a);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，extends 关键字用于指示 Dog 类继承了 Animal 类中定义的行为。这是有道理的，因为狗是一种动物。在这里，我们必须创建 dog 类的构造函数，因为 Dog 类的超类有一个 int 参数构造函数。