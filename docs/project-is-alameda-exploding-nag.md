# 项目:阿拉米达爆炸了吗？

> 原文：<https://dev.to/thatamymac/project-is-alameda-exploding-nag>

我住在旧金山湾区的阿拉米达岛上。我们的东面与奥克兰体育馆相邻，西面与美国电话电报公司公园相邻，仅隔着 3 英里远的水域。当这些地方有烟火时，我们能听到，有时能看到。在假日里，我们听到周围所有的烟火声。我们也有蓝天使飞过等等。

简而言之，我们已经习惯了听到噪音，我通常会转向我的丈夫，说，“一定是运动球”，然后耸耸肩。但是一个半星期前，我们听到了各种各样的烟火声，我丈夫说，“我希望有一个网站，我可以去验证为什么我听到了烟火声。”(那是 A 队赢了一场比赛。)这又让我想到了“奥克兰着火了吗？”觉得做这样简单的东西会很有趣。

因此，本周我决定快速完成一些东西，并为能够真正从事一次个人编码项目而感到非常兴奋，因为我往往没有太多属于自己的时间。

## 节假日

我认为 Sinatra 很适合快速启动和运行一些东西，因为它只有一页，然后开始查看任何插件或 API，它们会很容易给我一个假期列表。因为我真的只关心那些有 fireworks 的，我可以硬编码信息，但真的希望网站尽可能自动化和易于维护。我最终找到了[这个假期红宝石](https://github.com/holidays/holidays)，这是我所需要的完美。也很容易检查那一周是否有任何烟花假期，因为对于独立日，我们倾向于在前后几天听到一些事情，即使它们在技术上是非法的。

```
FIREWORK_HOLIDAYS = [
  "Independence Day",
  "New Year's Eve",
  "Lunar New Year's Day",
  "The second day of Lunar New Year",
  "The third day of Lunar New Year"
].freeze

def check_holidays
  logger.info "Checking Holidays"
  today = Date.today
  holidays = Holidays.on(today, :us, :hk)

  # Might be the week of Independence Day, etc
  if holidays.empty? && Holidays.any_holidays_during_work_week?(today)
    holidays = Holidays.next_holidays(1, [:us, :hk])
  end

  # We only care about the holidays that might have explosions
  holidays.map { |holiday| holiday[:name] }.detect { |hol| FIREWORK_HOLIDAYS.include?(hol) }
end 
```

Enter fullscreen mode Exit fullscreen mode

可能会有一些额外的假期要添加，但我会等待扩展它。此外，由于假期不会定期改变，我将很快添加缓存。

## 体育

然后我发现最繁重的工作是寻找关于运动会的信息。我认为我需要使用网络抓取来获取信息，并开始设置 [Nokogiri](http://www.nokogiri.org/) 和[http party](https://github.com/jnunemaker/httparty)。然而，我很快意识到，所有我想从其获取信息的网站都使用 JavaScript 在初始页面加载后加载信息。我以前没有做过太多的网络搜集工作，并且做了一些关于如何解决这个问题的研究，很快就看到了[这个页面](https://gohighbrow.com/scraping-javascript-heavy-websites/)，它提到了只访问带来信息的 URL，因为 JavaScript 通常在前端操作 JSON 之前都会访问它。当然，我读了这本书，然后说，“哦，那更简单。”

所以我找到了所有体育网站的幕后 URL，抛弃了 Nokogiri，因为它完全没有必要，并开始浏览所有返回的 JSON，以找到我需要的信息。

```
class Scraper
  attr_accessor :check_for_games

  ...

  def grab_page_info(team)
    case team
    when "Athletics"
      HTTParty.get(baseball_url("531221"))
    when "Giants"
      HTTParty.get(baseball_url("531220"))
    when "Warriors"
      HTTParty.get(basketball_url)
    end
  end

  def winning_baseball_game?(response)
    response = response["dates"].first

    home_game = response["date"] == simple_date && VENUE_NAMES.include?(response["games"][0]["venue"]["name"])

    return false unless home_game

    night_game = response["games"].first["dayNight"] == "night"
    is_winner = response["games"].first["teams"]["home"]["isWinner"]

    home_game && night_game && is_winner
  end

  def check_for_games
    games ||= []

    SPORTS_TEAMS.each do |team|
      puts "Checking site for #{team}"

      response = grab_page_info(team)
      return unless response.code == 200

      if team == "Warriors"
        home_game = response["games"].detect do |game|
          game["home"] == true && game["date"] == simple_date
        end

        games << team unless home_game.nil?
      else
        games << team if winning_baseball_game?(response)
      end
    end

    games
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是这样。我把它放在 Heroku 上，然后开始摆弄它，让它更快。在 JSON 被检索和解析之后，我最终添加了 IronCache 来存储结果，这是最大的瓶颈。

## 总结

这是一个有点乏味的项目，但很有趣。以下是您可能会看到的一些变化。

[![No idea, it's either in your head or it could be Armageddon](img/ec9574273f3f078c3fdef21d5d55949d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XhAgxWuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://amy-mac.cimg/alameda_exploding1.jpg)[![Probably because it's Independence Day](img/47f7f973da7093ff755bbb965f270f46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HlFK-W0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://amy-mac.cimg/alameda_exploding2.jpg)[![The Giants are playing tonight and just won](img/1a2b080349fe8d4305579f03a58f5afa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iEHte4AH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://amy-mac.cimg/alameda_exploding3.jpg)

在未来，我可能会添加一些检查，看看是否是舰队周，以及其他这样的大干扰来扩展它。希望阿拉梅丹人喜欢它！可以查看[现场](http://isalamedaexploding.com)或者在 Github 上查看[项目。](https://github.com/amy-mac/alameda_exploding)