# 使用 ANT 实现面向对象编程

> 原文：<https://dev.to/jaybeekeeper/using-ant-to-grok-oop-18jn>

## OOO & OOP

当我读到 Ruby 是一种面向对象的编程语言时，我首先想到的是面向对象的本体。这篇博文的第一稿是一次孤注一掷的尝试，试图将 Ruby 对物体的概念与[布鲁诺·拉图尔的](https://en.wikipedia.org/wiki/Bruno_Latour) [演员网络理论](https://www.youtube.com/watch?v=X2YYxS6D-mI)联系起来——我从书架上取下了*的《重新组装社交*；我尽可能地翻阅了文本，试图挖掘中介和调停者的细节，而不是赋予人类行动者相对于物体的特权，重新学习五种不确定性是什么

这在很大程度上是一场灾难，尤其是因为 ANT 是出了名的迟钝和难以概括，而且我真的没有时间去重新学习拉图尔的本体论概念的细节；所以在这里，我不想直接比较，也不想冒被屠杀和误解布鲁诺·拉图尔本体论主张的风险，而是更关注于思想(或者说思想的思想)如何让我探究 Ruby 的面向对象范式对于理解对象以及它们之间通过其他对象的关系意味着什么。

## 一切都是物体

因此，在我早期使用 Ruby 的经历中，我多次遇到的一个概念是，Ruby 中的一切都是对象。取决于你与谁交谈，似乎有很小的例外(方法是对象吗？积木是物体吗？两者都可以用物体包裹？)，但它们似乎是证明规则而不是破坏规则的例外。

每个单一对象都是一个类的实例。甚至类的对象也在类的实例中。由于是一个特定种类的类，一个对象具有该类所特有的可以被调用的属性。

```
#even nil has a class!
nil.class
 => NilClass 
```

## 对象关系

因此，在像 Ruby 这样的面向对象的语言中，理解对象之间的关系是非常重要的——并且不存在一个对象优于另一个对象的情况。而对象之间的关系*必须*通过其他对象来理解。这是一个公平的竞争环境。因此，我们可能认为没有主体或所有权概念的物体，突然拥有了相对于其他物体的所有权。我想在这里与拉图尔的行动者概念进行比较:

> 为了摆脱所谓“形象社会学”的影响，ANT 使用了一个来自文学研究的技术词汇 actant。这里有四种方法可以弄清楚同一个 actant:‘帝国主义争取单边主义’；“美国希望退出联合国”；小布什希望退出联合国；许多军官和 24 名新保守派领导人希望退出联合国。第一个是结构特征，第二个是法人团体，第三个是个人，第四个是个人的松散集合，这当然对账户有很大的不同，但它们都提供了相同行为的不同形象。这四者中没有一个比其他的或多或少是“现实主义的”、“具体的”、“抽象的”或“人为的”。

拉图，布鲁诺，*重新组装社会*牛津大学出版社，2009 年，54 页

就我们的目的而言，作用物只是任何能够实现行动或能够成为行动支点的东西。

这指向了一个“广义对称”的概念，在这个概念中，在有意向行动的行动者(通常是“主体”)和因果关系的物质世界中，不存在“先验的不对称”。我们对所有参与者使用相同的分析框架，无论他们是人、计算机还是关于技术的想法。这可能会很快变得非常深奥，所以让我们深入了解我是如何理解 Ruby 中的并行的。

例如，如果我创建了一个具有属性“name”的类“Elevator ”,并且“is_functioning”是一个缺省为“true”的布尔值，那么我可以确定用该类初始化的任何东西都将具有这些属性。

```
class Elevator

    attr_reader :name
    attr_accessor :is_functioning

    def initialize(name)
        @name = name
        @is_functioning = true
    end
end

elevator_9 = Elevator.new("Elevator No. 9")
#<Elevator:0x00007ff5ce3bc998 @functioning=true, @name="Elevator No. 9">

elevator.name
# "Elevator No. 9" 

elevator.is_functioning
#true 
```

我还可以创建一个拥有自己属性集的类“Inspector”

```
class Inspector
    attr_reader :name

    def initialize(name, philosophy)
        @name = name
        @philosophy = philosophy
    end
end

lila_mae = Inspector.new("Lila Mae Watson", "Intuitionism")
#<Inspector:0x00007fccd6a0fdc0 @name="Lila Mae Watson", @philosophy="Intuitionism"> 
```

所以我有一个电梯班，和一个检查员班。守则本身并没有暗示直接的关系。但是面向对象的范例对于映射到真实世界的关系是有用的；这里的刺是，在现实世界中，检查员是一个有代理权的人，可以要求拥有某物的所有权，而电梯不能。但是对于 Ruby，就没有这种先验的不对称。两者都是对象(两者都是参与者)——所以我们使用相同的框架来理解它们。结果我们可以说，一个检查员“有许多”(或检查许多)电梯。而且一部电梯“有很多”(或者被很多)检查员检查。

更重要的是，我们也可以把两者作为一个对象来理解。这两个角色(类电梯和检查员的 Ruby 对象)形成了一个临时网络，它成为关系的集合——拉图尔的“actant”交互点，即“电梯检查”，也可以表示为一个 Ruby 对象。描绘下面的画面:

```
 class ElevatorInspection

    def initialize(elevator, inspector)
            @elevator = elevator
            @inspector = inspector
    end

end 
```

这里我们看到，ElevatorInspection 类的每个实例都是由 Elevator 和 Inspector 类的实例启动的。如果我们在类中定义了一个类方法。都是为了收集每个实例-

```
 class ElevatorInspection

    @@all = []

    def self.all
        @@all
    end

    def initialize(elevator, inspector)
            @elevator = elevator
            @inspector = inspector
            self.class.all << self
    end

end 
```

*   然后，我们可以在理论上理解类 Elevators 和 Inspectors 之间的整体关系，因为 ElevatorInspection 类的每个实例都包含来自其他两个类的信息。所以我们知道电梯有“许多”检查员*到*电梯检查等级，反之亦然。这个对象可以充当我们的[【真理的单一来源】](https://en.wikipedia.org/wiki/Single_source_of_truth)并且我认为平行是“行动者”，通过允许“所有权”的关系来修改电梯和检查员的行动者我们的整个集合可以被表现为一个对象，并且，像一个行动者一样，拥有一个只有这种关系才有的相关性。

### 是编码为本体吗？

如果我失去了你，或者这种相似性看起来很脆弱，我不会责怪你:但是我在绘制这些相似性时的关键是 Ruby 的面向对象的范例允许我们建模真实世界的关系——这些关系也可以根据对象来理解，因为我们不需要调整我们的分析框架。由于我们可以在 Ruby 中为任何给定的对象分配属性，因此只需简单地识别关系中的代表对象，我们就可以修改和操作该对象，以了解整个关系。我们将这种关系抽象为一个 Ruby 对象。

因此，让我们最终深入了解一下 Ruby 中的情况:

```
class Elevator  
    attr_reader :name
    attr_accessor :is_functioning

    def initialize(name)
        @name = name
        @is_functioning = true
    end
end

class Inspector
    attr_reader :name

    def initialize(name, philosophy)
        @name = name
        @philosophy = philosophy
    end
end

class ElevatorInspection
    @@all = []

    def self.all
        @@all
    end

    def initialize(elevator, inspector)
        @elevator = elevator
        @inspector = inspector
        self.class.all << self
    end
end

elevator_9 = Elevator.new("Elevator No. 9")
=> #<Elevator:0x00007fab56b99c00 @is_functioning=true, @name="Elevator No. 9">

lila_mae = Inspector.new("Lila Mae Watson", "Intuitionism")
=> #<Inspector:0x00007fab55c6c070 @name="Lila Mae Watson", @philosophy="Intuitionism">

 ElevatorInspection.new(elevator_9, lila_mae)
=> #<ElevatorInspection:0x00007fab56087a10
 @elevator=#<Elevator:0x00007fab56b99c00 @is_functioning=true, @name="Elevator No. 9">,
 @inspector=#<Inspector:0x00007fab55c6c070 @name="Lila Mae Watson", @philosophy="Intuitionism">> 
```

因为 ElevatorInspection 类的每个实例都“属于”一个电梯和一个检查员，所以我可以通过 ElevatorInspection 类找到我需要的关于这两个类之间关系的所有信息——您会注意到我启动的 ElevatorInspection 实例具有关于相关电梯和检查员实例的所有信息。关键是识别代表和维持关系的对象。

## 延伸阅读&来源

[红宝石，把一切都看成一个物体](https://www.ruby-lang.org/en/about/)

[布鲁诺·拉图尔，*重组社交——演员网络理论简介*](http://a.co/28xHnHd)

Darryl Cressman -行动者网络理论概述:准时化，异质工程&翻译

[演员网络理论通俗易懂](https://www.youtube.com/watch?v=X2YYxS6D-mI)

[科学&技术研究维基百科页面](https://en.wikipedia.org/wiki/Science_and_technology_studies)