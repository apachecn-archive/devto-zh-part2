# 推动器项目-为什么我永远不会是 Devops

> 原文：<https://dev.to/itsasine/pusher-project---why-ill-never-be-devops-520d>

周日已经过去 12 个小时了。工作周重新开始前的最后一天。我大学期末考试周的最后一天。还有，我所在的城市举行大型马拉松比赛的那天(我住在主干道附近...傻呼呼的健康人)。

我做了什么？诅咒 AWS。**反复地。**

谈到 AWS，我的工作流程是，如果有什么东西不工作，我就销毁它，然后再试一次。所以我已经销毁了我的 AWS 资源...今天 4 次了。这是我现在拥有的:

[![Blank page with Pusher running](../Images/8963bcebb1b813db31ff38c15676f6cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OeuRMzvR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/or7j0rms75s13s91uq47.png) 
*你知道这是什么吗？进度*

* * *

我最初设置了一个 CodeStar 实例，它会自动设置构建管道的每个部分，甚至会为您设置一个 Github repo。然后我做得太多太快，破坏了建筑管道。纽克，再试一次。

第二个实例试图正确设置环境变量。打破管道，用核武器，再试一次。

第三个实例是试图建立一个我自己的一些 Elasticbeanstalk 项目模板，并试图建立它。不，核武器。

Fourth instance 更进一步，认为我应该尝试真正理解 CodeStar 技术堆栈，并自己手工制作。那太痛苦了。权限，S3，管道，代码构建...没错。

第五例:回到开头。采用我的默认节点 Lambda CodeStar 全新构建，添加 Pusher 的默认 JS，然后尝试构建它。一开始有点花哨，但失败了，所以我删除了代码并重新构建。看起来这个现在起作用了！尽管我不得不把这个 CodeStar repo 设置成一个私有的，因为我还不知道环境变量是如何工作的，而且你们这些互联网上的人也不知道我的秘密。

* * *

## 至此费用明细

*   域名(尚未使用)-12 美元/年
*   Python Dev Humblebundle 这样我就可以为他们的 AWS 视频获得一个便宜的 egghead.io 许可——20 美元
*   AWS 学生津贴和免费层的东西-免费！
*   推动器默认沙盒无层！
*   我的整个周日——无价之宝(不完全是——我本来是在做家务或做辐射尘 4)

* * *

## 事情还没做完

*   整个前端。这是一张白纸。
*   几乎整个后端

## 没有留下来做的事情

*   部署堆栈！
*   为这个应用程序取个名字——我告诉我的男朋友这是一个卖表情符号的商店，所以他把它命名为 **Shopoji** 。

## 拥有者的美好

*   弄清楚如何在构建时传递像密钥这样的信息，这样我就可以再次开放源代码
*   获取 DNS 设置权限以使用我的域名
*   实际上阅读和观看我为 Python 买的东西只是为了得到那个 egghead.io 密钥

* * *

以前的帖子

[![itsasine](../Images/3849aa01c92d200e0764b4a9ed4cb805.png)](/itsasine) [## 推销商店游戏-最初的想法

### 伊塔辛(凯拉)5 月 5 日 183 分钟阅读

#pushercontest #inprogress](/itsasine/pusher-shopkeep-game---initial-idea-36po)