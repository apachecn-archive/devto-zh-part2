# 公开暴露的私处

> 原文：<https://dev.to/belinde/private-parts-exposed-in-public-fl2>

据说一个好的对象保持私有或者保护他的属性，所以没有人能够破坏商业逻辑。这确实是一个明智的建议，但有时我们希望打破东西:也许我们需要获得由外部库管理的私有连接，也许我们只是想看到世界燃烧。第一种情况的一个例子发生在很多年前的我身上，当时我正在进行一个奇怪的 Wordpress 集成:我需要底层的`mysqli`连接，但是它存储在类`\wpdb`的`$wpdb` singleton 的`dbh`属性中。我不能编辑这个类来公开`dbh`属性，但是我需要这个连接。怎么办？我们都知道私有或受保护的属性不能通过`->`操作符从实例外部访问！A `$object->privateProp`会不会*总是*失败！

那不完全正确。相当，但不完全。有一个我认为不太为人所知的小细节:对方法和属性的访问控制是通过检查我们工作的上下文来完成的。因此，如果我们在该类的方法中，我们可以访问该类的任何实例的私有或受保护的属性。

说得够多了，让我们做些代码吧。想象一下，我们有一个第三方库，我们不能修改它，它做这种事情:

```
// begin read only code

class DeepThought {
    protected $theAnswer = 42;
    private $theQuestion = 'How many roads...';
}

$singleton = new DeepThought();

// end read only code 
```

Enter fullscreen mode Exit fullscreen mode

我们非常想知道:我们知道那是受保护的，所以我们可以继续工作。我们知道一个受保护的属性可以从扩展类中访问，所以我们编写了这段代码:

```
class Towel extends DeepThought {
    public static function getTheAnswer(DeepThought $instance): int
    {
        return $instance->theAnswer;
    }
}

echo Towel::getTheAnswer($singleton); 
```

Enter fullscreen mode Exit fullscreen mode

你看到魔法了吗？`Towel`扩展了`DeepThought`，所以可以合法地访问他的受保护属性。当我们调用`Towel::getTheAnswer()`时，我们在`Towel`上下文中，所以`$instance->theAnswer`也是合法的。

但是`theAnswer`仅仅是被保护，我们也想知道`theQuestion`，那是隐私。扩展类我们仍然不能访问私有属性...我们需要一条更大的毛巾！:-)

反思来拯救:

```
$refl = new ReflectionProperty(DeepThought::class, 'theQuestion');
$refl->setAccessible(true);

echo $refl->getValue($singleton); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，反射也适用于受保护的属性，但是为了便于叙述，我将它放在最后。我们只是为`theQuestion`实例化了一个 ReflectionProperty:这就像一个可以检查或编辑一些属性而无需更改代码的工具。之后，我们使用反射使属性可访问，最后我们将编辑过的属性描述应用于原始对象。就像 JavaScript 里的`.call()`。

仅此而已！有了这两种技术，你可以拆开任何一个类来提取里面的宝石。但是保持明智！具有私有属性的对象通常有很好的理由这样设计。没有一个开发者关心私有属性的向后兼容性:就像在现实生活中，如果你未经同意就玩别人的私有部分，你会有麻烦的！