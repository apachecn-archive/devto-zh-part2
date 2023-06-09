# 用 Octokit 和 fzf 搜索 GitHub 明星

> 原文：<https://dev.to/citizen428/search-github-stars-with-octokit-and-fzf-25p0>

# 用 Octokit 和 fzf 搜索 GitHub 明星

如果你和我一样，你正在使用 Github 的存储库 stars 作为你以后想要查看的有趣项目的书签。如果你真的像我一样(一般不建议)你喜欢命令行，并认为如果你有一个简单的方法从那里搜索你的星星会很棒。今天我有一个小时的空闲时间，所以我终于找到了一个真正适合我工作流程的解决方案，也许它也会吸引你。

### 使用 Octokit 获取带星号的存储库

[Octokit](http://octokit.github.io/) 是由 GitHub 团队编写的“使用 GitHub API 构建惊人体验的简单而官方的方法”。它非常方便，我用它为自己编写了一个名为`stars`的小工具，它将获取我的标有星号的库并输出它们的全名、描述和 URL，用制表符分隔。默认情况下，它将只返回最新的 30 个存储库(API 响应的第一页)，但是也可以在被调用为`stars all`时检索所有的存储库。

这是所有非荣耀的自我解释脚本:

```
#!/usr/bin/env ruby

require 'octokit'

Octokit.auto_paginate = true if ARGV[0] == 'all'
client = Octokit::Client.new(access_token: 'your_access_token')

starred = client.starred('your_username')
starred.each do |star|
  puts "#{star.full_name}\t#{star.description}\t#{star.html_url}"
end 
```

Enter fullscreen mode Exit fullscreen mode

(注意:访问令牌是可选的，但是允许更多的 API 请求，这在开发时很有用)

这会生成以下输出(缩写):

```
→ stars
tagomoris/deferral  Golang-style defer in Ruby  https://github.com/tagomoris/deferral
iridakos/duckrails  A development tool to quickly & dynamically mock API endpoints :duck:   https://github.com/iridakos/duckrails
baweaver/qo Qo - Query Object - Pattern matching and fluent querying in Ruby    https://github.com/baweaver/qo
trailofbits/ethersplay  EVM dissassembler   https://github.com/trailofbits/ethersplay
letsgetrandy/brototype  Bro, do you even?   https://github.com/letsgetrandy/brototype
devise-security/devise-security A security extension for devise, meeting industrial standard security demands for web applications. https://github.com/devise-security/devise-security 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 fzf 查找存储库

如果你不知道 [fzf](https://github.com/junegunn/fzf) ，那就帮自己一个忙，马上去查一下。这是一个用 Go 编写的非常棒的命令行模糊查找器，不仅速度快，而且提供了非常全面的功能集。

为了将我的`stars`实用程序与`fzf`集成，我在我的`.zshrc` :
中添加了一个名为`sstars`(意为“搜索星星”，没有原创奖)的功能

```
function sstars() {
  local url
  url=$(stars "$1" | fzf -e | cut -f3)
  open $url
} 
```

Enter fullscreen mode Exit fullscreen mode

这期望`stars`在你的道路上是可用的。然后，它将把输出通过管道传输到`fzf`，将该行的第三个字段(URL)赋给一个变量，并让 macOS 的`open`实用程序处理剩下的事情。

下面是该工具运行时的屏幕截图:

[![Searching for Haskell repositories I starred when I still believed in a better world for software developers](img/85eaf075abac7875535012d801b9b8e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zhQENxTS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlrnswyfp6yt4mn0hzid.png)

### 那都是乡亲们！

挠个人痒痒总是开始一个
小周末项目的最好动力，在这个过程中我确实了解了 Octokit，所以这是一个
很好的副作用。