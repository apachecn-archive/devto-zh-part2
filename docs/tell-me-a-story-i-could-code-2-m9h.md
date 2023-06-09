# 告诉我一个我能编码的故事#2

> 原文：<https://dev.to/mnivoliez/tell-me-a-story-i-could-code-2-m9h>

*最初发表在我的[博客](https://www.mathieu-nivoliez.com/posts/2018-05-11-tell-me-a-story-i-could-code-2/)上的帖子*

大家好！又是我们，盖坦和马修！
这是*的第二集，告诉我一个我可以编码的故事*。
令我们惊讶的是，这个概念比我们希望的更有效，而你，是的，你，非常多产！

## “提出了什么？”

简而言之，一个动作，一个对话和一条龙的出现被提出来了。以下是我们的进展情况。

首先，我们现在有一条龙，它仍然是一个角色，但与人类不同。
第二，人物可以互相说话。
第三，人物可以去或来自外景。

所以，让我们把我们的`Character`结构重命名为`Human` :

```
struct Human {
  name: String,
  money: i32,
} 
```

Enter fullscreen mode Exit fullscreen mode

然后创建`Dragon`结构:

```
struct Dragon {
  name: String,
} 
```

Enter fullscreen mode Exit fullscreen mode

下一步提取角色行为:

```
pub trait Character {
    fn new(name: &str) -> Self;

    fn says_to(&self, other: &Self, msg: &str);

    fn move_to(&self, location: &Self);

    fn get_name(&self) -> &str;

    fn run_away_from(&self, location: &Self);
} 
```

Enter fullscreen mode Exit fullscreen mode

但事实上，一个`Charater`唯一能去或逃离的地方是另一个`Charater`。所以，假设我们希望`Character`能够用`Localisable`结构做到这一点。

```
pub trait Localizable {
    fn get_location(&self) -> String;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以像这样修改`Character`结构，告诉它一些方法将使用类型`T`来表示`impl`元素`Localizable` :

```
pub trait Character {
    fn new(name: &str) -> Self;

    fn says_to<T: Character>(&self, other: &T, msg: &str);

    fn move_to<T: Localizable>(&self, location: &T);

    fn get_name(&self) -> &str;

    fn run_away_from<T: Localizable>(&self, location: &T);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以实现那些`trait`到`Character`

```
#[derive(Debug, Clone)]
pub struct Human {
    pub name: String,
    pub money: i32,
}

impl Human {
    pub fn find(&mut self, item: Item) {
        match item {
            Item::Money(amount) => {
                self.money += amount;
                println!("Oh {} penny! \" said {}.\"", amount, self.name);
            }
        }
    }
}

impl Character for Human {
    fn new(name: &str) -> Self {
        println!("There was a man named {}.", name);
        Human {
            name: name.to_owned(),
            money: 0,
        }
    }
    // Character needs to get back to another
    fn move_to<T: Localizable>(&self, location: &T) {
        println!("{} move to {}", self.name, location.get_location());
    }

    fn says_to<T: Character>(&self, other: &T, msg: &str) {
        println!("\"{}\" said {} to {}", msg, self.name, other.get_name());
    }

    fn get_name(&self) -> &str {
        self.name.as_str()
    }

    fn run_away_from<T: Localizable>(&self, location: &T) {
        println!("{} run away from {}", self.name, location.get_location());
    }
}

impl Localizable for Human {
    fn get_location(&self) -> String {
        self.name.clone()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

而对于`Dragon`和

```
#[derive(Debug, Clone)]
pub struct Dragon {
    pub name: String,
}

impl Dragon {
    pub fn fly_toward<T: Localizable>(&self, location: &T) {
        println!(
            "{} the dragon fly toward {}",
            self.name,
            location.get_location()
        );
    }
}

impl Localizable for Dragon {
    fn get_location(&self) -> String {
        format!("{} the dragon", self.name)
    }
}

impl Character for Dragon {
    fn new(name: &str) -> Self {
        println!("There was a dragon named {}.", name);
        Dragon {
            name: name.to_owned(),
        }
    }
    fn move_to<T: Localizable>(&self, location: &T) {
        println!("{} move to {}", self.name, location.get_location());
    }

    fn says_to<T: Character>(&self, other: &T, msg: &str) {
        println!("\"{}\" said {} to {}", msg, self.name, other.get_name());
    }

    fn get_name(&self) -> &str {
        &self.name
    }

    fn run_away_from<T: Localizable>(&self, location: &T) {
        println!(
            "How that, {} the dragon is running away from {}",
            self.name,
            location.get_location()
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，到目前为止，我们指定只有`Human`可以找到`Item`并且只有`Dragon`可以飞行。

还要注意的是，`Character`里面的名字在`&'static str`(意为具有`static`生命周期的 [`String`片](https://doc.rust-lang.org/std/primitive.str.html))之前，已经被更改为`String`。原因是使用`String`更容易，并且不需要关心生存期(变量存在的“范围”),以防我们必须以一种时尚的方式返回它。此外，请注意，使用`static`意味着基准电压源的寿命与应用寿命相同。

现在输出看起来像:

```
Once upon a time...
There was a man named Bob.
Oh 1 penny! " said Bob."
There was a man named Joe.
Bob move to Joe
"Dude! Look what I found!" said Bob to Joe
"You should invest the penny in the Sheriff of Nottingham's Bank" said Joe to Bob
"But I already have invested money in the bank, I want to buy a horse!" said Bob to Joe
There was a dragon named Igorrr.
Igorrr the dragon fly toward Bob
Bob run away from Igorrr the dragon
"The shiny thing belongs to me" said Igorrr to Bob
"Never! Founders keepers, you Reptilebug!" said Bob to Igorrr 
```

Enter fullscreen mode Exit fullscreen mode

完整代码可在[这里](https://gitlab.deep-nope.me/mnivoliez/tell-me-story-i-could-code)获得。

更多信息:

*   [切片](https://doc.rust-lang.org/book/second-edition/ch04-03-slices.html)
*   [特质](https://doc.rust-lang.org/book/second-edition/ch10-02-traits.html)
*   [寿命](https://doc.rust-lang.org/book/second-edition/ch10-03-lifetime-syntax.html)

特别感谢:

*   朱利安·陈
*   杰森·麦克唐纳
*   法伊赞·拉巴尼
*   瓦伦丁·西尔维斯特
*   索班
*   萨利姆·马赫布比

从[开发者到帖子讨论](https://dev.to/mnivoliez/tell-me-a-story-i-could-code-1ho7/comments)为故事添加元素。

和上次一样，下一集我们就指望你了

——
盖坦和马蒂厄