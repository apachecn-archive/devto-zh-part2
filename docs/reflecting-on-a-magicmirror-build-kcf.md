# 对 MagicMirror 构建的思考

> 原文：<https://dev.to/hardy613/reflecting-on-a-magicmirror-build-kcf>

# 简介

被评为 Raspberry Pi 最佳项目的[魔镜](https://magicmirror.builders/)，建造起来很有趣，价格适中，并为进一步的开发提供了一个平台。该程序由[电子](https://electronjs.org/)包装，通过一些配置[树莓 Pi](https://www.raspberrypi.org/) 可以通过旋转屏幕直接启动到应用程序中。

作者 Michael Teeuw 有一份[项目宣言](https://github.com/MichMich/MagicMirror#manifesto)。简而言之:为新的和有经验的开发者提供一个可接近的项目。该项目是开源的。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [米西米西](https://github.com/MichMich) / [魔法镜像](https://github.com/MichMich/MagicMirror)

### MagicMirror 是一个开源的模块化智能镜像平台。随着可安装模块的不断增加，MagicMirror 可以让您将走廊或浴室的镜子变成您的个人助理。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![MagicMirror²: The open source modular smart mirror platform. ](img/5c81b2dc1aa7bc188917f528e3ecb61d.png)T2】](https://github.com/MichMich/MagicMirror.github/header.png)

[![License](img/486bafc059df9137c69818cd66696105.png)](https://choosealicense.com/licenses/mit)[![GitHub Actions](img/49471f08667eed576b832f292df34d26.png)](https://camo.githubusercontent.com/2acbba7cb9c255fcb1b3dd245f8d4f5ad246724984df3cd1ed41e6d8571c2451/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f776f726b666c6f772f7374617475732f6d6963686d6963682f6d616769636d6972726f722f52756e2532304175746f6d617465642532305465737473)[![Build Status](img/e8632ada309457e9e8fc86118dd667a1.png)](https://camo.githubusercontent.com/78c5baee56fa7ced34fe5f5424397b80bd4e98c623599b67d3b6885a6d57f747/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636865636b732d7374617475732f6d6963686d6963682f6d616769636d6972726f722f6d6173746572)[![CodeCov Status](img/48d1fbc00fa25f799f133ee2fb61d6cf.png)](https://codecov.io/gh/MichMich/MagicMirror)[![](img/a56caa051e61917c793d0d3df3dfa384.png)](https://github.com/MichMich/MagicMirror)

**MagicMirror** 是一个开源的模块化智能镜像平台。随着可安装模块越来越多， **MagicMirror** 可以让你将走廊或浴室的镜子变成你的私人助理。 **MagicMirror** 是由最初的 MagicMirror 的创造者在[日益壮大的贡献者社区](https://github.com/MichMich/MagicMirror/graphs/contributors)的大力帮助下打造的。

MagicMirror 专注于模块化插件系统，并使用 [Electron](https://www.electronjs.org/) 作为应用包装器。因此，没有更多的网络服务器或浏览器安装的必要！

## 证明文件

完整文档包括 **[安装说明](https://docs.magicmirror.builders/getting-started/installation.html)** ，请访问我们的专用文档网站:[https://docs . magic mirror . builders](https://docs.magicmirror.builders)。

## 链接

*   网址:[https://magic mirror . builders](https://magicmirror.builders)
*   文档:[https://docs . magic mirror . builders](https://docs.magicmirror.builders)
*   论坛:[https://forum . magic mirror . builders](https://forum.magicmirror.builders)
    *   技术讨论:[https://forum.magicmirror.builders/category/11/core-system](https://forum.magicmirror.builders/category/11/core-system)
*   不和谐:[https://discord.gg/J5BAtvx](https://discord.gg/J5BAtvx)
*   博客:[https://michaelteeuw.nl/tagged/magicmirror](https://michaelteeuw.nl/tagged/magicmirror)
*   捐款:[https://magicmirror.builders/#donate](https://magicmirror.builders/#donate)

## 投稿指南

欢迎各种形式的贡献，不仅是代码形式的，还有关于

*   错误报告
*   证明文件
*   翻译

欲了解完整的投稿指南，请查看:[https://docs.magicmirror.builders/about/contributing.html](https://docs.magicmirror.builders/about/contributing.html)

## 享受 MagicMirror？考虑捐款！

MagicMirror 是开源和免费的…

</article>

[View on GitHub](https://github.com/MichMich/MagicMirror)

# 为什么要建造它

我有一个树莓派，有一段时间没用了。在谷歌上搜索了几下后，我已经做好了建立 MagicMirror 的准备。我想在项目结束后拥有一些可以使用的实物。希望它会成为一个话题。

# 我需要构建什么

覆盆子 Pi 3 (MagicMirror 推荐使用 Pi 2 或 3)。我用一个读卡器刷新了硬盘，安装了 NOOBS。从那里我安装了 [Raspiban](https://projects.raspberrypi.org/en/projects/noobs-install) ，我按照文档安装 MagicMirror。现在我需要一台显示器。我的岳父有很多，给了我一个。我拿了一个[飞利浦显示器 190b7cs](https://www.cnet.com/products/philips-190b7cs-lcd-monitor-19-series/specs/) ，按照[在线手册](https://www.manualslib.com/manual/897546/Philips-190b7.html?page=41)安全地把多余的尽量去掉。

[![Installing](img/27083ed1735a70ac9fb5cf6cd12fe899.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t5ULvT3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wmrw9vlgl01y14rngsrk.jpg)

[![Installed](img/296dbb9395dd378b0a7a5d4d15601a10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUrFNRTP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o8wbu9l3bsyajsgytndr.jpg)

现在我需要弄清楚我想要哪种类型的镜子。起初我想用玻璃，但最终还是用了为这个项目设计的双向丙烯酸镜。

到目前为止，我的花费(CND)是:

```
- RaspberryPi 3 - $60
- MagicMirror Program - Free
- Philips monitor 190b7cs - Free
- 2-way Acrylic Mirror - $80 
```

Enter fullscreen mode Exit fullscreen mode

总共 140 加元，两个晚上的工作，剩下的就是一个相框。相当不错！

# 数控建筑框架

在这一点上，这个项目很快就完成了，我向妻子演示了这个项目。我们决定买一个深色的素色镜框。

为了建立这个框架，我向一个朋友寻求一些建议。他是当地一家翻新公司的合伙人。他推荐[中密度纤维板(MDF)](https://en.wikipedia.org/wiki/Medium-density_fibreboard) ，因为它便宜，而且如果你给它上漆，没人会知道有什么不同。对于一个普通的深色框架来说，这听起来很棒，他免费提供中密度纤维板，并用他的[电脑数控机床(CNC)](https://en.wikipedia.org/wiki/Numerical_control) 切割它。

我准备好了。

在渥太华的整修车间，我从基本意义上了解了 [CNC 刀具轨迹](http://www.cs.cmu.edu/~rapidproto/students.03/zdb/project2/CNCToolpathing.htm):如何引导 CNC 进行切割。下面是一个视频从帧被削减，警告:响亮的机械；请注意音量。

[https://www.youtube.com/embed/0xgn4g4OVwY](https://www.youtube.com/embed/0xgn4g4OVwY)

切削分多次进行，最后一次切削方向相反的原因是钻头偏斜。反方向的最后一道加工会拉直切口。这台计算机数控系统精确到 1/2000 英寸。框架切割后，我清理了一些边缘，并用砂纸打磨。然后我们拼凑了一个测试版本。

[![Test build back](img/6ecc2ece5dc054cb029be886c90c1b34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oyakin8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ccicqrozurfwau9q9xu8.jpg)

[![Test Build front](img/a10aaa5b818cc9fbf9db6159d47964ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qb7QGNR8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/44xdxz90x1dpa63oem5a.jpg)

# 绘画

建议中密度纤维板使用非水基底漆。油基底漆很难找到，我不想花太多钱。我决定选择油漆+底漆的组合。我还捡了些木头泥，我有个小修理要做。一旦涂上木泥并晾干，我就用砂纸打磨。天啊，我打磨了那个框架。下图是最终打磨后的照片。

[![sanding](img/ea94f76e9ae292d0f34874f79f70bae8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--erNQbvWo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwlofq13qqqbxt52lax8.jpg)

你可以在左上角和右上角看到，我仍然没有打磨掉所有的瑕疵。左上角有主要的木泥固定。我开玩笑说我在修复物理 bug 报告。

接下来是油漆+底漆组合。哑光黑色。在我看来，刷笔画完成赋予了一些生命的纹理中密度纤维板。正面两层，侧面和背面各一层。

[![painted](img/ee35c40278d3895839a4c3ac55129b14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YNTSXX9C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x8cwlnogcrzd694owty4.jpg)

在绘画方面，我肯定还有很多东西要学。

# 最终建成

油漆干了以后，我对 MagicMirror 仪表板做了一些小的配置更改，主要是为了位置准确的数据。油漆干了之后，就该组装最后的建筑了。

[![Mirrorvengers Assemble](img/03abe89cc295dafd4a7e55b637c5b0aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zficFa0L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uzwgtfbenr6ex6u5oh4q.jpg)

*   你看到的是镜子的底部
*   顶部没有背框和通风开口
*   桥墩是用螺丝固定在一块木头上的，木头是用水泥粘在后底座上的
*   监视器用螺丝固定在顶部，由一根横杆支撑着
*   我连夜对丙烯酸树脂进行了热测试——它保持得很好

# 结论

我真的很满意这个造型。现在，我可以从我的主机 SSH 到 Pi，对仪表板进行添加或修改，甚至添加一个模块。总价是 185 美元。我从镜子的物理构造中学到了很多。关注细节至关重要；你不想像我一样用砂纸打磨。我要感谢渥太华 Refacing 为 CNC 付出的时间和教育，并提供了 MDF。

[![Final Build](img/75fc9a16073ccc66a8b2ebb8f21b0ea9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---hMzIvWE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1p3u16x35b897jvgz9gz.jpg)

我希望你喜欢这本书！

干杯。