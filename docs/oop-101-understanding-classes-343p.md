# OOP 101:理解类

> 原文：<https://dev.to/lefebvre/oop-101-understanding-classes-343p>

最简单的形式是，类是代码和数据的容器，就像模块一样。但是与模块不同，类提供了更好的代码重用。类是面向对象编程的基本构件。
课程提供许多好处，包括:

*   **可重用代码**:当你直接添加代码到一个窗口上的按钮来定制它的行为时，你只能使用那个按钮的代码。如果您想对另一个按钮使用相同的代码，您需要复制代码，然后在代码引用原始按钮的情况下对代码进行更改(因为新按钮将具有与原始按钮不同的名称)。类只存储一次代码，并一般地引用对象(如按钮),这样相同的代码无需修改就可以重复使用任意次。如果您基于按钮控件创建一个类，然后将您的代码添加到该类中，则该自定义类的任何用法(实例)都将具有该代码。
*   **更小的项目和应用程序**:因为类允许你存储一次代码，并在一个项目中反复使用，所以你的项目和最终的应用程序更小，需要的内存也更少。
*   代码维护更容易:代码越少，维护就越少。如果您在应用程序的几个地方复制了基本相同的代码，那么在进行更改或修复 bug 时，您必须记住这一点。通过存储代码的一个副本，当您需要进行更改时，您将花费更少的时间来跟踪项目中所有使用相同代码的地方。对类中代码的更改会自动应用到使用该类的任何地方。更容易调试代码越少，需要调试的代码就越少。
*   更多的控制:类给了你更多的控制，这是你仅仅通过在窗口中的控件的事件处理程序中添加代码所不能得到的。您可以使用类来创建自定义控件。对于类，您可以选择创建不允许访问类源代码的版本，允许您创建可以共享或出售给其他人的类。

## 了解类、实例和引用

在项目中使用类之前，理解这三个概念之间的区别很重要:类本身、类的实例和对类的引用。

*   **类**:把类想象成一个信息(代码和数据)容器的模板，很像一个模块。像一个模块一样，每个类在你的项目中只存在一次。但是与模块不同，一个类可以有多个实例，每个实例包含不同的数据。
*   参考文献:类提供了更好的代码重用，因为有一个叫做实例的概念。与应用程序中只存在一次的模块不同，一个类可以有多个实例。每个实例(也称为对象)都是该类及其所有方法和属性的独立副本。

### 上课

Xojo 有许多用于用户界面控件的内置类，例如 PushButton、WebButton、iOSButton、Label、WebLabel、iOSLabel、TextField、WebTextField、iOSTextField、ListBox、WebListBox 和 iOSTable 等等。

控件类本身并不那么有用；它们只是抽象的模板。但是每次你添加一个控件类到一个布局中(比如一个窗口，网页或者视图)你会得到一个类的实例。因为每个按钮都是一个单独的实例，所以这允许您在一个窗口中有多个按钮，每个按钮都完全相互独立，有自己的设置和属性值。

这是一个将在后面的示例中使用的车辆类示例:

```
Class Vehicle
  Property Brand As String
  Property Model As String
End Class 
```

Enter fullscreen mode Exit fullscreen mode

### 实例

这些实例是您在窗口上编写代码时与之交互的对象，也是用户在使用您的应用程序时与之交互的对象。

例如，当您将 TextArea 从“资源库”拖到窗口时，您会在窗口上创建一个可用的 TextArea 实例。新实例具有内置于 TextArea 类中的所有属性和方法。所有这些都是免费的——样式化的文本、多行、滚动条等等。您可以通过修改实例的属性值来自定义 TextArea 的特定实例。

当您将控件添加到布局中时，布局编辑器会自动为您创建引用(它是控件的名称)。然而，在编写代码时，你使用 New 关键字创建类的实例。例如，这将在您项目中创建一个车辆类的新实例:

```
Dim car As New Vehicle 
```

Enter fullscreen mode Exit fullscreen mode

### 引用

引用是引用类的实例的变量或属性。在上面的代码示例中，car 变量是对 Vehicle 类实例的引用。

在您的代码中，您使用点标记法与类的属性和方法进行交互，如下所示:

```
Dim car As New Vehicle
car.Brand = "Ford"
car.Model = "Focus" 
```

Enter fullscreen mode Exit fullscreen mode

在这里，品牌和型号被定义为车辆类的属性，它们被赋予特定实例的值。拥有一个实例很重要！如果你试图在没有实例的情况下访问一个类的属性或方法，你会得到一个 NilObjectException，这可能会导致你的应用程序退出。这是人们最常犯的编程错误之一，通常是因为忘记了 New 关键字。例如，这段代码乍一看可能没问题:

```
Dim car As Vehicle
car.Brand = "Ford"
car.Model = "Focus" 
```

Enter fullscreen mode Exit fullscreen mode

但是第二行将导致 NilObjectException，因为 car 实际上不是一个实例。它只是一个被声明为最终包含车辆实例的变量。但是因为实例没有被创建，所以它得到默认值 Nil，这意味着它是空的或者未定义的。

如果不确定变量是否包含实例，可以在使用前检查它:

```
If car <> Nil Then
  car.Brand = "Ford"
  car.Model = "Focus"
End If 
```

Enter fullscreen mode Exit fullscreen mode

当变量不为零时，它有一个有效的引用。因为它是一个引用，所以在将一个变量赋给另一个变量时，有一些重要的考虑:当你从一个引用变量赋给另一个引用变量时，第二个变量与第一个变量指向同一个引用。

这意味着第二个变量引用了与第一个变量相同的类实例，而不是它的副本。因此，如果您用其中一个变量更改类的属性，那么这两个变量的属性都会更改。一个例子可能会有所帮助:

```
Dim car As New Vehicle
car.Brand = "Ford"
car.Model = "Focus"
Dim truck As Vehicle
truck = car
// truck.Model is now "Focus"
car.Model = "Mustang"
// truck.Model is now also "Mustang"
truck.Model = "F-150"
// car.Model is now also "F-150" 
```

Enter fullscreen mode Exit fullscreen mode

在此图中，您可以看到 car 和 truck 的变量指向同一个 Vehicle 实例。所以改变其中任何一个都会有效地改变两者。

[![](../Images/8410af07653cacfead8225f555d218b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DG50nQqL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dzf8vqv24eqhg.cloudfront.net/userfiles/1539/2321/ckfinder/images/OOPSameInstance.png%3Fdc%3D201509081753-26)

如果您想要创建一个类的副本，您需要创建一个新的实例(使用 new 关键字),然后复制它的单个属性，如下所示:

```
Dim car As New Vehicle
car.Brand = "Ford"
car.Model = "Focus"
Dim truck As New Vehicle
truck.Brand = car.Brand
trunk.Model = car.Model
// truck.Model is now also "Focus"
truck.Model = "F-150"
// car.Model remains "Focus" 
```

Enter fullscreen mode Exit fullscreen mode

当你这样做的时候，你会得到两个独立的实例。正如您在这些图表中看到的，对其中一个的更改不会影响另一个:

[![](../Images/3bf33fc834c946254d0b68bc580b3f3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EieD3wHR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dzf8vqv24eqhg.cloudfront.net/userfiles/1539/2321/ckfinder/images/OOPDiffInstances.png%3Fdc%3D201509081756-40)

## 向项目中添加类

在 Xojo 中，向项目添加一个类很容易。要添加新类，请单击工具栏上的“插入”按钮，然后选择“类”或从“插入”菜单中选择“类”。这将向导航器添加一个新类，使用默认名称(Class1 表示第一个类)。

使用检查器来更改类别的名称。像模块一样，类主要包含属性和方法。但是它们也可以包含许多其他东西，比如常量、枚举、事件和结构。

## 给类添加属性

属性是属于整个类实例而不仅仅是单个方法的变量。要将属性添加到类中，请使用代码编辑器工具栏上的“添加”按钮，从菜单、上下文菜单或键盘快捷键(在 macOS 上为 Option-Command-P，在 Windows 和 Linux 上为 Ctrl+Shift+P)中插入↠属性。您可以使用检查器设置属性名称、类型、默认值和范围。

要快速创建属性，可以在 name 字段的一行中输入它的名称和类型，如下所示:PropertyName 作为数据类型。当您离开该字段时，类型将在“类型”字段中设置。

以这种方式添加的属性有时称为实例属性，因为它们只能用于类的实例。您还可以添加无需使用实例即可通过类本身访问的属性。这些被称为共享属性。

### 共享属性

共享属性(有时称为类属性)类似于“常规”属性，只是它属于类，而不是类的实例。共享属性是全局的，可以从其范围允许的任何地方访问。在许多方面，它像模块属性一样工作。

重要的是要明白，如果您更改了共享属性的值，该更改将适用于共享属性的每次使用。

一般来说，共享属性是一种高级特性，只有在特殊情况下才需要。例如，如果您使用一个类的实例来跟踪项目(例如，人员、商品、销售交易等)，您可以使用一个共享属性作为计数器。每次创建或销毁类的实例时，都可以在其构造函数中增加共享属性的值，并在其析构函数中减少该值。(有关构造函数和析构函数的信息，请参见构造函数和析构函数一节。)当您访问它时，它会给出该类的当前实例数。

## 向类添加方法

要向类中添加方法，请使用代码编辑器工具栏上的“添加”按钮，从菜单、上下文菜单
或键盘快捷键(在 macOS 上为 Option-Command-M，在 Windows 和 Linux 上为 Ctrl+Shift+M)中插入↠方法。您可以使用检查器设置方法名称、参数、返回类型和范围。

当键入方法名时，字段将自动补全其超类中任何方法的名称。

以这种方式添加的方法称为实例方法，因为它们只能用于类的实例。

您还可以添加可通过类本身访问的方法。这些被称为共享方法。

### 共享方法

共享方法(有时称为类方法)类似于普通方法，只是它属于类，而不是类的实例。共享方法是全局的，可以从其作用域允许的任何地方调用。在许多方面，它像模块方法一样工作。

共享方法不知道实例，所以它的代码只能访问类的其他共享方法或共享属性。

一般来说，共享方法是一种高级特性，只有在特殊情况下才需要。共享方法的一个常见用法是创建一个实例(而不是依赖于构造函数)。

了解更多关于 Xojo 的信息，这是为 Windows、Mac、Linux、iOS 和网络制作应用的快捷方式。