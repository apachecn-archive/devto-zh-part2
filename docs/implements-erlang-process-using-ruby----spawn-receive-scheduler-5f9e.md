# 使用 Ruby - spawn、receive、scheduler 实现 Erlang 流程

> 原文：<https://dev.to/chenge/implements-erlang-process-using-ruby----spawn-receive-scheduler-5f9e>

这是别人写的，我觉得很好玩所以贴在这里。

```
 require "./erl_scheduler"
require "./erl_process"

scheduler = ErlScheduler.new
counter = scheduler.spawn(Counter)

10000.times do |i|
  scheduler.send_message(counter, :inc)
end

scheduler.send_message(counter, :stop)
scheduler.loop 
```

[翻译链接- >](https://translate.google.com/translate?sl=auto&tl=en&js=y&prev=_t&hl=en&ie=UTF-8&u=https%3A%2F%2Fruby-china.org%2Ftopics%2F37750%23Example&edit-text=)