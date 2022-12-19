# 安装 Ruby 2.5.0

> 原文：<https://dev.to/andy/installing-ruby-250--2pe1>

我刚刚安装了刚刚过去的圣诞节(2017 年 12 月 25 日)发布的 [Ruby 2.5.0](https://www.ruby-lang.org/en/news/2017/12/25/ruby-2-5-0-released/) 。总而言之，路上没有颠簸，花了大约 13 分钟。相当棒！

[![computer kid thumbs up meme](img/cbb3c1dd31e45e122f7c7464d6c9a9bd.png)T2】](https://i.giphy.com/media/111ebonMs90YLu/giphy.gif)

我只在工作回购时需要它，但我也很高兴拥有它。我也不太确定该怎么做，就说，“好吧，就这样。”

```
git pull upstream master
# pulled from repo with new Ruby version set
rbenv install 2.5.0
#=> ruby-build: definition not found: 2.5.0
#
# The following versions contain `2.5.0' in the name:
#  2.5.0-dev
#  rbx-2.5.0
#
# See all available versions with `rbenv install --list'.
#
# If the version you need is missing, try upgrading ruby-build:
#
# brew update && brew upgrade ruby-build
brew update && brew upgrade ruby-build
#=> completed with no errors
rbenv install 2.5.0
#=> Installed ruby-2.5.0
rbenv global 2.5.0
# set Ruby version for the whole machine
ruby -v
#=> ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-darwin16]
bundle install
#=> rbenv: bundle install: command not found

# The `bundle install' command exists in these Ruby versions:
#  2.3.4
gem install bundler
# 1 gem installed
bundle
# install all gems to new Ruby version needed for repo 
```

Enter fullscreen mode Exit fullscreen mode

就这样了！嗯，只是为了更好地衡量，我迁移了:

```
bin/rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

希望对你来说也一样简单！