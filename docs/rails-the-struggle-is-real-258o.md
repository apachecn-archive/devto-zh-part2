# Rails:斗争是真实的

> 原文：<https://dev.to/swlkr/rails-the-struggle-is-real-258o>

[![](img/5a484293b924bd713660a2f7afe53c2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a9BHJ9X6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0rrawr2rjzh2j16018hi.jpeg)

我不知道我花了多少时间去寻找最简单的事情，但是我最终挠头了，因为我被迫用一种疯狂复杂的方式去做，因为一些丹麦人认为这是一个好主意。不要曲解，rails 有一些积极的东西，但对我来说有意义不是其中之一。让我给你一些我反复处理的 WTF 时刻的具体例子。

## 自定义表单生成器助手

这是当你想把一些元素包装成漂亮整洁的东西的时候，比如

```
<%= f.my_tag :user_id, @user.id %> 
```

制作这种东西的代码对我来说是一个非常糟糕的时刻，我不得不从我的高价 macbook pro 上后退一步，深呼吸 3 次，然后才能集中精神，投入到 content_tag 地狱中。

### ActiveRecord 语法

我曾经是 ActiveRecord 的粉丝，我一直认为它有一种特殊的东西，使与令人讨厌的 mysql、postgres 或 sqlite 的交互变得可口而理智。直到我意识到它对我的伤害大于它对我的帮助。老实说，我甚至不知道从哪里开始。等等…我想起来了，好了，我们开始吧，让我们从一些简单的事情开始，从相关对象中获取集合:

```
current_user.todos.includes(:tags).where(completed: true).order(created_at: :desc).limit(30) 
```

我的意思是，这似乎是我想要的，我想要一些待办事项和基于当前登录用户的相关标签，我期望这样:

```
[
  {
    todo: {
      name: "todo #1",
      tags: [{name: "tag"}]
    }
  }
] 
```

但是这不会发生，所有的东西都被包装在一个对象中，为了得到简单的数据，你必须向自己介绍一些奇怪的词，比如序列化，有时甚至是整理。不过说实话，作为一名数据管理员，听起来好像我要在镇中心进行一场狂野的西部枪战:只有我、我的键盘和四人组。

### ERB

这甚至与定制表单生成器助手无关，这完全是胡说八道。如果我因为编辑故障关闭一个 ERB 标签就能得到五分钱，我肯定不用再工作了，因为我会成为一个有钱人。在 ruby 和 rails 惯例的基础上学习另一种语法的想法？我只是想在这里做一个 fricken 网站，而不是把一个人送上月球，我需要在这里学习多少不同的东西？！

### AR 回调地狱

如何在给定事件上实现函数重用？比如当一个对象被创建时？你输入 after_create 然后调用一个方法。完成了。当然，没人告诉我的是，我首先需要这些钩子的原因是因为我们不只是调用常规函数，我们调用了如此复杂的方法链，这使得马里亚纳海沟看起来像一个儿童游泳池。你会问，我们如何在每次创建新的数据库记录时调用一个函数？它被称为一个函数，你可以在任何你想保存它的地方调用这个函数，你不需要回调，你不需要学习另一件事，也不会迷失在关于它是 after_commit on: :create 还是 after_create 的争论中。你可以直接调用一个函数。

### 批评是制定新框架的最真诚的形式

这就是我所做的，我做了另一个全栈框架，除了这个不是用 ruby，而是用 clojure。它也没有很多东西，没有独立的模板语言，没有 ORM，没有 AR 回调或特殊的`content_tag`方法，它使用函数，仅此而已。如果你已经做到这一步，你一定会感兴趣，请查看[快速入门](https://github.com/coast-framework/coast/blob/master/docs/Quickstart.md)，并让我知道你的想法！

—

最初发布于[媒体](https://medium.com/@swlkr/rails-the-struggle-is-real-e0bf8052e58b)