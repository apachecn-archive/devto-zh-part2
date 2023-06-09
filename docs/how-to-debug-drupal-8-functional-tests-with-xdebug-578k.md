# 如何用 XDebug 调试 Drupal 8 功能测试

> 原文：<https://dev.to/fgm/how-to-debug-drupal-8-functional-tests-with-xdebug-578k>

如何用 XDebug 调试 Drupal 8 功能测试

### 问题

在将 [MongoDB 项目](https://drupal.org/project/mongodb)中的旧式功能测试从`WebTestBase`转换到`BrowserTestBase`时，我偶然发现了一个问题:在按照 drupal.org 的指令从 PhpStorm 中[运行 PHPunit 测试后，在 IDE 中进行逐步调试工作得很好，但在第一个`$this->drupalLogin();`调用时停止了，经过很长时间的延迟后返回 404，尽管相同的调用在没有调试的情况下运行正常。会发生什么事？](https://www.drupal.org/docs/8/phpunit/running-phpunit-tests-within-phpstorm)

### 诊断

进入`UiHelperTrait::drupalLogin()`，我注意到在提交帐户标识之前，在方法的早期`drupalGet('user/login')`调用中出现了延迟。会不会是这个问题？

事实上，在调用返回后，通过`$this->getSession()->getStatusCode();`评估调用结果会显示一个 504 超时 HTTP 状态代码。事实上，用`$this->getSession()->getPage()->getContent()`转储页面显示它包含一个典型的超时页面。

检查日志显示超时发生在 PHP 级别，在 Xdebug 对调试客户端执行相反方向的调用期间，尽管这样的调用显然是有效的，因为在那之前我已经单步执行代码几个小时了。

### 解

总是乐于助人且知识渊博的人 [@berdir](https://www.drupal.org/u/berdir) 提出了解决方案:PhpStorm 恰好对它将接受的用于调试的同时连接的数量设置了一个限制，该限制默认为 1。

突然间，它变得有意义了:在传统的调试过程中，来自主机站点的 PHP 解释器建立了一个到客户机(PhpStorm IDE)的连接，但是当在这样的调用过程中调用子站点时，解释器的第二个实例被启动并试图连接...但是被默认配置的单个连接限制所阻止。

[![Screenshot from the XDebug preferences in PhpStorm 2018.1.2](img/5d9596e347ba83ee3acc5a0d7253eb4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zGBtotM3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.riff.org/sites/default/files/inline-images/phpstorm-xdebug-connections_0.png) 所以所要做的就是增加连接的数量！

在 PhpStorm 2018 和 2019 macOS 中，设置可以在右窗格`External connections`部分的*PHP storm/Preferences/Languages&Frameworks/PHP/Debug*中找到，如下图。

不需要重启 IDE 或者 PHP，只需要重启调试运行配置，就很漂亮了。