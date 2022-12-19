# dev.to 如何动态生成社交图像

> 原文：<https://dev.to/ben/how-devto-dynamically-generates-social-images--2c2n>

没有封面图片的 [dev.to](https://dev.to/) 帖子有一个设计良好的“卡片”非常重要，当 Twitter 用户滚动浏览提要时，它可以抓住用户的注意力。我不确定我们的设计是否都那么好，但是他们*可能是*，那部分并不是真的关于代码。

我们收到很多关于如何动态生成这些默认图像的问题。我一直犹豫写这篇文章，因为我仍然认为我们可以做得更好。但是由于我们没有采取不同的方法，这里有一个关于我们当前方法的帖子，并描述了可能的替代解决方案。

### 这是一个#讨论帖子的 Twitter 卡片示例

[![](img/d4107140c6b6bae6592f90ea82af487c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XhONnVF5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5wug0xrfibr56uhe3o1w.png)

## 我们做什么

我们使用 [Cloudinary](https://cloudinary.com) 作为图像管理/操作服务和 CDN。他们有一些功能，比如[给图像添加文本覆盖](https://cloudinary.com/blog/adding_watermarks_credits_badges_and_text_overlays_to_images)，我们用它来生成图像。因此，最终的问题是摆弄各层，直到你得到它的权利，然后编写代码，使我们的应用程序与服务进行编程互动。这很简单，但是我们的创造力受到系统内我们能做的事情的狭窄范围的限制。这是一项伟大的服务，但我们生成的图像模板是一种痛苦的改变。难以更改的代码不是好代码。

## 我们可以采取的其他方法

### 用 HTML 生成图像，然后使用服务进行截屏

这将给我们更多的灵活性和对设计的控制。我认为这将是我们理想的方法。Cloudinary 本身以插件的形式提供这项服务，但我们还没有转而使用它。

### 在我们的服务器上动态生成图像。

ImageMagick 是一个经典的库，具有很多可扩展性。在我看来，这很受欢迎，但工作起来有点痛苦。像 Cloudinary 这样的服务之所以存在，是因为 ImageMagick 是一种痛苦。我确信有其他的库依赖于你的后端生态系统，但是我不知道什么是好的选择。

### 服务如[分享喵](https://github.com/producthunt/ShareMeow)

对于一种非常特殊类型的生成图像，ShareMeow 是一个有趣的自托管网络服务，我们已经尝试过了。

## 外卖

我们正在做的工作，但我不确定我会推荐它。如果我是你，我会直接跳到通过 HTML 或其他好的服务来生成这些图片。

# 整个过程中另一个有趣的部分

这个过程不仅仅是生成图像。我们还需要一个缓存破坏机器人，以便定期更新卡。如果我们想显示随着时间的推移不断增长的评论数量，我们不能等待 Twitter 每周一次刷新缓存。它仍然不会立即更新，但如果一条推文已经传播了一段时间，它会显示准确的评论数，即使它比该推文最初发布时更多。

Twitter 没有通过 API 公开他们的缓存破坏功能，所以我们不得不通过浏览器自动化来 ping 它。

这是一个 Ruby 文件，它利用 Capybara 和 Selenium 来自动化 ping Twitter 卡缓存验证的过程。它基本上使用了这三种方法:

```
 def visit_twitter_and_log_in
    visit 'https://cards-dev.twitter.com/validator'
    find('input.js-username-field').set(ENV['TWITTER_USERNAME'])
    find('input.js-password-field').set(ENV['TWITTER_PASSWORD'])
    click_on('Log in')
  end 
```

Enter fullscreen mode Exit fullscreen mode

```
 def enter_url_and_click_preview(url)
    find('input.FormControl').set(url)
    click_on('Preview card')
    result = has_content?('Page fetched successfully')
    visit 'https://cards-dev.twitter.com/validator'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

第二种方法需要通过一些垃圾缓存破坏来执行几次 pinged 操作。这是调用它的方法:

```
 def enter_urls
    urls.each do |url|
      3.times do
        enter_url_and_click_preview("#{url}?#{rand(10_000)}=#{rand(10_000)}")
      end
      enter_url_and_click_preview(url)
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。构建所有这些是自动化一些潜在手工工作的好方法。这一切都是为了让更多用户分享内容时， [dev.to](https://dev.to/) 服务变得更有价值。