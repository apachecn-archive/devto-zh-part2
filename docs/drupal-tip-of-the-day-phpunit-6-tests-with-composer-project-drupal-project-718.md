# Drupal 日积月累:用 composer-project/Drupal-project 测试 PHPUnit 6

> 原文：<https://dev.to/fgm/drupal-tip-of-the-day-phpunit-6-tests-with-composer-project-drupal-project-718>

### 问题

当试图在即将发布的 8.2.0 版本的 MongoDB contrib 模块上运行 PHPUnit 测试时，我最近开始遇到这个错误:

```
PHPUnit testing framework version 6 or greater is required when running on PHP 7.0 or greater.
Run the command 'composer run-script drupal-phpunit-upgrade' in order to fix this. 
```

Enter fullscreen mode Exit fullscreen mode

好的，没什么大不了的，让我们运行这个命令。

```
# From $PROJECT/web/core directory
$ ../../vendor/bin/composer run-script drupal-phpunit-upgrade
In RunScriptCommand.php line 89:

Script "drupal-phpunit-upgrade" is not defined in this package run-script [--timeout TIMEOUT] [--dev] [--no-dev] [-l|--list] [--] [] []... 
```

Enter fullscreen mode Exit fullscreen mode

嗯，所以我需要那个命令，但是它没有在`composer.json`中定义。那么它在哪里？

```
$ cd $PROJECT
$ grep -ir drupal-phpunit-upgrade .
./web/core/tests/bootstrap.php: $message = "PHPUnit testing framework version 6 or greater is required when running on PHP 7.0 or greater. Run the command 'composer run-script drupal-phpunit-upgrade' in order to fix this."; 
./web/core/tests/README.md:- If you're running PHP 7.0 or greater you will need to upgrade PHPUnit with `composer run-script drupal-phpunit-upgrade`./web/core/scripts/run-tests.sh: simpletest_script_print_error("PHPUnit testing framework version 6 or greater is required when running on PHP 7.0 or greater. Run the command 'composer run-script drupal-phpunit-upgrade' in order to fix this."); 
./web/core/scripts/run-tests.sh: passthru("$composer run-script drupal-phpunit-upgrade-check"); 
./web/core/lib/Drupal/Core/Composer/Composer.php: * Fires the drupal-phpunit-upgrade script event if necessary.
./web/core/lib/Drupal/Core/Composer/Composer.php: // call the drupal-phpunit-upgrade script to upgrade PHPUnit.
./web/core/lib/Drupal/Core/Composer/Composer.php: ->dispatchScript('drupal-phpunit-upgrade'); 
```

Enter fullscreen mode Exit fullscreen mode

因此该命令在多个地方被引用，但没有被定义。这是怎么回事？

### 诊断

大多数时候，当构建 Drupal 8 项目时，我们希望使用 [drupal-project](https://github.com/drupal-composer/drupal-project) 包，以获得更合理的项目布局。但是在为 core 做贡献时，通常的做法是长期使用`drupal/drupal`或者甚至只是 Drupal 本身的 Git checkout。不过，当[问题#2982674](https://www.drupal.org/project/drupal/issues/2982674) 解决后，这方面的情况可能会有所改变。

与此同时，PHP 版本也在不断更新，大多数情况下最新版本是 PHP 7.1.x，今年秋季的目标版本是 PHP 7.2.x。而忠实为我们服务多年的 PHPunit 4.x 与 PHP 7.2 完全不兼容，所以从 2018 年 4 月[期#2927806](https://www.drupal.org/project/drupal/issues/2927806) 开始就要求迁移到 PHPunit 6.x。现在也用于 7.0.x/7.1.x，详见 https://www.drupal.org/node/2957906 的[。](https://www.drupal.org/node/2957906)

但是另一方面，`composer-project/drupal-project`提供了一个不包含`drupal-phpunit-upgrade`的`composer.json`脚本，可能是因为它现在仍然生成 Drupal 8.5.x 版本，这导致这个命令失败。

### 预期的非工作修复

直到[发布#2982674](https://www.drupal.org/project/drupal/issues/2982674) 成功，或者`drupal-scaffold`被更新，这里我们只需要出现在[当前 Drupal core 8.7.x](http://cgit.drupalcode.org/drupal/tree/composer.json?h=8.7.x#n56) :
的`composer.json`中的命令

```
 "drupal-phpunit-upgrade": 
        "@composer update phpunit/phpunit --with-dependencies --no-progress", 
```

Enter fullscreen mode Exit fullscreen mode

我们就把它加到`composer.json`里查一下 PHPunit:还是 4.8.x 版本！问题是`composer-project`在 4.8.36 中增加了一个 phpunit 依赖项，安装并锁定了早期版本，Composer 找不到更新路径。

### 实际搞定

实际上，项目不再需要这一行，因为`webflo/drupal-core-require-dev`依赖，它本身需要`phpunit/phpunit (^4.8.35 || ^6.5)`，这将允许 PHPunit 6.x，不像我们的基础`composer.json`所需要的。所以让我们去掉对 PHPunit 的直接依赖，目前 PHPunit 将我们锁定在 4.8 上。

```
$ composer remove --dev phpunit/phpunit
# ...lots of lines showing the upgrade...
$ vendor/bin/phpunit --version
PHPUnit 6.5.9-3-g2d53bd5ae by Sebastian Bergmann and contributors.
$ composer why phpunit/phpunit
webflo/drupal-core-require-dev  8.6.x-dev  requires  phpunit/phpunit (^4.8.35 || ^6.5) 
```

Enter fullscreen mode Exit fullscreen mode

正如这些结果所示，我们现在有了 PHPunit 6.x，这要感谢 [webflo](https://www.drupal.org/u/webflo) 的包。问题解决了。