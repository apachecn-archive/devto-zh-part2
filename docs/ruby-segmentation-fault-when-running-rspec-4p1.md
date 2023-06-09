# 运行 RSpec 时出现 Ruby 分段错误

> 原文：<https://dev.to/andy/ruby-segmentation-fault-when-running-rspec-4p1>

所以我得到了这个`segmentation fault`错误，根据我所听到和读到的，这是一个低级的 Ruby 错误。当我通过 bin 命令运行所有规范时，就会发生这种情况。我已经遇到这个错误几次了，不管是什么原因，它在一两个小时内就自己解决了。我没有运行任何超级消耗内存的测试，鉴于我们的 CI 在 100%的时间里运行得很好，我不认为这是测试的问题。

以下是日志片段:

```
............................................................................................../Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/2.5.0/open-uri.rb:408: [BUG] Segmentation fault at 0x000070000dfbf4b0
ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-darwin16]

-- Crash Report log information --------------------------------------------
   See Crash Report log file under the one of following:
     * ~/Library/Logs/DiagnosticReports
     * /Library/Logs/DiagnosticReports
   for more details.
Don't forget to include the above Crash Report log file in bug reports.

-- Control frame information -----------------------------------------------
c:0080 p:---- s:0452 e:000451 CFUNC  :write
c:0079 p:---- s:0449 e:000448 CFUNC  :<<
c:0078 p:0011 s:0444 e:000443 METHOD /Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/2.5.0/open-uri.rb:408
c:0077 p:0012 s:0438 e:000437 BLOCK  /Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/2.5.0/open-uri.rb:353
c:0076 p:0007 s:0434 e:000433 METHOD /Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/2.5.0/net/protocol.rb:433
c:0075 p:0018 s:0429 e:000428 METHOD /Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/2.5.0/net/protocol.rb:424
c:0074 p:0166 s:0424 e:000423 METHOD /Users/andyzhao/.rbenv/versions/2.5.0/lib/ruby/gems/2.5.0/gems/webmock-3.3.0/lib/webmock/http_lib_adapters/net_http_response.rb 
```

Enter fullscreen mode Exit fullscreen mode

我运行的是 8 GB 内存的 macOS High Sierra 10.13.5。

如果有人感兴趣，下面是通过 Google Drive 获得的最近三个崩溃报告文件的链接:
[https://Drive . Google . com/file/d/1g 7 spoquidthuth-qhwj 7 akigtaw 7 mnnh/view？usp =分享](https://drive.google.com/file/d/1G7sPOquIDTHUTh-qhwJ7AKIgtaw7MNNh/view?usp=sharing)

[https://drive . Google . com/file/d/1 oiahfzatclnd 2 yz 6 fg _ zrydybd 2 ehg 6 p/view？usp =共享](https://drive.google.com/file/d/1OIAhfZAtclNd2YZ6FG_zRYDYBd2eHG6p/view?usp=sharing)

[https://drive . Google . com/file/d/1 qegihkyap 1 hlx k 7 lai jub 26 aqt 8 ngjpl/view？usp =共享](https://drive.google.com/file/d/1qeGIhKYAp1HlxK7LaijuB26AQt8NGjpL/view?usp=sharing)