# 设置后缀、鸽笼和筛子

> 原文：<https://dev.to/foursixnine/setting-up-postfix-dovecot-and-sieve-b9d>

# 恐怖

在尝试设置我的邮件系统时，我跑进了多个教程，以找出避免多个错误信息的最佳方法，主要是因为你，像我一样(你这个愚蠢的人类！)，只是简单地从 stack overflow 和 howtoforge 等地方的教程中复制粘贴一些随机的东西…

## 错误百出

你尝试过这样的事情吗

```
spamassassin unix - n n - - pipe flags=DROhu user=vmail argv=/usr/bin/spamc -e /usr/lib/dovecot/deliver -f ${sender} -d ${user}@${nexthop} 
```

还是这个:

```
mailbox_transport = lmtp:unix:private/lmtp
virtual_transport = lmtp:unix:private/lmtp 
```

## 痛苦

所以你最终得到了类似这样的东西:

```
Oct 24 01:13:24 nergal postfix/pipe[10207]: fatal: get_service_attr: unknown username: vmail
Oct 24 01:13:25 nergal postfix/master[10104]: warning: process /usr/lib/postfix/bin//pipe pid 10207 exit status 1
Oct 24 01:13:25 nergal postfix/qmgr[10106]: warning: private/spamassassin socket: malformed response
Oct 24 01:13:25 nergal postfix/master[10104]: warning: /usr/lib/postfix/bin//pipe: bad command startup -- throttling
Oct 24 01:13:25 nergal postfix/qmgr[10106]: warning: transport spamassassin failure -- see a previous warning/fatal/panic logfile record for the problem description 
```

## 辞职

所以对我来说有用的是把服务留在 master 中

并且简单添加到 master.cf

```
spamassassin unix - n n - - pipe flags=R user=app argv=/usr/bin/spamc -e /usr/sbin/sendmail -oi -f ${sender} ${recipient} 
```

总的来说. cf

```
mailbox_command = /usr/lib/dovecot/deliver 
```

## 光明

在这些变化之后，筛网过滤开始工作:)

*   查看这篇文章，这篇文章给了我一条救生索