# 在 Heroku 上运行模块化的 Sinatra 应用程序

> 原文：<https://dev.to/fullstackplus/running-a-modular-sinatra-app-on-heroku-4d68>

你好，我无法在生产中正确运行模块化 Sinatra 应用程序。`git push heroku master`运行良好，但是当我试图通过`/contact`页面提交表单时，我得到:

`bundler: failed to load command: rackup (/app/vendor/bundle/ruby/2.4.0/bin/rackup)`。

我的`Procfile:`

```
web: bundle exec rackup config.ru -p $PORT 
```

Enter fullscreen mode Exit fullscreen mode

我的`config.ru:`

```
require 'rubygems'
require 'bundler'

Bundler.require

require_relative 'lib/app'
run Tir::App 
```

Enter fullscreen mode Exit fullscreen mode

我的`lib/app.rb` :

```
require 'sinatra/base'
require File.expand_path('articles', File.dirname(__FILE__))
require File.expand_path('static', File.dirname(__FILE__))
require File.expand_path('landing', File.dirname(__FILE__))

module Tir
  class App < Sinatra::Base
    #code
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我已经尝试了各种方式的调整，但我卡住了！感谢任何帮助。