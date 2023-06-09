# 使用框架简化电子邮件设计

> 原文：<https://dev.to/whoisryosuke/using-a-framework-to-simplify-email-design-36m9>

我们从塞满杂乱表格组件的标准 HTML 模板切换到电子邮件基金会的简洁的人性化代码。

## 问题

电子邮件设计很难。这是一堆嵌套的表格和内嵌的样式，制造出震耳欲聋的低效代码。

> 如果你曾经从头开始设计过一封电子邮件，或者甚至修改过一个模板，你就会体验到解析意大利面条的痛苦。

我们使用的模板有足够的组件使其灵活，但每次都需要我们移除每个组件和内联的 CSS。退一步说，这很麻烦，我们努力设计足够短的电子邮件，以免被 GMail 等标准电子邮件夹到。

## 电子邮件的解决方案基础

ZURB 的 [**电子邮件基础**](http://foundation.zurb.com/emails/getting-started.html) 是一个电子邮件设计框架，它简化了电子邮件的开发、测试和构建。它预装了一个 SASS 编译器，用于快速开发的浏览器同步，通过 Gulp 压缩的生产版本，甚至通过 AWS 和 Litmus 发送测试电子邮件。

## 墨色

最吸引人的特性之一是 **Inky** ，这是一种模板语言，它简化了创建表格布局的过程。只需几行代码，您就可以创建一个符合复杂电子邮件兼容性要求的表格布局。

**墨色:**

```
<container>
  <row>
    <columns>This is a column.</columns>
  </row>
</container> 
```

Enter fullscreen mode Exit fullscreen mode

以及**最终编译的 HTML:**

```
<table class="container">
  <tbody>
    <tr>
      <td>
        <table class="row">
          <tbody>
            <tr>
              <th class="small-12 large-12 columns first last">
                <table>
                  <tr>
                    <th>This is a column.</th>
                    <th class="expander"></th>
                  </tr>
                </table>
              </th>
            </tr>
          </tbody>
        </table>
      </td>
    </tr>
  </tbody>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

这使得我们可以从邮件中删除大量代码，使得模板更加**优雅**和 ***易读*** 。使用`<spacer>`标签，像用嵌套表格和空白 gif 分隔元素这样的事情变得毫不费力。

```
<container>

  <row class="header leaf">
    <columns small="12" style="background:url(./assets/img/bg-leaf.jpg)">
        <spacer size="25"></spacer>
        <center>
            <a href="http://stayregular.net/?utm_source=newsletter&utm_campaign=redesign2018">
                <img src="./assets/img/logo.png" alt="Stay Regular logo" />
            </a>
        </center>
        <spacer size="100"></spacer>
        <img src="./assets/img/text-header-1.png" alt="We redesigned our portfolio site" />
        <spacer size="45"></spacer>
        <a href="http://stayregular.net/?utm_source=newsletter&utm_campaign=redesign2018">
            <img src="./assets/img/btn-check-us-out.jpg" alt="Check us out" />
        </a>
        <spacer size="145"></spacer>
    </columns>
  </row>

</container> 
```

Enter fullscreen mode Exit fullscreen mode

## 破绽百出

尽管这个框架在很多方面使得设计电子邮件变得非常简单，但是它仍然有一些缺陷。

我在把邮件发送到 T2 的 GMail 账户时，遇到了随机转换错误。*电子邮件的基础*预建了一个网格系统，它有媒体查询，使电子邮件在较小的宽度下做出响应(所以它不是硬设置为某个像素宽度)。邮件代码在 MailChimp 中看起来很好，但 GMail 对它进行了不同的编译，内嵌了最小的媒体查询 CSS。它完全打破了布局，使电子邮件变得流畅，而不是固定的宽度。

我在用 AWS 以外的东西测试电子邮件时也遇到了问题。我想使用 [**邮件陷阱**](http://mailtrap.io) ，因为它是我电子邮件的首选服务器。尽管我找不到任何关于如何使用 Foundation 的文档。

说到底，你仍然在设计电子邮件，所以你受限于与普通电子邮件兼容的内容。没有花哨的 CSS 或 JS 效果，即使有 SASS 支持。*但是不要恨玩家，要恨游戏。*

## 尽在一解？

这个框架是我发现的开发电子邮件的最简单和最好的解决方案。没有人能接近这个完整的包装。尽管有一些缺陷，自动化的同步和构建过程是这个框架的重要补充。

> 我所有的修改，从 SASS 到压缩图像，都在*秒内*编译成产品版本，我可以为 MailChimp 进行实时编辑或压缩。

它甚至通过将所有电子邮件和风格保存在一个存储库中，使协作变得更加容易。你可以从我们的 Gitlab 中克隆它，然后**你就可以使用我们的风格组件设计电子邮件了**。

## 快速入门指南

如果我已经激起了你对我们的过渡有多成功的兴趣，那么开始使用电子邮件的基础是非常容易的。只需将以下命令粘贴到项目父文件夹中的终端:

```
git clone https://github.com/zurb/foundation-emails-template your_project_folder
cd your_project_folder
npm install 
```

Enter fullscreen mode Exit fullscreen mode

再次感谢 [ZURB](http://zurb.com) 和[所有的贡献者](https://github.com/zurb/foundation-emails/graphs/contributors)将如此棒的无缝和简化的电子邮件开发解决方案*(这是开源的！).*

奥斯卡保持常规

* * *

**继续阅读:**

*   [Github 上电子邮件的基础](https://github.com/zurb/foundation-emails)
*   [电子邮件文档的基础](https://foundation.zurb.com/emails/docs/)