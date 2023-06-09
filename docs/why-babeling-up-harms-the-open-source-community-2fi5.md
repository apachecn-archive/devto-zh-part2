# 为什么“babeling”会伤害开源社区

> 原文：<https://dev.to/fohlen/why-babeling-up-harms-the-open-source-community-2fi5>

### 序言

这篇文章意在特别讨论对待进步的技术态度。这绝不意味着侮辱或贬低巴别塔或类似工具背后的人们所做的伟大工作。请保持下去，你很棒，开源世界绝对需要你！

## “混乱”及其危害

所以你已经建立了一个全新的网站，或者服务，或者一段代码。现在是时候去真棒，并运送到世界各地！好吧，让我们做一个清单

*   [x]确保在发布前修复所有错误
*   [x]确保您有一个很棒的发布名称
*   [ ]确保它可以在任何地方运行
*   [x]打包，上传。给你。世界爱你，❤️

现在。我们清单上的第三部分特别麻烦。根据我与幕后工作人员一起工作的经验，我知道这几乎是 T2 的全部工作。你认为你已经完成了发布一个惊人的版本，但是页面对你不利，现在是**加倍**时间让它到处工作。这样的事情在我身上发生了一次，两次，第三次，一次又一次。句号。有一次，我和我的同事花了整整一周的时间才在 IE10 上发布了我们的最新功能。我想知道为什么会这样，这对商业部门来说有点意义。然而，开源社区中有一种令人担忧的趋势，试图解决这个问题。我想到的一些项目有:

*   以打字打的文件
*   咖啡脚本
*   巴比伦式的城市
*   网络包

这些都试图解决一个共同的问题:给老平台带来新的特性。我们试图用 polyfills 来安慰我们的用户，最终我们花费了大量的时间来支持所有过时的环境。我认为这必须停止。
我接下来想到的问题是:*“我们如何让我们的技术更加进步”*。我记得有一次我帮助我的一个同事向 python 委员会提交了一个特性提案，据我所知，那是一次非常艰难的经历。通读 W3C 标准组和 ECMA 标准提案都不令人满意。
编写和测试增强应该容易得多。对于浏览器标准来说尤其如此，一个新的想法可能需要几年到几个世纪才能实现。因此，我建议那些项目(是的，我正看着你 libcef，尽管你最近做得更好)提供方便地编写和发布语言和框架增强的机制。

## 为什么推动实施负担还不够

鉴于我们有那些令人惊叹的 API 和工作流来编写增强功能，这不会真正改变什么。这是技术进步的良好开端。尽管这并没有真正改变什么。当我们仔细看看软件业的历史时，人们为改善这种状况付出了巨大的努力。为什么还没有成功？答案很简单:心态。大多数开发人员害怕更新会让他们的用户不高兴。这对于企业来说可能是不可行的。对于开源来说，这种态度严重阻碍了开发。作为一个社区，我们应该迫使用户跟上步伐。为什么支持 IE10？为什么支持 Windows XP？老实说，没有人应该使用它，我们应该保护整个软件社区不使用不赞成使用的环境。这是一篇鼓励的文章！不要害怕你的用户。打破他们的工作流程。进步开源。只有在我们强迫用户使用最新环境的心态下，变革才会最终到来。
*所以请*:下次你开始一个新项目的时候，不要用 babel，不要用 python2，不要用 node4。如果你的用户不能改变他们的环境，这当然不是你的责任。记住:**你**把你的空闲时间和精力投入到那个项目中。如果你的用户不能更新，因为他或她在一个受保护的环境中，黑客，他们应该要求他们的管理员使之成为可能！。只有这样的态度才会给最终用户施加足够的压力，从而使进展成为可能。