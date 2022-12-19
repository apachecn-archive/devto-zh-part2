# 告诉我一个我可以编码的故事

> 原文：<https://dev.to/mnivoliez/tell-me-a-story-i-could-code-1ho7>

*最初发表在我的[博客](https://www.mathieu-nivoliez.com/posts/2018-05-06-tell-me-a-story-i-could-code)上的帖子*

大家好！今天，我的一个同事兼朋友，Gaetan，想向你提出一个简单的(或不简单的)概念！

这是一个你是故事讲述者的代码！

## “Whaaaaat？”

[![whaaaaat](img/b984a79c281f0308709722350b6cbf17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wqsZDlDC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i0.kym-cdn.com/photimg/newsfeed/000/768/057/7e2.gif)

让我解释一下！我们建议你一句一句地讲一个故事(一个小故事)。每次选择一个句子，我们都会试着用代码来表达这个故事。

一个例子抵得上 1000 次演讲，所以这是我们故事的开始:

### “从前，一个名叫鲍勃的年轻骑士捡到了一便士！”

```
#[derive(Debug)]
struct Story {
    pub time: &'static str,
    pub main_character: Character,
}

#[derive(Debug)]
struct Character {
    pub name: &'static str,
    pub money: i32,
}

fn found_a_strange_penny(character: &mut Character) {
    character.money += 1;
    println!("Oh a penny! \" said {}.\"", character.name);
}

fn main() {
    let mut story = Story {
        time: "Once upon a time",
        main_character: Character {
            name: "Bob",
            money: 0,
        },
    };

    found_a_strange_penny(&mut story.main_character);
} 
```

Enter fullscreen mode Exit fullscreen mode

所有的代码都将存在于这个[库](https://gitlab.deep-nope.me/mnivoliez/tell-me-story-i-could-code)中。

所以，就看你了，读者，继续这个故事。用一两句话发表评论，我们会尽量编码。
我们将选择最吸引我们的评论！

开始:p

——
盖坦和马蒂厄