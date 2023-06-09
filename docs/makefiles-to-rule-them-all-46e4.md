# Makefiles 来统治它们

> 原文：<https://dev.to/maxlmator/makefiles-to-rule-them-all-46e4>

在我的上一篇博客中，你可能已经发现我使用 [Makefile](https://www.gnu.org/software/make/manual/make.html) 来简化项目任务。我对 Makefiles 的热情更进一步，现在我已经在我的大多数项目中添加了一个。以下是我为什么这样做，以及它们如何让我的日常开发工作变得更简单。

我的项目都不是用 C 或 C++编写的，它们依赖 Makefile 来编译它们。相反，我的项目是用 JavaScript (Node)、Ruby 和 PHP 编写的。我使用 make 命令作为每个生态系统附带的各个工具的包装器，来创建一个公共接口。这样我就可以运行`make test`了，不管测试是在 JavaScript 中，还是使用[摩卡](https://mochajs.org/)或者使用 [PHPUnit](https://phpunit.de/) 。

除了属于一个平台的不同工具集(例如 Node 的 [npm](https://www.npmjs.com/) ，PHP 的 [Composer](https://getcomposer.org/) 和 Ruby 的 [Bundler](http://bundler.io/) )之外，我还为不同的项目创建了一个快捷方式:为了在本地运行一个项目，它可能是介于`npm run`、Docker 容器或一个流浪者盒子之间的任何东西。在项目结构的简单层次上，也有很大的差异:所有东西都可能在根目录中，或者被分割在`src/`和`docker/`目录中。在这方面，每个项目都是它自己特殊的雪花。

在用 shell 脚本做了一个总是来自项目根的重复任务的短暂尝试后，我很快切换到使用`make`来代替。它可以广泛使用，并使处理不同的命令变得容易。所以我开始为 Makefiles 开发我的策略。它的模式完美地适用于我的大多数基于网络的项目:

*   `make setup`–在第一次设置时做所有必要的事情，比如安装依赖项，比如为一个节点项目运行`npm install`。
*   `make run`–这通常默认为`docker-compose up`。或者去`bundle exec jekyll serve --drafts`看这个博客。
*   你可能已经猜到了:它执行项目的单元测试。

当然不止于此。通常会有更多的命令触发现有的构建系统，比如[和](https://ant.apache.org/)。或者只是作为`npm scripts`的别名。尽管如此，这是一个很好的捷径，节省了我在项目之间切换的时间。

一个 PHP 项目的小例子，它使用 Composer 并带有 Docker-Compose 设置。测试在 Docker 容器中执行。

```
.PHONY: setup
setup:
    @cd src/ && \
    composer install

.PHONY: test
test:
    docker-compose run web /bin/bash -c 'composer test' && exit

.PHONY: run
run:
    docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果您碰巧也经常在不同的项目之间切换，也许您也想尝试一下 Makefiles。