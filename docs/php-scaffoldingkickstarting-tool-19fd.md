# PHP 项目搭建(kickstarting)工具

> 原文：<https://dev.to/adhocore/php-scaffoldingkickstarting-tool-19fd>

介绍我雄心勃勃的项目。已经可以做很多事情，但更多的贡献将使它变得完美。新的特性正在开发中，以取悦任何多产的开发者。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[ad hoc ore](https://github.com/adhocore)/[phint](https://github.com/adhocore/phint)

### 交互式地搭建和初始化新的(或修复旧的)PHP 项目/库，并使用模板立即使用相同的默认设置

<article class="markdown-body entry-content container-lg" itemprop="text">

## adhocore/phint

使用模板初始化新的 PHP 项目，默认使用模板来搭建 PHP 库和/或项目，以提高您的生产力和节省时间。

对于已经存在的项目，用`--sync`标志运行以添加缺少的东西，见 [phint init](https://github.com/adhocore/phint#init) 。

一旦你在`src/`或`lib/`中有了文件，你就可以运行 [phint docs](https://github.com/adhocore/phint#docs) 来生成类似于 API 的`.md`格式的文档，运行 [phint test](https://github.com/adhocore/phint#test) 来生成基本的测试存根，所有的结构都已经维护好了。

它帮助你变得更加懒惰！phint 在不断发展，计划是让它[变大](https://github.com/adhocore/phint#todo)。

[![Latest Version](img/ad3e7844738ffb429c9c2f99c9161e75.png)](https://github.com/adhocore/phint/releases)[![Travis Build](img/c8fc2eddb7e87769df38d6511ebd1793.png)](https://travis-ci.org/adhocore/phint?branch=master)[![Scrutinizer CI](img/e5fde28bc58b854ba7bd4c2cde7a57ee.png)](https://scrutinizer-ci.com/g/adhocore/phint/?branch=master)[![Codecov branch](img/7c37954a3ac898fd40d310cd770f5ef2.png)](https://codecov.io/gh/adhocore/phint)[![StyleCI](img/8d686e138e03bd7d7d798ae1e5f7ba45.png)](https://styleci.io/repos/108550679)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/adhocore/phintLICENSE)[![Donate 15](img/b2c2f94625d612f1d6f170d10bb8de6d.png)](https://www.paypal.me/ji10/15usd)[![Donate 25](img/55169c512eb5e340a3267ace0ef15ff2.png)](https://www.paypal.me/ji10/25usd)[![Donate 50](img/f0d5a67bfcb273a3a2b8548db2a580d6.png)](https://www.paypal.me/ji10/50usd)[![Tweet](img/3aedcf72e57e3736cd6ea05d11dfd7db.png)](https://twitter.com/intent/tweet?text=Scaffold+new+PHP+project+with+sane+defaults+using+templates&url=https://github.com/adhocore/phint&hashtags=php,template,scaffold,initproject)

[![Phint Preview](img/b07016daacbe8053cc999a19f9d08b2e.png "Phint Preview")T2】](https://camo.githubusercontent.com/f5aec54ef4a752fc3a6aecd8e29e000f856b3ebc87bfeaa301c764d5dcd63cf4/68747470733a2f2f696d6775722e636f6d2f4636506b58395a2e706e67)

[安装](https://github.com/adhocore/phint#installation) [功能](https://github.com/adhocore/phint#features) [自动完成](https://github.com/adhocore/phint#autocompletion) [用法](https://github.com/adhocore/phint#usage) [phint 初始化](https://github.com/adhocore/phint#init) [phint 更新](https://github.com/adhocore/phint#update) [phint 文档](https://github.com/adhocore/phint#docs) [phint 测试](https://github.com/adhocore/phint#test) [模板化](https://github.com/adhocore/phint#templating)

> Phint 由[ad hoc core/CLI](https://github.com/adhocore/php-cli)供电

## 装置

> 需要 PHP7。

### 指南

从[最新版本](https://github.com/adhocore/phint/releases/latest)下载`phint.phar`并如此使用`php /path/to/phint.phar [opts] [args]`。嗯不酷。参见下面的命令部分。

### 命令

```
#
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/adhocore/phint)

Phint 是一个用于控制台的生产力工具，它使用 twig 模板，以交互方式搭建和初始化新的 PHP 项目，并使用 sane 缺省值。灵活，可扩展，可定制和许多选项可供选择。

为你节省大量的时间和精力做平凡重复的任务。

特点:

*   生成类似`.gitignore, .travis.yml, . editorconfig`等的点文件
*   使用所有 dep 生成许可证、readme.md、composer.json
*   生成变更日志存根、投稿指南、问题和 PR 模板
*   生成二进制文件(如果有)
*   去吧，林特克
*   交互式询问并安装所有开发和生产部门
*   生成`phpunit.xml`，测试`bootstrap.php`
*   为对应于`src` ( `phint test`)的所有类/方法生成测试存根
*   生成文档(。md)来自文档块:)(`phint docs`)
*   更新自己的自我(`phint update`)
*   支持来自用户选择的路径的自定义模板:)

感谢阅读。请试用并留下反馈。😊