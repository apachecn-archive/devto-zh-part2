# 检查你的代码库是否准备好了 PHP 7 并与 PHP 兼容

> 原文：<https://dev.to/matthewbdaly/check-your-code-base-is-php-7-ready-with-php-compatibility-47m8>

我最近开始了一项新的工作，作为其中的一部分，我正在处理一个相当大的遗留代码库。事实上，它是如此遗留，以至于它仍然在 Subversion 中——不用说，我做的第一件事就是将它迁移到 Git。对于这个代码库，我们要做的工作之一是将它从 PHP 5.4 迁移到 5.6，然后再迁移到 PHP 7。到目前为止，我在 5.6 中一直在本地使用它，没有任何问题，但是我也一直在寻找一个自动化的工具来帮助捕捉潜在的问题。

我最近发现了 [PHP 兼容性](https://github.com/wimg/PHPCompatibility)，它是 PHP 代码嗅探器的一组嗅探，可用于检测特定 PHP 版本中可能有问题的代码。由于我已经广泛使用 CodeSniffer，它非常适合我现有的工具集。

要安装它，请将以下依赖项添加到您的`composer.json` :

```
 "require-dev":  {  "dealerdirect/phpcodesniffer-composer-installer":  "^0.4.3",  "squizlabs/php_codesniffer":  "^2.5",  "wimg/php-compatibility":  "^8.1"  }, 
```

Enter fullscreen mode Exit fullscreen mode

然后更新你的`phpcs.xml`，看起来像这样:

```
<ruleset name="PHP_CodeSniffer">
   <description>The coding standard for my app.</description>
   <file>./</file>
   <arg value="np"/>
   <rule ref="PSR2"/>
   <rule ref="PHPCompatibility"/>
   <config name="testVersion" value="7.2-"/>
</ruleset> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它可以与现有的编码标准(如 PSR2)一起使用。注意`testVersion` config 键——指定的值是我们测试的 PHP 版本。这里我们指定 PHP 7.2。

显然，防止 PHP 新版本崩溃的最好方法是拥有一个全面的测试套件，但是遗留代码库从定义上来说往往很少或者没有测试。通过使用 PHP 兼容性，您至少应该能够发现语法问题，而不必手动审计代码库。