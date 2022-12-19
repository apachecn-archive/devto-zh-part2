# 修复随机、间歇和不稳定的 RSpec 测试失败

> 原文：<https://dev.to/talum/fixing-random-intermittent-and-flaky-rspec-test-failures-56c1>

在我看来，几乎没有什么比随机失败、断断续续、反复无常的测试失败更令人恼火的了。本地运行测试套件，一切都通过了。在 CI 服务上运行它，您的构建会失败！单红！🔴重新建造它，不知何故，神奇地，它变绿了。

一个古怪或闪烁的测试会为大量的噪音和不确定性创造空间。漏报可能会延迟部署和发布功能。🚢预期的红色失败可能会鼓励开发人员忽略真正的负面因素，并合并到主控和部署中。

这就是我的团队最近的普遍氛围。不确定的规范每隔几个构建就会出现，最终在主分支上我们的构建中有 50%会出现。在一个不断壮大的团队中，随着发布日期的临近，没有人觉得有足够的权力来解决这个古怪的测试问题。毕竟，追踪一个不确定的 bug 可能需要几天、几周甚至更长时间。

我们一直说我们会尽快解决这个问题，但是随着我们的运营团队致力于一个新的依赖于通过构建的部署策略，我们最终优先考虑了这个问题。我是被选中追踪它的幸运儿。

这是我如何解决这个特殊的测试失败的故事。

## 意外失败😕

在过去的几个月里，我一直在对我团队的代码库进行大量后端、系统和广泛的更改，以支持向 GitHub Enterprise 的迁移。我指的是整个代码库，从我们的主要 Rails monolith，到我们的开源 gems，到我们的 IDE，到我们的聊天服务。这项工作需要有自己的帖子，所以现在，只要说我们的平台与 GitHub 深度集成就足够了，从头像和用户名到托管我们的课程内容，一切都依赖于它。

无论如何，为了将我们的平台从 GitHub 中分离出来，我和我的团队开始引入与`GithubAccount`分开的`LearnAccount`的概念，它分别跟踪特定于 Learn 的身份和特定于 GitHub 的身份。

作为负责任的团队成员，我们一丝不苟地替换了每个方法调用
,并用规范支持我们的更改。对于新的`LearnAccount`模型，也就是`belongs_to`的`User`模型，我们使用[思维机器人的工厂机器人](https://github.com/thoughtbot/factory_bot_rails)创建了一个工厂。我们使用 [Faker](https://github.com/stympy/faker) gem 来创建合理的真实和随机的测试数据。

```
FactoryBot.define do
  factory :learn_account, class: Identities::LearnAccount do
    username    { Faker::Lorem.word }
    user_id     1
  end
end 
```

`GithubAccount`的新工厂基本上是一样的。

我们还为我们的`Users`工厂添加了一个特征，这样我们就可以有选择地为我们在测试中创建的`user`创建`learn_account`。我们绝对不希望保存不必要的记录，这会降低我们测试套件的速度。

```
FactoryBot.define do
  factory :user do
    email { Faker::Internet.email }
  end

  trait :with_learn_account do
    after(:create) do |user|
      user.create_learn_account
    end
  end
end 
```

这一切看起来都很好。我们推出了新功能，去掉了旧代码，然后测试套件开始偶尔失败。最常见的测试失败应该是这样的:

```
1) Api::V1::UsersController Switch active batch or track POST #switch_active_track given track within students current active batch updates the current track
Failure/Error: learn_account.username
     NoMethodError:
       undefined method `username' for nil:NilClass
     # ./app/models/user.rb:185:in `learn_username' 
```

```
 1) Api::V1::StudyGroupsController PUT update rsvp_update rsvped returns a status 200
     Failure/Error: learn_account.username
     NoMethodError:
       undefined method `username' for nil:NilClass
     # ./app/models/user.rb:185:in `learn_username' 
```

```
 1) CoursesController GET courses/:course_slug/sign-up user has email in session user's email already exists in the db user has login credentials redirects user to sign in and stores slug in session
     Failure/Error: expect(session[:course_slug]).to eq(course_slug)
       expected: "batch-123"
            got: nil 
```

在我们的代码库中，经常会有来自不同领域的不同测试，但是错误看起来总是类似于上面的一个测试失败。

## 再现性🤷🏻‍

我遇到的第一个问题是试图重现失败的规格。我不断地在本地重新运行这些规范，它们根本不会失败。我一遍又一遍地这样做，并开始担心我的理智。然而，在 CircleCI，建筑不断失败。我也会重新运行它们，有时会失败，有时会通过。我查看了一下各种圆形配置，想知道是否是一些错误的配置导致了测试不能自动清理。为了以防万一，我还搜索了我们的本地`rspec`配置，但是一无所获。

然后，我搜索了其他互联网文献，寻找其他策略来解决古怪的测试。其中最有帮助的帖子是关于如何使用 RSpec 的`bisect`标志的指导性帖子。根据 [RSpec 文档](https://relishapp.com/rspec/rspec-core/docs/command-line/bisect)，当与 RSpec 的`--seed`标志一起使用时，`bisect`选项将“运行你的套件的子集，以隔离重现相同故障的最小示例集”。这允许您识别顺序相关的测试失败。与此同时，`seed`选项允许你随机选择规格以相同的顺序运行。你需要使用两个标志来完成这项工作:

```
rspec --seed 1234 --bisect 
```

然而，这并不是最终的解决方案。我在单个测试文件上运行的头几次，失败的测试不会让他们自己知道。我心里明白，如果我能让其中一个持续失败，我就能解决其余的问题。但是最后，我能够用上面的命令重现失败的规范。通过这个最小的重现命令，我能够进行确定性的测试，并进一步隔离问题。

## 亚修🛠️

有了再现性，我可以更仔细地查看代码并识别问题。因为不是同一个测试总是失败，我意识到问题不在于测试本身，*而在于数据设置*。因为我们的`LearnAccount`和`GithubAccount`的`Usernames`必须是唯一的，但是我们的工厂并不总是生成唯一的值，这导致与`User`相关联的`LearnAccount`和`GithubAccount`由于无效而无法在数据库中持久化。这解释了为什么`learn_account`和`github_account`有时是`nil`。

事实上，我们使用`Faker` gem 来生成接近真实值的值，特别是`Faker::Lorem::Word`。在那个单词集合中只有 249 个值，所以存在不小的冲突机会。(是的，有一个补充的值集合，但是更多的单词仍然不能保证唯一性。)

因此，修复方法是确保在测试中生成的用户名总是唯一的，我通过使用 [Factory Bot 的序列](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md)将一个唯一的整数附加到生成值的末尾来做到这一点。

我们的工厂现在看起来是这样的:

```
FactoryBot.define do
  factory :learn_account, class: Identities::LearnAccount do
    sequence :username do |n|
      "#{Faker::Lorem.word}-#{n}"
    end
    user_id     1
  end
end 
```

我在本地和 Circle 上又运行了几十次测试套件，都是通过的构建，合并了这个变化，并通过阻止所有未来没有通过测试套件的 master 合并来庆祝。开个玩笑，我还通过 Slack 提醒了我们的团队，并在各处散布了一堆手指交叉的表情符号。🎉🤞🏼到目前为止，一切顺利。全绿色✅.

## 🗝️的主要经验教训

1.  在引入随机测试失败的那一刻，解决它们是非常重要的。
2.  RSpec `--seed`和`--bisect`可以帮助您隔离问题。
3.  有时不是测试，而是支持测试的数据。即不依赖`Faker`生成唯一值。

(额外的教训)我太喜欢表情符号了。🚀

## 资源

*   [消除片状红宝石测试](https://engineering.gusto.com/eliminating-flaky-ruby-tests/)
*   一个更好的方法来驯服你随机失败的规格
*   [RSpec 平分](https://relishapp.com/rspec/rspec-core/docs/command-line/bisect)
*   【RSpec 如何帮助我解决随机规格故障