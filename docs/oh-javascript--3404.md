# 哦 Javascript...🙄

> 原文：<https://dev.to/kspeakman/oh-javascript--3404>

刚刚发现了这个小宝石。

```
new Date('2018-01-01')
<- Sun Dec 31 2017 18:00:00 GMT-0600 (Central Standard Time)

new Date('2018/01/01')
<- Mon Jan 01 2018 00:00:00 GMT-0600 (Central Standard Time) 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况的原因(我认为)是因为 JS 将第一个日期解析为 ISO 8601 格式。因为没有指定时区偏移量，所以它采用 UTC。然而，JavaScript 只会创建本地时间的日期。因此，应用当地时区后，现在是前一天的下午 6 点。

第二个使用了一个`/`分隔符，所以它不会触发 ISO 解析分支。相反，它合理地假设我对当前时区的日期感兴趣。