# 揭开虚拟和抽象函数的神秘面纱

> 原文：<https://dev.to/codemouse92/demystifying-virtual-and-abstract-functions--73g>

**编辑:**经过一番精彩的评论后，我对我的原始代码做了一些修改:(1) `virtual`析构函数，以及(2)`override`和`final`关键字的使用。

* * *

你有没有注意到 C++的继承系统以一种你意想不到的方式运行？也许基类的函数一直被调用，而你不知道如何调用派生类的函数。或者你可能在类定义中遇到了一些奇怪的代码，比如`virtual`和`=0;`。

这些都与**虚拟遗产**有关，它远没有第一眼看上去那么可怕！让我们创建一个基本的例子来演示`virtual`是怎么回事，以及它为什么如此棒。

假设我们有一个基类`Animal`，并且我们从它派生出一个新类`Dog`...

```
class Animal
{
public:
    Animal(){}

    void eat()
    {
        std::cout << "Nom nom nom" << std::endl;
    }

    void sit()
    {
        std::cout << "[stares blankly]" << std::endl;
    }

    void speak()
    {
        std::cout << "[undefined sound]" << std::endl;
    }

    ~Animal(){}
};

class Dog : public Animal
{
public:
    Dog(){}

    void sit()
    {
        std::cout << "[sits]" << std::endl;
    }

    void speak()
    {
        std::cout << "woof" << std::endl;
    }

    ~Dog(){}
};

class Cat : public Animal
{
public:
    Cat(){}

    virtual void sit()
    {
        std::cout << "[meows disdainfully and walks away]" << std::endl;
    }

    virtual void speak()
    {
        std::cout << "mew" << std::endl;
    }

    ~Cat(){}
};

int main()
{
    Dog* dog = new Dog();
    Cat* cat = new Cat();

    dog->eat();
    dog->sit();
    dog->speak();

    cat->eat();
    cat->sit();
    cat->speak();
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类将定义一个基本的动物，所以我们可以重用一些函数，比如所有动物共有的`eat()`。
然后我们覆盖`sit()`和`talk()`具体到`Dog`。这一切都工作得很好！

> 诺姆诺姆诺姆
> 
> 汪汪
> 诺姆诺姆诺姆
> 【轻蔑地喵喵叫着走开】
> 喵

太好了！这正是我们想要的。然而，继承的一个优点是我们可以像这样编写函数...

```
void makeAct(Animal* critter)
{
    critter->eat();
    critter ->sit();
    critter->speak();
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们没有编写两个(或更多)为每种动物做同样事情的函数，而是接受*基类*(或指向它的指针/引用)作为参数类型。然后，我们可以这样做...

```
int main()
{
    Dog* dog = new Dog();
    Cat* cat = new Cat();

    makeAct(dog);
    makeAct(cat);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是事情变得奇怪的地方！当我们运行这个时，我们得到...

> 姓名姓名〔姓名空白〕t1〔未定义声音〕T2〔姓名〕T3〔姓名空白〕T4〔未定义声音〕

哎呦喂。这不是我们要找的，对吧？我们的汪汪叫在哪里？为什么狗不坐，猫不叫？他们都表现得像无聊的下贱阶级！

这就是`virtual`函数派上用场的地方。默认情况下，编译器会在基类中查找函数定义。 **`virtual`告诉计算机查看*派生的*类来代替函数定义。**

与此同时还有**抽象**类，一种特殊类型的`virtual`函数，其**必须在派生类中**定义。要对此进行分解:

*   `virtual`函数*可能会被派生类*覆盖；将使用函数的派生版本，即使基类用作数据类型。

*   *抽象*或*纯虚拟*函数*必须*被派生类覆盖——它们甚至在基类中都没有定义。

所有动物都差不多，所以这没什么大不了的。然而，我们知道我们应该有选择地覆盖`sit()`；未经训练的动物*会*发呆，但有些会以特定的方式回应。

看看我们的类设计，我们也意识到在`Animal`中定义`speak()`是一个相当愚蠢的函数...打印“[未定义的声音]”只是看起来很傻。我们定义的任何动物都应该有声音，否则明确说类似“[没有声音]”的话。所以，我们就把这个*做成纯虚拟的*。

**设计原则:**显性优于隐性。换句话说，每种情况都应该在代码中有一些特别指定的动作(或失败)。这也是为什么我们在所有的类中显式定义空的构造函数和析构函数，而不是让编译器隐式定义它们。

所以，我们重写一下，让`sit()`是`virtual`，`speak()`是*纯虚拟*。

我们的基类也应该有一个虚析构函数。

```
class Animal
{
public:
    Animal(){}

    void eat()
    {
        std::cout << "Nom nom nom" << std::endl;
    }

    virtual void sit()
    {
        std::cout << "[stares blankly]" << std::endl;
    }

    virtual void speak() = 0;  // the `= 0;` literally means "not defined here"

    virtual ~Animal(){}
}; 
```

Enter fullscreen mode Exit fullscreen mode

同时，在派生类中，我们将`[override](http://en.cppreference.com/w/cpp/language/override)`关键字(C++11 和更高版本)添加到我们正在重写的每个函数中。如果我们试图重写一个非虚函数，这会给我们带来编译器错误。

```
class Dog : public Animal
{
public:
    Dog(){}

    void sit() override
    {
        std::cout << "[sits]" << std::endl;
    }

    void speak() override
    {
        std::cout << "woof" << std::endl;
    }

    ~Dog() override {}
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不打算以后覆盖这个函数，我们也可以使用关键字`[final](http://en.cppreference.com/w/cpp/language/final)`( c++ 11 和更高版本)来代替`override`。

狗可能会发出不同的声音，行为也不同，所以我们可能会从`Dog`中派生出来，为特定的品种分类。另一方面，猫基本上都会说“喵”，没有一只会为你而坐，所以没有必要进一步忽略它们！因此，对于`Cat`类，不使用`override`，让我们只使用`final`，以防止进一步覆盖！

```
class Cat : public Animal
{
public:
    Cat(){}

    void sit() final
    {
        std::cout << "[meows disdainfully and walks away]" << std::endl;
    }

    void speak() final
    {
        std::cout << "mew" << std::endl;
    }

    ~Cat() override {}
}; 
```

Enter fullscreen mode Exit fullscreen mode

稍后，如果我们试图在从`Cat`派生的类中覆盖`speak()`或`sit()`函数，我们会得到一个编译器错误。

现在让我们重新运行前面的代码。又来了，以防你忘了。我们没有改变这里的任何东西！

```
void makeAct(Animal* critter)
{
    critter->eat();
    critter ->sit();
    critter->speak();
}

int main()
{
    Dog* dog = new Dog();
    Cat* cat = new Cat();

    makeAct(dog);
    makeAct(cat);
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行它时，我们看到...

> 诺姆诺姆诺姆
> 
> 汪汪
> 诺姆诺姆诺姆
> 【轻蔑地喵喵叫着走开】
> 喵

好极了。现在一切都如我们所料。

现在，提醒一句:如果你定义一个函数为*纯虚拟*或*抽象*(即`virtual thefunc() = 0;`)，你必须在每个派生类中定义它。如果你不这样做，你会得到一个编译错误。可以说，这是纯虚函数的好处之一...编译器能够介入并阻止你做傻事。

就是这样！简单地允许你控制在类继承中调用函数的位置。现在没那么可怕了，是吗？