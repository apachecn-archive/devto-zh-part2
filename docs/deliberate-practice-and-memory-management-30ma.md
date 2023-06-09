# 刻意练习和记忆管理

> 原文：<https://dev.to/sandordargo/deliberate-practice-and-memory-management-30ma>

我最近读了本令人大开眼界的书 [Cal Newport，好到他们无法忽视你](http://sandordargo.com/blog/2018/08/22/so-good-they-cant-ignore-you)。他非常强调有意练习的重要性。我还决定更加认真地对待我的练习课，我重新安排了[我如何在早上和午餐时间使用我的番茄工作证](http://sandordargo.com/blog/2018/02/28/setting-yourself-up-to-succeed)，以便进行更有意识的练习。我想拓展我的极限。在 C++中，并没有那么难。

在我的一篇文章中，我已经写了我在实现[镀金玫瑰形](https://github.com/emilybache/GildedRose-Refactoring-Kata)时使用的一种新方法。

现在，我想详细介绍一下重构的一个部分，也是我最纠结的部分。

此时，我已经创建并实现了一个`Updater`接口，来管理一个`Item`的`sellIn`和`quality`属性。但是我不喜欢这个解决方案，因为它没有直接更新`Item`的相应属性，而是只更新了`Updater`的属性。紧接着，它将`Updater`的值复制回`Item`类。

```
class Updater {
 public:
  Updater(int sellIn, int quality) : _quality(quality), _sellIn(sellIn) {}
  virtual ~Updater() {};

  virtual void updateQuality() = 0;
  virtual void updateSellIn() = 0;

// later these became protected
  int _quality;
  int _sellIn;
 };

// There were several updaters implementing this abstract class
// ...

};

class Item {     
public:
    string name;
    int sellIn;
    int quality;
    Updater* updater;

    Item(string name, int sellIn, int quality) : name(name), sellIn(sellIn), quality(quality)//, updater()
    {
      if (name == "Sulfuras, Hand of Ragnaros") {
        updater = new SulfurasUpdater(this->sellIn, this->quality);
      } 
      // else if ...

    }

    void updateSellIn() {
      updater->updateSellIn();
      this->sellIn = updater->sellIn; // This is so ugly!
    }

    void updateQuality() {
      updater->updateQuality();
      this->quality = updater->quality;
    }
}; 
```

相反，我想要实现什么，我的限制是什么？

我想从`Updater`中更新`Item`类的属性。我自我施加的约束是，我不想改变我们在测试中与项目交互的哪怕是最微小的方式。不是因为我懒，而是我们在测试中与对象交互的方式与用户与对象交互的方式是一样的。如果它在测试中为我而改变，显然它也会为我们的用户而改变。因为这样的改变对我们想象的客户来说代价很高，所以当我们引入一些 API 改变时，我们可能会失去它们。这种变化不受欢迎。

我的想法是，在`Item`的构造函数中，我将把`sellIn`和`quality`变量的地址而不是它们的值传递给`Updater`。然后在`Updater`中，我将存储引用，即非空指针，而不是值。

听起来不错？

在我实施之前，它听起来确实比现有的解决方案要好。

```
class Updater {
 public:
  Updater(int& sellIn, int& quality) : _quality(quality), _sellIn(sellIn) {}
  virtual ~Updater() {};

  virtual void updateQuality() = 0;
  virtual void updateSellIn() = 0;

// later these became protected
  int& _quality;
  int& _sellIn;
 };

//...

class Item {
  //...

  void updateSellIn() {
    updater->updateSellIn();
    // this->sellIn = updater->sellIn; // This line is removed now!
  }

  void updateQuality() {
    updater->updateQuality();
    // this->quality = updater->quality; // Just like this! Yay!
  }
}; 
```

没用。`Item`类的`quality`和`sellIn`属性没有更新。好吧...嗯，不好，一点也不好！我想，我一定错过了什么。我读了代码。看起来很好。我又看了一遍。再一次。再一次。寻找丢失的&符号或类似的琐事。我找不到它。

晚上已经很晚了。我说今晚就这样吧，以后我会检查一下。然后我去了洗手间，但我还是把笔记本电脑开着。以防解决方案会击中我的脑袋。你猜怎么着，当我站在那里的时候，我意识到问题不在于`Item.quality`和`Item.sellIn`被复制，而很可能是整个`Item`类在某个地方被复制，在测试中，我试图断言原始实例的属性，同时更新其他东西。我当时就想跑回去，但我不得不等一会儿。

当我看着我的试卷，我知道我得到了它。

```
//GildedRoseTextTests.cc
int main()
{
  vector<Item> items;
  items.push_back(Item("+5 Dexterity Vest", 10, 20));
  items.push_back(Item("Aged Brie", 2, 0));
  // ...
  GildedRose app(items);
  // ...
  app.processItems();
} 
```

我添加了一些日志，以确保它是肯定的。

当调用`updateQuality`或`updateSellIn`时，`Item`的地址在构造函数中是不同的。我创建了一个条目，当它被推回到条目向量时，它被复制了。那很好。但是它被错误地复制了，包括成员引用。

如果没有实现(或者从 C++ 11 开始没有显式删除)，C++会自动为你实现复制构造函数和赋值操作符。这是好事吗？没关系。重要的是它会发生，有时实现不会像你期望的那样。就像这个案子一样。

事实上，所发生的是创建了 Item 的新副本，创建了`sellIn`和`updater`的副本(在新地址)，但是在`updater`中对`sellIn`的引用仍然指向被复制对象的“旧”塞林。所以事实上`sellIn`被更新了，但不是我们想要的那个。

修复很简单，我只需要实现复制构造函数和赋值操作符:

```
Item& Item::operator=(const Item& i){
  this->name = i.name;
  this->quality = i.quality;
  this->sellIn = i.sellIn;
  this->updater = i.updater;
  return *this;
}

Item::Item(string name, int sellIn, int quality) : name(name), sellIn(sellIn), quality(quality)//, updater()
{
  updater = Updater::CreateUpdater(name, this->sellIn, this->quality);
} 
```

我非常高兴看到我想要实现的实现最终能够工作。复制后，`updater`的引用也指向了新的`sellIn`。

我还发现了两个重要的要点:

1.  永远不要忘记复制构造函数和赋值操作符。
2.  C++是一种语言，它给你很大的权力去控制事情应该如何发生。正如你可能知道的那样，拥有强大的力量，也意味着巨大的责任。也不要忘记这一点。

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/08/29/deliberate-practice-and-memory-management)上。*