# 构建 PHP 框架:第 6 部分——依赖反转，控制反转，天啊！

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-6---dependency-inversion-inversion-of-control-oh-my-58>

本系列的第 5 部分讨论了测试驱动开发(TDD)。今天，我将讨论依赖反转、控制反转、依赖注入以及其他相关主题。

## 什么是依赖注入？

我们将从依赖注入开始，因为它将引导我们进入其他主题。[维基百科](https://en.wikipedia.org/wiki/Dependency_injection)告诉我们:

> 在软件工程中，依赖注入是一种技术，其中一个对象(或静态方法)提供另一个对象的依赖。

有没有写过类似这样的代码:

```
<?php

class Superhero 
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

class ComicBook
{
    public $mainCharacter;

    public function __construct(Superhero $superhero) 
    {
        $this->mainCharacter = $superhero;
    }
}

$superhero = new Superhero('Caffeine Man');
$comic = new ComicBook($superhero); 
```

Enter fullscreen mode Exit fullscreen mode

这就是依赖注入的作用。我们的`ComicBook`对象依赖于我们的`Superhero`对象，所以我们注入它。明白了吗？

## 什么是依存倒置？

漫画书不总是关于超级英雄的。如果我们想要一本漫画，它的主角是一只狗或者一只吸血鬼或者一只吸血的狗，会怎么样？让我们重新编写代码，允许使用任何类型的字符。

```
<?php

interface CharacterInterface
{
    /**
     * All classes implementing this interface must provide a getName method.
     */
    public function getName() : string;
}

abstract class Character implements CharacterInterface
{
    public $name;

    public function getName() : string
    {
        return $this->name;
    }
}

class Superhero extends Character
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

class VampiricDog extends Character
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

class ComicBook
{
    public $mainCharacter;

    public function __construct(CharacterInterface $character)
    {
        $this->mainCharacter = $character;
    }
}

$superhero = new Superhero('Caffeine Man');
$superheroComic = new ComicBook($superhero);

var_dump($superheroComic->mainCharacter->getName());

$vampireDog = new VampiricDog('Mr. Fangz');
$vampireComic = new ComicBook($vampireDog);

var_dump($vampireComic->mainCharacter->getName()); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在编写一个界面，所以我们现在可以创建任何类型的角色用于漫画书。我们已经将角色实现与漫画实现完全分离。想想看——一本漫画书只是想成为一本漫画书，并做漫画里的事情。它不在乎你赋予它什么样的性格，它只知道自己需要一个。一旦角色就位，漫画书就可以继续做漫画书的工作了。[换句话说](https://en.wikipedia.org/wiki/Dependency_inversion_principle):

> A.高级模块【漫画】不要依赖低级模块【超级英雄】。两者都应该依赖于抽象[特征接口]。抽象不应该依赖于细节。细节应该依赖于抽象。

## 什么是控制反转(IoC)？

控制反转有时被称为“好莱坞法则”。就像“不要打电话给我们，我们会打电话给你。” [Brett Schuchert](https://martinfowler.com/articles/dipInTheWild.html) 用游戏《大富翁》来解释这个概念:

> 想象一下由许多玩家创建的垄断系统。游戏协调玩家之间的互动。当轮到玩家时，游戏可能会询问玩家是否有任何移动前的动作，如出售房屋或酒店，然后游戏将根据掷骰子来移动玩家(在现实世界中，物理玩家掷骰子并移动他或她的令牌，但这是棋盘游戏的人工产物，而不是计算机-也就是说，这是对正在发生的事情的现象学描述，而不是本体论描述)。请注意，游戏知道玩家何时可以做出决定，并相应地提示玩家，而不是玩家做出决定。

在传统的过程化编程中，步骤是一个接一个地进行的(做这个，然后做这个，然后做这个)。当一个人开始垄断时，他们就控制了:

1.  购买酒店。
2.  掷骰子。
3.  移动到新的地点。

如果游戏由电脑控制，它看起来会更像:

```
Computer: Would you like to make a purchase? 
Human: I would like to buy a hotel. 
Computer: Great, here's your hotel. Do you want to make additional purchases? Human: No. 
Computer: Please roll the dice and move to a new spot. 
```

Enter fullscreen mode Exit fullscreen mode

游戏的流程完全颠倒了。

## 什么是 IoC 容器？

IoC 容器有点用词不当。更好更准确的名字是依赖注入容器(DiC)。这两个术语总是可以互换使用。无论如何，DiC 负责管理对象——从如何创建对象到如何配置对象。框架使用容器在运行时注入依赖关系。

## 这和一个框架有什么关系？

实际上，很多。当你使用一个框架时，你实际上是在给它对你的应用程序的控制权。此外，IoC/DiC 容器为大多数框架奠定了基础。我已经开始分析容器的工作，请随时关注 Github 的进展。一旦完成，我将深入研究它的工作原理和功能。

## 进一步阅读

[反转控制容器和依赖注入模式](https://www.martinfowler.com/articles/injection.html)
[什么是依赖注入？](http://fabien.potencier.org/what-is-dependency-injection.html)
[DI，&服务定位器 Redux](http://ralphschindler.com/2012/10/10/di-dic-service-locator-redux)
[浸野](https://martinfowler.com/articles/dipInTheWild.html)
[InversionOfControl](https://martinfowler.com/bliki/InversionOfControl.html)

最后一件事，请务必查看我的时事通讯！每周我都会给你发一封很棒的电子邮件，里面有更新、很棒的链接、提示&技巧和其他非开发人员的随机信息。如果你感兴趣，你可以通过点击这个[链接](http://eepurl.com/dvwlM5)来注册。

[最初发布于 DevelopmentMatt.com](http://developmentmatt.com/building-a-php-framework-part-6-dependency-inversion-inversion-of-control-oh-my/)