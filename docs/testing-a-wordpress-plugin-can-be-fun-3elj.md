# 测试一个 WordPress 插件会很有趣！

> 原文：<https://dev.to/omerlh/testing-a-wordpress-plugin-can-be-fun-3elj>

[![Header](img/0b8cc7ce65eff9ac6e6a837317007d49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ArJgrwTi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mi996to0lvo21s9osao6.png) 
你有使用 [WordPress](https://wordpress.com/) 的经验吗？直到最近，我从未尝试在 WordPress 生态系统中开发什么东西。几周前，我发现了一个很酷的插件，我想改进它。在开发过程中，我意识到为 WordPress 编写测试是多么复杂——我想和你分享我的经验。复杂性的一部分是在 Docker 容器中运行这些测试——以便在真实的 WordPress 环境中运行它们。因为它有点复杂，所以我创建了一个您可以使用的模板——这样您可以将更多的精力放在编写测试上，而不是如何运行它们。模板(实际上也是一个示例项目)可以在 [GitHub](https://github.com/Soluto/wordpress-plugin-tests-template) 上获得。

## 使用模板

要使用这个模板来测试你的插件，你首先需要修改一些东西，这样它将测试你的实际插件而不是演示插件:

*   克隆[回购](https://github.com/Soluto/wordpress-plugin-tests-template)。
*   将插件的所有源代码复制到`src`文件夹中。
*   修改 [`test/bootstrap.php`](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/tests/bootstrap.php#L22) :改为需要你的插件而不是我的插件(在第 21 行寻找`TODO`)。
*   开始为你的插件编写测试吧！您可以修改 [`tests/tests-plugin-security-scanner.php`](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/tests/test-plugin-sample.php) 下的基本测试，或者删除该文件并从头开始。整个 WordPress 测试 SDK 对你来说是可用的。

要运行测试，您首先需要使用:
构建容器

```
docker-compose up --build 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用:
来运行测试

```
docker-compose run wordpress vendor/bin/phpunit 
```

Enter fullscreen mode Exit fullscreen mode

然后，坐下来看着测试进行。

很酷，对吧？

现在开始测试你的插件吧，这真的很简单，而且提高了你代码的质量！

那么幕后到底是怎么回事呢？

继续阅读，深入了解模板是如何工作的。

## 插件安全扫描器

在我们深入细节之前，让我们从一个小故事开始——我是如何结束开发一个 WordPress 插件的。正如你可能知道的，WordPress 是一个伟大的平台，几乎可以构建任何东西——博客、购物网站等等。WordPress 如此强大的原因之一是它的生态系统——WordPress 有超过 50，000 个插件和数以千计的主题，你可以用来定制你的网站。你的大部分 WordPress 需求已经被打包成一个插件或者一个主题，这太棒了！但并非一切都是完美的。你有没有想过你正在使用的插件有多安全？毕竟，插件代码是在你的站点上运行的，所以插件中的漏洞(或者，更糟糕的是——一个故意的恶意插件)可能会损害你的站点。对我们来说幸运的是，我们有 [WP 漏洞数据库](https://wpvulndb.com/),它将 WordPress 中的漏洞分类，以及它的插件和主题。这很棒——但是每次安装新的插件或主题时手动这么做是不可行的。此外，当在你使用的插件中发现新的漏洞时，它不会提醒你。

对我们来说幸运的是，同样有一个[插件](https://wordpress.org/plugins/plugin-security-scanner)可以解决这个问题——它会用你所有的插件、主题和 WordPress 版本每天查询一次 WP 漏洞数据库。如果发现漏洞，它会通过邮件向您报告。这很好，但理想情况下，我希望它能与监控系统集成(我们使用 icinga，但任何监控系统都应该工作)。实现与电子邮件的集成是可能的，但 webhook 支持会使它变得容易得多。

我是开源爱好者的原因之一是，如果有缺失的功能，您可以为项目做出贡献并添加它。因为这是一个开源插件，所以我就这么做了。当我键入代码时，我问自己——测试它怎么样？这是一个非常重要的插件——我想确信它做了它应该做的事情，并且它将在未来继续这样做。

## 当前的 WordPress 测试工具

如果你有 WordPress 开发经验，你可能问过自己同样的问题——如何为 WordPress 插件(或主题，这通常是一样的)编写测试？。

好的一面是 WordPress 也可以处理这些——WordPress CLI[可以生成](https://make.wordpress.org/cli/handbook/plugin-unit-tests/)你开始测试所需要的一切。尽管它被称为单元测试，集成测试似乎更适合他们的名字——因为他们是在真正的 WordPress 上运行的。对于单元测试场景，最好使用 [WordPress mock](https://github.com/10up/wp_mock) 。

因为那些是集成测试，所以它们需要一个安装了 PHP 和 MySQL 的环境——这是有意义的——但并不理想:(1)我不想在我的机器上(以及任何其他开发人员的笔记本电脑上)安装 PHP 和 MySQL，只是为了运行那些测试。(2)我希望本地环境尽可能接近 CI 服务器中的环境。更重要的是，它还需要靠近生产服务器上的环境。(3)我想避免版本地狱——我不想担心安装正确的版本等等。

我通过在 [Docker](https://www.docker.com/) 中运行测试解决了这个问题，尽管其他解决方案(如[流浪者](https://www.vagrantup.com/))也可以工作。做到这一点相当复杂——这就是为什么我创建了一个任何人都可以在他们的测试中使用的[模板](https://github.com/Soluto/wordpress-plugin-tests-template)(就像我在开始所说的那样)。

正如我之前提到的，您可以使用 WordPress CLI 来生成运行测试所需的所有内容。该模板主要基于 WordPress 生成的内容，做了一些修改，这样我就可以在 Docker 中运行它了。

让我们深入了解一下，这样你就能明白发生了什么。

## 模板文件结构

在看代码之前，我们先讨论一下 Docker 解决方案。该模板基于两个容器:一个将运行 MySQL DB(使用[官方 docker 镜像](https://hub.docker.com/_/mariadb/))，另一个将运行我们的测试(我们将很快研究 docker 文件)。这可以通过使用 Docker 的另一个工具 [Docker Compose](https://docs.docker.com/compose/) 轻松完成，它允许你轻松定义和管理多个容器。这些容器是使用 [`docker-compose.yml`](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/docker-compose.yml) 文件定义的，我不会在这里讨论——但是非常欢迎您来看看！

最有趣的文件夹是 tests 文件夹。在该文件夹下，您会发现两个文件:

*   [`bootstrap.php`](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/tests/bootstrap.php) :顾名思义，这个文件负责引导——加载 WordPress SDK，这样它就可以用于你的代码和测试代码，加载你的插件代码，这样你就可以测试它。
*   [`tests-plugin-sample.php`](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/tests/test-plugin-sample.php) :这个文件包含了你的插件的实际测试——可以随意重命名。

为了测试 WordPress 函数，你的测试类必须继承自`WP_UnitTestCase`类。除此之外，这是一个常规的 PHPUnit 测试类——所以 PHPUnit 的所有常规规则在这里都适用。你可以调用你插件的任何功能，当然，你可以使用完整的 WordPress SDK。此外，在 WP _ UnitTestCasethere 中有很多实用方法可以帮助你测试你的插件——例如，生成用户或帖子。WordPress 有一个非常棒的测试 SDK——但是文档不是很好。有一个[非官方文件](https://miya0001.github.io/wp-unit-docs/)，但你可以随时阅读[代码](https://develop.svn.wordpress.org/tags/4.8.2/tests/phpunit/includes/factory/)。

现在我们来看看 bin 文件夹。它包含 2 个脚本，安装我们的测试所需的一切。WordPress CLI 生成一个文件—`install-wp-tests.sh`。我选择把它分成两个文件，这样它就能更好地与 Docker 兼容——当我们深入 Docker 文件时，你就会明白为什么了。

最后一个文件夹是`src`文件夹，它包含了你的插件的代码。我选择将插件代码移动到一个文件夹中，以使事情变得更加整洁。根文件夹现在包含许多文件，有点难以理解发生了什么。

## docker file

让我们看一下 [Dockerfile](https://github.com/Soluto/wordpress-plugin-tests-template/blob/master/Dockerfile) ，以便更好地理解正在发生的事情:

```
ARG PHP_IMAGE_TAG
FROM php:$PHP_IMAGE_TAG
ARG WORDPRESS_DB_PASSWORD
ARG WORDPRESS_VERSION 
```

Enter fullscreen mode Exit fullscreen mode

Dockerfile 文件的前几行定义了一些构建参数，可以用来定制测试环境 PHP 和 WordPress 版本。这允许您确保您的代码与所有相关版本兼容。最后一个参数是 DB 密码——由合成文件控制:它必须与传递给 DB 容器的密码相同。

PHP 映像必须基于 Alpine Linux——因为从 PHP 5 开始，您需要安装一个扩展来与 MySQL 交互。出于某种原因，这在 Ubuntu Linux 上并没有很好地工作，迁移到 Alpine Linux 解决了所有奇怪的 MySQL 问题。通过查看[这里的](https://github.com/docker-library/php/issues/279)，看起来安装扩展的过程在 Alpine Linux 上运行得更好。这也是我没有使用 PHPUnit 映像的原因——它没有附带`docker-php-ext-enable`实用程序，可以让您轻松地启用 PHP 扩展。

```
RUN echo "http://dl-3.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories &&\
apk add --update --no-cache subversion mysql mysql-client git bash g++ make autoconf && \
set -ex;&& \
docker-php-ext-install mysqli pdo pdo_mysql pcntl \
&& php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php composer-setup.php --install-dir=/usr/bin --filename=composer
&& docker-php-source extract \
&& pecl install xdebug \
&& docker-php-ext-enable xdebug \
&& docker-php-source delete \
&& echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
&& rm -rf /tmp/* 
```

Enter fullscreen mode Exit fullscreen mode

这个块安装了运行设置脚本和我们的测试所需的所有东西。该脚本使用 SVN 下载 WordPress，使用`mysqladmin`管理 MySQL 数据库。你会注意到我正在安装一些扩展，主要是 MySQL 扩展。安装 XDebug 是为了支持代码覆盖和远程调试(点击这里了解如何通过 docker 调试。我个人没有尝试过)。最后，安装 Composer 来管理我们测试代码的依赖性，包括 PHPUnit

```
WORKDIR /tmp
COPY ./bin/install-wp-tests.sh /tmp/install-wp-tests.sh .
RUN /tmp/install-wp-tests.sh wordpress_test root $WORDPRESS_DB_PASSWORD mysql $WORDPRESS_VERSION
COPY ./db-error.php /tmp/wordpress/wp-content/db-error.php . 
```

Enter fullscreen mode Exit fullscreen mode

这些行负责安装 WordPress 和测试 SDK。安装过程需要一些时间，尤其是因为它下载了很多东西。这就是为什么我尽可能快地运行它，这样它将被缓存，而不是每次在我的机器上触发构建时都要运行。使用`db-error`是为了获得有意义的 DB 误差，但这不是必需的。

```
WORKDIR /wordpress
COPY composer.json /wordpress
RUN composer install
COPY . /wordpress 
```

Enter fullscreen mode Exit fullscreen mode

在所有这些之后，我们需要做的就是复制`composer.json`并使用 composer 安装依赖项——同样，当只改变测试文件时，保持它被缓存。你会注意到我使用的是稍微老一点的 PHPUnit 版本(5 . 5 . 0)——自 PHPUnit 6 以来有一个突破性的变化，打破了 WordPress 测试 SDK(更多细节见[这里](https://stackoverflow.com/a/44051138/4792970))，所以我不得不使用一个老版本。最后，将所有其他文件复制到容器中。

```
CMD bin/install-db.sh wordpress_test root $WORDPRESS_DB_PASSWORD mysql 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到 CMD 是 DB init 脚本:这就是为什么我将原始脚本分成两个不同的文件。所有的 WordPress 初始化可以发生在构建阶段的早期，所以它将被缓存，而不必在每次测试代码改变时运行。数据库初始化脚本需要一个实际的数据库来处理，所以它必须在数据库准备好之后才运行。

## 结论

我希望现在您对模板的工作原理有了更好的理解。我已经很期待听到你对它的体验了:你能使用它吗？什么对你有效，什么对你无效？在评论中分享你的想法，在回购上开一个问题——或者发微博给我( [@omerlh](https://twitter.com/intent/tweet?text=.@omerlh) )。

最初发布于 [soluto-engineering 博客](https://blog.solutotlv.com/testing-wordpress-plugins-easy/?utm_source=devto)