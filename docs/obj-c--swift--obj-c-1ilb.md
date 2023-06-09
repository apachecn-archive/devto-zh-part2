# 斯威夫特

> 原文：<https://dev.to/itscoderslife/obj-c--swift--obj-c-1ilb>

这是人们可能遇到的罕见情况之一，但这个问题及其解决方案是独一无二的，也是这里最简单的。

*   您是否遇到过这样的场景，您觉得需要在框架内的类中添加一个额外的方法(您添加的一个 pod)来完成您的工作？
*   这个库是用 Objective-C 实现的吗？
*   您的遗留代码是否在 Objective-C 中，而现在您正战略性地转向 Swift？

今天上班时，我的朋友维诺德问我这个问题:

> “我有一个在 Objective-C 中的 pod 框架，我需要添加一个功能，但现在需要在 Obj-C 中使用，将来可能会在 Swift 文件中使用。做这件事的最好方法是什么？

假设我们在一个 pod 框架中有一个名为 Person 的类。这个 Person 类有*姓名*、*年龄*和*性别*作为属性，带有一个格式化姓名的函数。

```
// Objective-C header file of Person class #import typedef enum : NSUInteger { male, female } Gender; @interface Person : NSObject @property (nonatomic, strong) NSString \*name; @property (nonatomic, strong) Gender \*gender; @property (nonatomic, strong) NSUInteger \*age; - (NSString\*)formattedName; @end // Objective-C Implementation of Person class @implementation Person - (NSString\*)formattedName { return [NSString stringWithFormat:"This is %@",self.name]; } @end 
```

Enter fullscreen mode Exit fullscreen mode

当对 name 属性的值为“Vinodh”的 person 对象调用 formattedTitle 方法时，它将显示为“This is Vinodh”。

现在我们需要用另一种方法扩展这个 Person 类，比如一种新的格式，它告诉年龄和姓名。

在经典的 Objective-C 风格中，它将向 Person 类添加一个类别，在任何需要这个新方法的地方导入这个类别。在推出 Swift 之前，这可能是最好的方式。

但是如果新开发人员加入 unitl，就会有一个缺点，除非他导入 category 头，否则代码完成不会显示新方法。很可能新的开发人员会再次编写相同的方法来创建新的类别。这是一个问题。

迅速营救。请执行以下操作:

*   创建一个 Person 类扩展并编写所需的方法
*   现在创建一个协议并声明方法签名
*   现在给协议加上前缀`@objc`,使其在 Obj-C 文件中可用

这是你怎么做的。

```
@objc protocol Formats { func getFormattedName(with ageHidden: Boolean) -\> String } extension Person: Formats { func formattedName(with ageHidden: Boolean) -\> String { let formattedString: String = ageHidden ? "This is \(self.name)" : "This is \(self.name) aged \(self.age) years." return formattedString } } 
```

Enter fullscreen mode Exit fullscreen mode

现在，无论你在 Obj-C 中的什么地方键入`[personObject form` …或者在 Swift 中的`personObject.form` …代码 complete 将向你展示我们添加的扩展中的两个方法，一个人类另一个人类。

喜欢听你讲更多的故事。请在评论中分享。

编码快乐！