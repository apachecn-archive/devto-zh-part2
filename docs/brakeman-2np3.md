# 司闸员

> 原文：<https://dev.to/jeancarlosn/brakeman-2np3>

在 RoR 应用的安全工具，非常好的工具

充气-> `gem install brakeman`

查看版本-> `brakeman --version`

在应用程序中运行工具以输出文件 html -> `brakeman -o brakeman.html`

工具显示这个-->

Result

Loading scanner...
Processing application in /home/user/aplication
Processing gems...
[Notice] Detected Rails 5 application
Processing configuration...
[Notice] Escaping HTML by default
Parsing files...
Processing initializers...
Processing libs...ed
Processing routes...
Processing templates...
Processing data flow in templates...
Processing models...
Processing controllers...
Processing data flow in controllers...
Indexing call sites...
Running checks in parallel...
- CheckBasicAuth
- CheckBasicAuthTimingAttack
- CheckCrossSiteScripting
......