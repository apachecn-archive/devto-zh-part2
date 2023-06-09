# 理解蒙蒂·霍尔问题

> 原文：<https://dev.to/micahshute/understanding-the-monty-hall-problem-3mgf>

这不完全是关于开发或编码，但这是一个解决问题、逻辑和概率的练习，所有这些都是我们交易的基础。作为工程师和程序员，逻辑和数学，尤其是概率和归纳，是我们应该追求精通的领域。

我最近在听一个博客推荐的播客，在其中一集讨论 big-O 理论时，提到了 Monty Hall 问题。评论员对此谈了一点，在这一点上，他们承认没有'得到它'，并继续前进。这促使我分享我对这个问题的理解，因为我认为作为工程师，我们应该是能够理解这个概念和类似问题的人。

所以这里是我如何得到它的。这需要一些思考，但最终，它实际上并不复杂。

如果你不熟悉这个问题，蒙蒂霍尔问题如下:

*   你会看到三扇门。只有一个后面是奖品。

[![3 doors](img/aa5cf3f0697277430f3ee0c7853f5531.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ss5ZHH5E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/txe872kzoah3ynxycj3k.png)

*   你选一个你认为奖品可能在后面的门。

[![initial choice](img/5d7404c9555936979349fc7e36f93ed8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kD6Ixjk3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z84dm0puooe6pvyv9oyr.png)

*   当你打开门后，第三方(即游戏主持人)打开了一扇没有奖品的门。
*   然后，您可以选择继续使用您最初的猜测，或者将您的猜测切换到游戏节目主持人没有打开的那扇门。

[![final choice](img/a374601706ed8caaaa647bb4f5b9b17f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--obUJnZBu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ruv6l2o6tzz9e5hrijta.png)

*   你应该做哪一个？换还是留？有关系吗？

大多数人最初的直觉是，一旦你被展示了没有奖品的门，现在剩下的两扇门之间的可能性是 50-50，所以你切换与否并不重要。但事实并非如此。实际上，切换到另一扇门可能对你最有利。但是，让我们成为优秀的工程师，遵循科学的过程，首先假设你有 50%的机会赢。

我们有我们的假设，所以让我们实验。

代码:

```
rand_gen = Random.new
prize_locations = []
switch_wins = 0
stay_wins = 0

#generate winning spots
10000.times do
    # rand_gen.rand(3) returns  0, 1, or 2
    prize_locations << (rand_gen.rand(3) + 1)
end

prize_locations.each do |prize_loc|
    # contestant chooses a random location
    initial_choice = rand_gen.rand(3) + 1
    # monty opens a door without a prize -> init. choice is prize ? (random opening of one of the remaining 2 doors) : (since 1, 2, and 3 must all be an option, 6 minus the other two will always give us the leftover one)
    opened_door = initial_choice ==  prize_loc ? (prize_loc + rand_gen.rand(2)) % 3 + 1 : (6 - initial_choice - prize_loc)
    # Universe split 1: I decide to stay with my choice.
    stay_wins += 1 if prize_loc == initial_choice
    # Universe split 2: I switch 
    switch_wins += 1 if prize_loc == (6 - initial_choice - opened_door)
end

puts "Total wins if you stay: #{stay_wins}, which is #{stay_wins / 10000.0}%"
puts "Total wins if you switch: #{switch_wins}, which is #{switch_wins / 10000.0}%" 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
[07:28:18] monty_hall  
//⚓   [⚛ micah] ruby monty_hall.rb  
Total wins if you stay: 3344, which is 0.3344%  
Total wins if you switch: 6656, which is 0.6656%  

[07:28:20] monty_hall  
//⚓   [⚛ micah] ruby monty_hall.rb  
Total wins if you stay: 3316, which is 0.3316%  
Total wins if you switch: 6684, which is 0.6684%  

[07:28:21] monty_hall  
//⚓   [⚛ micah] ruby monty_hall.rb  
Total wins if you stay: 3348, which is 0.3348%  
Total wins if you switch: 6652, which is 0.6652% 
```

Enter fullscreen mode Exit fullscreen mode

另一种可以给你更多信息的方法是在停留和换门之间随机切换:

代码:

```
rand_gen = Random.new
prize_locations = []
switch_wins = 0
stay_wins = 0

#generate winning spots
10000.times do
    #rand_gen.rand(3) returns  0, 1, or 2
    prize_locations << (rand_gen.rand(3) + 1)
end

prize_locations.each do |prize_loc|
    #contestant chooses a random location
    initial_choice = rand_gen.rand(3) + 1
    #monty opens a door without a prize -> init. choice is prize ? (random opening of one of the remaining 2 doors) : (since 1,2, and 3 must all be an option, 6 minus the other two will always give us the leftover one)
    opened_door = initial_choice ==  prize_loc ? (prize_loc + rand_gen.rand(2)) % 3 + 1 : (6 - initial_choice - prize_loc)
    #This time, let's just keep one universe and randomly choose
    switch_or_stay = rand_gen.rand(2) # 0 means stay, 1 means switch
    stay_wins += 1 if prize_loc == initial_choice && switch_or_stay == 0
    switch_wins += 1 if prize_loc == (6 - initial_choice - opened_door) && switch_or_stay == 1
end

puts "Total wins from when you stay: #{stay_wins}, which is #{stay_wins / 10000.0}%"
puts "Total wins from when you switch: #{switch_wins}, which is #{switch_wins / 10000.0}%"
puts "Total wins: #{wins = switch_wins + stay_wins}, which is #{wins / 10000.0}%" 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
[07:43:07] monty_hall
//⚓   [⚛ micah] ruby monty_hall.rb
Total wins from when you stay: 1685, which is 0.1685%
Total wins from when you switch: 3303, which is 0.3303%
Total wins: 4988, which is 0.4988%

[07:43:08] monty_hall
//⚓   [⚛ micah] ruby monty_hall.rb
Total wins from when you stay: 1689, which is 0.1689%
Total wins from when you switch: 3307, which is 0.3307%
Total wins: 4996, which is 0.4996%

[07:43:09] monty_hall
//⚓   [⚛ micah] ruby monty_hall.rb
Total wins from when you stay: 1662, which is 0.1662%
Total wins from when you switch: 3398, which is 0.3398%
Total wins: 5060, which is 0.506% 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我们的假设是错的。看来我们应该换个门。时间来完善我的假设，这一次不仅仅是依靠天真的直觉。(也许你在上面看到了一些新的直觉:你获得持久胜利的唯一方法是你最初猜对了)

看问题有几种方法。首先，让我们来看看分裂宇宙的时间线:

*   你有三扇门，其中一扇门后有奖品。那么，对于你最初的选择，你正确选择奖品的概率是多少？
    *   没错——1/3
*   让我们把时间定格在这里，再做一点分析。在你的选择中有两种可能性。所以让我们分裂我们的宇宙。
    *   **宇宙 1** :你落在 33.3%几率内，选择正确。现在，游戏节目主持人可以打开剩下的任何一扇门，因为他们都没有奖品。现在你又多了一个选择(又一个分裂！)
        *   你坚持你最初的选择。(人为因素，假设 50%)
            *   你赢了。(100%)
        *   **宇宙 1.2** 你切换。(50%)
            *   你输了。(100%)
    *   **宇宙 2** 你最初选错了，这种情况发生的几率是 66.7%。现在游戏节目主持人只能打开后面没有奖品的门(留下另一扇门有奖品)**分裂宇宙 2**
        *   你坚持你最初的选择。(50%)
            *   你输了(100%)
        *   **宇宙 2.2** 你切换。(50%)
            *   你赢了。(100%)

**数学分析**:那么我们的问题陈述是什么？我们想知道我们是换还是留会赢更多，对吗？所以让我们计算一下你留在你最初的门的情况下获胜的概率，然后计算一下你换门的情况下获胜的概率。请注意，我们给这个人一个随机的 50%的机会，让他选择换门或留在原来的门上。

```
P(win | stay) = [P(win) * P(stay | win)] / P(stay)  

P(win) = (Universe 1 && Universe 1.1) || (Universe 2 && Universe 2.2) =  (1/3) * (1/2) + (2/3) * (1/2) = 1/2    

P(stay | win) = Out of wins possibilities only, what percentage of those did you stay for?  
=  (Universe 1 && Universe 1.1)  / [(Universe 1 && Universre 1.1) || (Universe 2 && Universe 2.2)] = (1/3) * (1/2) / (1/2) = 1/3

P(stay) = (Universe 1 && Universe 1.1) || (Universe 2 && Universe 2.1) = (1/3) * (1/2) + (2/3) * (1/2) = 1/2

P(win | stay) = [1/2 * 1/3] / 1/2 = 1/3 
```

Enter fullscreen mode Exit fullscreen mode

你也可以说“在我停留的时间里，我赢了多少次？”那会给你同样的答案:

```
P(win | stay): all wins which lie within stay universes
P(win | stay) = (Universe 1 && Universe 1.1) / [(Universe 1 && Universe 1.1) || (Universe 2 && Universe 2.2)] = (1/3) * (1/2) / [(1/3) * (1/2) + (2/3) * (1/2)] = (1/3) * (1/2) / (1/2) = 1/3
P(win | stay) = 1/3 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们看看给定你的开关赢的概率。(虽然 Baye's 并没有真正为我们购买任何东西，因为我们有一个很短的时间表来描述我们的一切，但为了彻底起见，我会从两个方面来做)。

```
P(win | switch) =  [P(win) * P(switch | win)] / P(switch)  

P(win) = (Universe 1 && Universe 1.1) || (Universe 2 && Universe 2.2) =  (1/3) * (1/2) + (2/3) * (1/2) = 1/2    

P(switch | win) = Out of wins possibilities only, what percentage of those did you switch for?  
=  (Universe 2 && Universe 2.2)  / [(Universe 1 && Universre 1.1) || (Universe 2 && Universe 2.2)] = (2/3) * (1/2) / (1/2) = 2/3

P(switch) = (Universe 1 && Universe 1.2) || (Universe 2 && Universe 2.2) = (1/3) * (1/2) + (2/3) * (1/2) = 1/2

P(win | switch) = [1/2 * 2/3] / 1/2 = 2/3 
```

Enter fullscreen mode Exit fullscreen mode

或者，你可以说:

```
P(win | switch) : all wins which lie within switch universes
P(win | switch) = (Universe 2 && Universe 2.2) / [(Universe 1 && Universe 1.1) || (Universe 2 && Universe 2.2)]
P(win | switch) = [(2/3) * (1/2)] / [(1/3) * (1/2) + (2/3) * (1/2)] 
P(win | switch) = (2/3) * (1/2) / (1/2) = 2/3 
```

Enter fullscreen mode Exit fullscreen mode

从我们的数学分析中，我们可以看到，如果你选择跳槽，你获胜的可能性是留下的两倍。如果你每次都换，你也会比随机选择换或留(在这种情况下，你会赢 1/2 的时间)赢更多(2/3 的时间)

**用逻辑**分析:如果你选择**总开关**，那么如果你**最初**选错门，你**总**就是对的。所以，由于你最初选择错误(1- 1/3 = ) 66.67%的时间，你将保证选择正确的**门 66.67%的时间，如果你**总是**开关。如果你选择**从不**切换，只有当**你在第一次尝试时从 3 扇门中选择了正确的门，你才能保证是正确的**，这是 33.3%的几率。**

**扩展这个问题以获得更好的直觉**:有时当一个问题似乎很难理解时，我喜欢将数字发挥到极致，同时保持必要的关系。在这种情况下，我们知道，在您选择 1 扇门后，主持人将打开**所有的门**，但**会打开另外一扇门**(即使在我们有 3 扇门的情况下，这只是他打开了一扇门)，这些门必须没有奖品。所以，利用这个关系，我们把情况扩大到一奖 100 万门。现在，在你选择了一扇门后，游戏节目主持人必须打开除了一扇门之外的所有门。他也打不开有奖的那个。所以，除非你认为你在第一次尝试中以百万分之一的几率选择了正确，否则你不认为这是一笔很好的交易吗，因为主持人实际上是被迫告诉你奖品在哪里？！这是完全相同的概念，除了现在当我们转换时，我们获得奖金的几率是 999，999/1，000，000，而不是 2/3。这个概念这样展开就容易有直觉多了。

现在，我们的假设与我们的实验数据相符，我们可以收工了。