# 在 Ubuntu 中安装 Rails

> 原文：<https://dev.to/jeancarlosn/install-rails-in-ubuntu-31nc>

我喜欢 rails，因为它是我工作过的最好的框架，但是安装它并不容易，这里我展示一下我的经验

第一步:安装 Ruby 语言-> `sudo apt install ruby`

第二步:从 Ubuntu 安装 Gem -> `sudo apt install gem`

第三步:更新 Gem -> `gem update --system`

第四步:安装 RubyOnRails - > `gem install rails`

步骤 4.1:如果安装程序给你这个:`zlib is missing; necessary for building libxml2`他们
安装这个库:`zlib1g-dev`用这个命令行命令- > `sudo apt-get install zlib1g-dev`

步骤 4.2:如果看到此错误-> `ERROR: Error installing rails:
ERROR: Failed to build gem native extension. current directory: /var/lib/gems/2.3.0/gems/nokogiri-1.8.1/ext/nokogiri`

然后运行它这一行命令-> `sudo apt-get install ruby-dev`

最后一步:对于 rails 测试，您可以使用这一行:`rails --version`。

给它看这样的东西:`Rails 5.1.4`

后会有期！