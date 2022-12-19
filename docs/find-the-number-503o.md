# 找到号码

> 原文：<https://dev.to/thomasbnt/find-the-number-503o>

[![Find The Number](../Images/6fe9a15b9d1fee1b7200d032db88c885.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xeM1SyH7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a78xl46305veutcgcdix.png)

*您好！代码和帖子显示了我在 Ruby 中的第一个脚本，请宽容。*

*   **描述**

这是一个必须用 ruby 运行的控制台/终端下的基本“游戏”。
目标？找到确切的数字。

*   **系统**

一个简单的随机数和一个条件来检查它是大还是小。如果他太大或太小，他会给你做标记。

[![Example of the script](../Images/dc54fb65700c0b1fd79bb61194be7656.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iozRP9Yy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3nkp01kzqvfb4982zlhc.gif)

*   **代码**

```
MaxChiffre = 20
NumberToGuess = Random.new.rand(MaxChiffre)
MaxChance = Random.new.rand(2..6)
a = nil
puts "Enter your number between 0 and #{MaxChiffre} included, you only have #{MaxChance} chances."
for num in 1..MaxChance
  next if a == NumberToGuess
  a = gets.chomp.to_i
  if a > NumberToGuess
    puts "Number too big"
  elsif a < NumberToGuess
    puts "Number too small"
  end
end
puts "Congratulations, you've found it !" if a == NumberToGuess
puts "Pity ! It was #{NumberToGuess}. " if a != NumberToGuess 
```

Enter fullscreen mode Exit fullscreen mode

*   **GitHub**

资源库在 GitHub 上，现在就去看看吧。

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png)/[找号码](https://github.com/thomasbnt/Find-The-Number)

### 我的第一个 Ruby 脚本。