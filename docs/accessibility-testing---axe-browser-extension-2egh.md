# 可访问性测试- aXe 浏览器扩展

> 原文：<https://dev.to/davefollett/accessibility-testing---axe-browser-extension-2egh>

受到[网站无障碍(a11y)](https://en.wikipedia.org/wiki/Computer_accessibility) 上的 [syntax.fm 播客插曲](https://syntax.fm/show/072/accessibility)的启发，我决定尝试一下由[德克系统](https://www.deque.com)创建的 [aXe](https://www.deque.com/axe/) 浏览器扩展。虽然 Wes 和 Scott 提到了几个有趣的工具，但这个工具吸引了我，因为它听起来既健壮又易于使用。

为了测试 aXe，我决定使用开源项目 [QIT](https://qit.cloud/) 。我最近开始参与这个项目，所以这似乎是一个自然的选择。QIT 是一个进步的网络应用程序(pwa ),允许你按主题搜索软件开发播客。这是[乔·扎克](http://joezack.com/)的创意，他是[编码块播客](https://www.codingblocks.net/)的主持人之一，由他和[编码块松弛频道](https://www.codingblocks.net/slack/)的[成员](https://github.com/codingblocks/podcast-app/graphs/contributors)赋予生命。如果你还没有看过 Coding Blocks podcast 或 slack channel，我强烈建议你去看看。

## 设置

Axe 有几种不同的使用方式，但是在这篇文章中，我将使用 aXe for Chrome 浏览器扩展。设置非常简单。只需从 Chrome 网上商店安装谷歌 Chrome 的 axe 扩展。

## 测试执行

[![Running the aXe tests](img/55c64480b282dd74b1d592069dd2e75b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M-8dgole--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://davefollett.io/2018/09/13/accessibility-testing-axe-browser-extension/run-axe-test.gif)

测试网页也非常简单:

1.  导航到要测试的页面
2.  打开 Chrome 的 DevTools (Control + Shift + I)
3.  选择 aXe 选项卡
4.  单击分析按钮

让我们继续对 QIT 主页进行测试，看看结果。

## 检查结果

[![The aXe tests results](img/c299cb0ca3bf4d8b1bcc2cdad77a4af2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--icfHsQ2i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2018/09/13/accessibility-testing-axe-browser-extension/qit-axe-results.jpg)

Axe 不仅在识别问题方面做得非常好，而且提供了修复问题可能需要的一切。

*   问题的描述
*   问题的影响评级
*   切换以突出显示或检查页面中的问题
*   解决问题的方法列表
*   了解该问题更多信息的链接

## 现在怎么样了？

哇，太棒了。我在几分钟内发现了四个 a11y 问题，但如果我不修复它们，又有什么意义呢！在开始之前，我应该成为一名优秀的开发人员，在 GitHub 项目中为他们制造问题。自从我最近读了金成([@ Twitter 上的 SlightEdgeCoder](https://twitter.com/SlightEdgeCoder))的文章:

[![dance2die image](img/468efb35d9e51b70de31c7071d198e22.png)](/dance2die) [## 提供更好的 GitHub 问题体验

### sung m . Kim Jun 24 ' 18 3min read

#github #opensource](/dance2die/providing-a-better-github-issue-experience-fgh)

为什么不为可访问性问题定制一个问题模板呢？

[![Thanks for the emoji recommendations Sung Kim!](img/05ac30d8a17eb89de6982138c753f3bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9GbSk5dm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2018/09/13/accessibility-testing-axe-browser-extension/qit-a11y-issue.jpg)

既然问题已经记录下来，是时候解决它了。

## 修罗

[![A11y fix by adding aria-label attribute](img/ae058edc8a4edd97b3f35c828a9ccee5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AiZOz4lX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2018/09/13/accessibility-testing-axe-browser-extension/qit-a11y-fix.jpg)

这个特殊的可访问性问题可以通过向`<input>`元素添加一个 [aria-label 属性](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute)来解决。除了修复这个问题，它也不会改变`<input>`元素的原始外观。尽管更好的解决方案可能是重新设计页面，使用与`<input>`相关联的`<label>`元素，而不是使用`aria-label`属性。我将把它留到下一天，并取得一个小小的胜利，使网站的可访问性比以前稍微好一点。

## 资源

*   [Syntax.fm 播客第 72 集](https://syntax.fm/show/072/accessibility)
*   [Deque aXe 系统网页](https://www.deque.com/axe/)
*   [aXe for Google Chrome 扩展](http://bitly.com/aXe-Chrome)
*   [QIT 播客搜索网站](https://qit.cloud/)
*   [QIT GitHub 项目网页](https://github.com/codingblocks/podcast-app)