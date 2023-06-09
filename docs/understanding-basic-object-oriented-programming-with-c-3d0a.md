# 用 C++理解基本的面向对象编程

> 原文：<https://dev.to/ichtrojan/understanding-basic-object-oriented-programming-with-c-3d0a>

## 先决条件

*   安装在您机器上的 C++编译器
*   对 C++有基本的了解
*   IDE 或[文本编辑器](https://atom.io)
*   时间
*   开放思想

## 简介

面向对象编程是一种在代码中表示现实生活中的对象或实体的编程范式，对于初学者来说，在 OOP 中有两个基本但重要的概念你必须理解，即**类**和**对象**。

## 什么是阶级？

类是指定对象属性和行为的蓝图。假设我们要创建一个新的类人类，我们可以指定一些人类特有的属性。

```
...
class human {
  public:
    string name;
    int age;
};
... 
```

在上面的代码片段中，我们创建了一个具有属性`name`和`age`的类。

## 什么是物体？

一个对象是一个类的一个实例，从某种意义上说，它可以从一个类中获得任何属性。为了说明问题，我们人类是我们基因的实例。

使用我们之前创建的类，我们可以从它创建一个对象，然后给属性`name`和`age`赋值。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;
};

int main () {
  human exhibitA;

  exhibitA.name = "Michael";
  exhibitA.age = 25;

  cout <<"Exhibit A's Name is "<< exhibitA.name << endl;
  cout <<"Exhibit A's Age is "<< exhibitA.age << endl;

  return 0;
} 
```

在上面的代码片段中，我们成功地创建了一个类，还创建了一个对象，并从父类中为该对象分配了属性。

[![object created](img/6a7653d7d35b01168d7aeba0e3506661.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z15-p1qk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538312314/Screenshot_2018-09-30_at_1.57.45_PM_mb0z8z.png)

至此，您应该已经了解了类和对象。请注意，一个类不仅限于一个对象，您可以创建任意多的对象。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;
};

int main () {
  human exhibitA;
  human exhibitB;

  exhibitA.name = "Michael";
  exhibitA.age = 25;

  exhibitB.name = "Nerfetiti";
  exhibitB.age = 24;

  cout <<"Exhibit A's Name is "<< exhibitA.name << endl;
  cout <<"Exhibit A's Age is "<< exhibitA.age << endl;

  cout <<"Exhibit B's Name is "<< exhibitB.name << endl;
  cout <<"Exhibit B's Age is "<< exhibitB.age << endl;

  return 0;
} 
```

如上面的代码片段所示，创建了一个新对象，并按照父类中的定义为其分配了属性。

[![Two objects created](img/0260e7859a883d0a341888a5cc51ec2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KB8VV4Pn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538312453/Screenshot_2018-09-30_at_1.59.54_PM_nrejof.png)

## 什么是行为？

我们要研究的另一个概念是行为，行为是对象可以执行的动作。例如，一个人可以跑、吃、睡等等。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;

    void run () {
      cout << name <<" is running" << endl;
    }
};

int main () {
  human exhibitA;

  exhibitA.name = "Michael";  
  exhibitA.run();

  return 0;
} 
```

[![Output](img/14d47ea9ec752ef6869965d6d13089ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E6WrNyRr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538312115/Screenshot_2018-09-30_at_1.51.45_PM_bn7aba.png)

如上面的代码片段所示，创建了一个函数来传递 public name 变量以打印“Michael 正在运行”。基于分配给该函数/行为的对象，它将通过点符号传递该函数/行为所需的参数。

## 什么是重载？

重载是一个概念，它允许您使用相同的函数名，根据传递给它的参数来执行不同的操作。对于这个例子，我们将创建一个新的行为`eat()`，没有参数，有一个参数和两个参数。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;

    void eat () {
      cout << name <<" is eating" << endl;
    }
};

int main () {
  human exhibitA;

  exhibitA.name = "Michael";
  exhibitA.eat();

  return 0;
} 
```

如上所示，我们创建了一个名为`eat`的行为，没有参数，它应该返回“Michael 正在吃饭”或者您声明为`exhibitA` name 的任何名称。

[![Michael is eating](img/c4d0360b28b70cdc1aaa3838077a3a35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bd8IZx-T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538313193/Screenshot_2018-09-30_at_2.12.41_PM_jntguj.png)

接下来，我们将通过为另一个名为 eat 的行为要求一个参数来重载`eat`行为。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;

    void eat () {
      cout << name <<" is eating" << endl;
    }

    void eat (string food1) {
      cout << name <<" is eating "<< food1 << endl;
    }
};

int main () {
  human exhibitA;

  exhibitA.name = "Michael";
  exhibitA.eat();
  exhibitA.eat("Rice");

  return 0;
} 
```

上面的代码片段有第二个`eat`函数，但是需要一个参数，这个参数被传递以形成基于分配给它的对象的“Michael 正在吃米饭”。

[![Michael is eating rice](img/6b206de83ef3db8e8a75ce5a842bd841.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tUl_SlSg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538313662/Screenshot_2018-09-30_at_2.20.29_PM_1_vbvi8k.png)

现在，最后让我们创建一个新的接受两个参数的`eat`行为。

```
#include <iostream> using namespace std;

class human {
  public:
    string name;
    int age;

    void eat () {
      cout << name <<" is eating" << endl;
    }

    void eat (string food1) {
      cout << name <<" is eating "<< food1 << endl;
    }

    void eat (string food1, string food2) {
      cout << name <<" is eating "<< food1 << " and " << food2 << endl;
    }
};

int main () {
  human exhibitA;

  exhibitA.name = "Michael";
  exhibitA.eat();
  exhibitA.eat("Rice");
  exhibitA.eat("Rice", "Beans");

  return 0;
} 
```

这个代码片段的最终输出是“Michael 正在吃米饭和豆子”，最后一个`eat`调用触发了重载的`eat`行为，在 human 类中声明了两个参数，因为我们正好传递了两个参数。

[![two parameters](img/27d5b4b634d5c5d5253380d356fe2df5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VQmGFCbR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ichtrojan/image/upload/v1538314708/Screenshot_2018-09-30_at_2.37.07_PM_ybmmsu.png)

如果您注意到声明的所有其他`eat`行为仍然有效，不管在此之前或之后声明的其他`eat`行为。

## 结论

到目前为止，您应该对面向对象编程的工作原理有所了解，但不仅仅是在 C++中，这个概念也适用于其他编程语言，只是语法不同。我错过了一些概念，比如继承、多态、数据抽象和接口。我会在另一篇文章《玩得开心》中介绍这些概念。