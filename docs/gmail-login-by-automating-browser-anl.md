# 通过自动化浏览器登录 Gmail

> 原文：<https://dev.to/manojnaidu619/gmail-login-by-automating-browser-anl>

#### 先决条件

*   确保你运行的是最新版本的火狐浏览器
*   确保您已经在 ruby 可执行路径中安装了 geckodriver(一个 firefox webdriver)
*   运行`gem install watir`安装 watir gem

### 表演时间到了！！

```
require 'watir'                       
browser = Watir::Browser.new(:firefox)         # To launch the browser
browser.goto("https://www.gmail.com")
sleep 2
browser.text_field(class: "whsOnd zHQkBf").set "{ YOUR GMAIL ADDRESS HERE }"  
sleep 2
browser.span(index: 4).click
sleep 2
browser.text_field(name: "password").set "{ YOUR GMAIL PASSWORD HERE }"
sleep 2
browser.send_keys :enter 
```

Enter fullscreen mode Exit fullscreen mode

最后，cd 到终端中的脚本路径并键入

`ruby FILENAME.rb`

坐下来观看它的运行！！