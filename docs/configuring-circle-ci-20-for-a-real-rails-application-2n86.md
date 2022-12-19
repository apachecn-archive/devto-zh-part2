# 为真实的 Rails 应用程序配置 Circle CI 2.0

> 原文：<https://dev.to/ozzyaaron/configuring-circle-ci-20-for-a-real-rails-application-2n86>

对于所有正在寻找在 Circle CI 上运行真正的 Rails 应用程序的快速指南的开发人员，请根据需要随意修改。

对于其他想了解 Circle 新的 2.0 基础设施的一些不寻常的方面的人，请在这个巨大的代码块之后继续阅读！

```
version: 2
jobs:
  build:
    parallelism: 8
    working_directory: ~/path/to/your/app
    docker: - image: circleci/ruby:2.5.1-node-browsers
        environment: CIRCLE_ARTIFACTS: /tmp/circleci-artifacts
          CIRCLE_TEST_REPORTS: /tmp/circleci-test-results
          RAILS_ENV: test
          YARN_VERSION: 1.7.0
          PGHOST: 127.0.0.1
          PGUSER: root

      - image: circleci/postgres:10.3-alpine-ram
        environment: POSTGRES_USER: root
          POSTGRES_DB: circle-test_test
    steps: - checkout
      - restore_cache:
          name: Restore Bundler cache
          keys: - iex-app-{{ checksum "Gemfile.lock" }}
            - iex-app-

      - run:
          name: Install NVM
          command: |
            set +e
            touch $BASH_ENV
            curl --retry 10 --retry-max-time 30 -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
            export NVM_DIR="$HOME/.nvm"
            [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
            nvm install 10.6.0

            echo 'export NVM_DIR="$HOME/.nvm"' >> $BASH_ENV
            echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> $BASH_ENV
            echo 'nvm alias default 10.6.0' >> $BASH_ENV

      - restore_cache:
          name: Restore Yarn Binary
          keys: - iex-app-yarn-{{ .Environment.YARN_VERSION }}
            - iex-app-yarn-

      - run:
          name: Prepare Yarn Path
          command: echo 'export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"' >> $BASH_ENV

      - run:
          name: Install Yarn
          command: |
            if [[ ! -e ~/.yarn/bin/yarn || $(yarn --version) != "${YARN_VERSION}" ]]; then
              echo "Download and install Yarn."
              echo "Current Yarn at `which yarn`"
              echo "Current yarn version `yarn --version`"
              echo "Current YARN_VERSION `echo $YARN_VERSION`"
              curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
              curl --retry 10 --retry-max-time 30 -o- -L https://yarnpkg.com/install.sh | bash -s -- --version $YARN_VERSION
            else
              echo "The correct version of Yarn is already installed."
            fi

      - save_cache:
          name: Cache Yarn Binary
          key: iex-app-yarn-{{ .Environment.YARN_VERSION }}
          paths: - ~/.yarn

      - restore_cache:
          name: Restore Yarn Package Cache
          keys:
            - yarn-packages-{{ checksum "yarn.lock" }}

      - run:
          name: Install node packages & build assets
          command: yarn -v && yarn install && yarn heroku-postbuild

      - save_cache:
          name: Save Yarn Package Cache
          key: yarn-packages-{{ checksum "yarn.lock" }}
          paths:
            - node_modules/

      - run:
          name: APT Installs (QT, PDFtk, psql-client)
          command: |
            sudo apt-get update
            sudo apt-get install -y software-properties-common
            sudo apt install -y gcc g++ make qt5-default libqt5webkit5-dev ruby-dev zlib1g-dev
            sudo apt-get install pdftk
            sudo apt install postgresql-client

      - run:
          name: Bundle Install
          command: bundle install --path=vendor/bundle --jobs 4 --retry 3 --without development database_management
          no_output_timeout: 30m

      # Store bundle cache
      - save_cache:
          key: iex-app-{{ checksum "Gemfile.lock" }}
          paths: - vendor/bundle

      - run:
          name: Wait for DB
          command: dockerize -wait tcp://localhost:5432 -timeout 1m

      - run:
          name: Database Setup
          command: |
            bundle exec rake db:create
            bundle exec rake db:structure:load

      - run:
          name: Create Artifacts Directory
          command: |
            mkdir tmp/artifacts
            echo 'export CI_ARTIFACTS="tmp/artifacts"' >> $BASH_ENV

      - run: mkdir ~/rspec

      - run:
          name: Run Specs
          command: |
            TESTFILES=$(circleci tests glob "spec/**/*_spec.rb" | circleci tests split --split-by=timings)
            bundle exec rspec --format progress --format RspecJunitFormatter -o ~/rspec/rspec.xml --tag ~wip -- ${TESTFILES}

      - store_test_results:
          path: ~/rspec

      - store_artifacts:
          path: ~/rspec

      - run: mkdir -p ~/cucumber

      - run:
          name: Run Cukes without Failing
          command: |
            TESTFILES=$(circleci tests glob "features/**/*.feature" | circleci tests split --split-by=timings)
            DONT_FAIL=true bundle exec cucumber --tags 'not @wip' --format rerun --out rerun.txt --format pretty --format json --out ~/cucumber/tests.cucumber ${TESTFILES}

      - run:
          name: Run Cukes that Failed
          command: |
            if [ -f rerun.txt ]; then
              bundle exec cucumber @rerun.txt
            fi

      - store_test_results:
          path: ~/cucumber

      - store_artifacts:
          path: ~/cucumber

      - store_artifacts:
          path: tmp/artifacts 
```

Enter fullscreen mode Exit fullscreen mode

你可能会注意到一些奇怪的事情:

*   将输出连接到`$BASH_ENV`
*   缓存 yarn 的二进制文件夹
*   我们必须告诉 Circle 等待*半个小时*来安装软件包

除此之外，很多事情都很标准，包括安装 nvm、构建资产、运行测试等等。

## 串联到$BASH_ENV

起初，您可能认为 2.0 基础设施下的命令是一系列一个接一个运行的命令。看起来它们实际上是一系列独立的命令，每次都重新加载 ENV。当您输出到`$BASH_ENV`时，您实际上是在添加将由每个后续命令完成的设置。

出于这个原因，我们在检查 yarn 的版本之前导出 yarn 的路径，并且我们设置 NVM 以便对于其他命令 NVM 被正确设置。在该命令中导出并继续是不够的，因为后续命令将不再具有您所导出的内容。

## 缓存纱线的二进制文件夹

我们遇到的一个新问题是，当我们使用建议的机制将 web 请求的输出管道化到 bash 时，yarn 经常无法安装。我们尝试了许多潜在的修复，比如 curl 的重试，但是真正将我们的测试与 yarn 安装失败隔离开来的最后一步是缓存已安装的文件。

似乎很多人在 Circle 2.0 下遇到了这个问题，但我找不到一个发布的解决方案，但这对我们来说效果很好。

有趣的是，即使我们在 Circle 的 1.0 基础设施下安装了一个新的 yarn 版本，我们也从未遇到过这个问题。

## 为什么我们需要等 30 分钟的 Bundler？！

我不知道。我在 Circle 上有一张支持票，但是他们没有给我答案。他们确实告诉我，2.0 基础架构下的新映像分配了 1.0 映像的一半内存，所以这可能只是交换的增加。

在本地，一个全新的安装不到一分钟就完成了，而在 Circle CI 1.0 中，这个时间是 2-3 分钟。

我得到指导是只添加超时配置选项。没有其他解决办法。

在大多数情况下，罪魁祸首似乎是编译本机扩展，如
capybara-webkit 或 nokogiri 所需要的，这两个库在讨论 Rails 时几乎无处不在。

## 其他名人

您可能还注意到我们使用了内存中的 Postgres 实例。这只是 Circle 善意提供的 docker 图片。这使得该系统的设置更加容易。

我们安装了一些 apt 包，如 pdftk，这样可以测试 PDF 的创建。

我们安装 QT，以便我们可以使用 capybara-webkit 进行测试。

我们对 Cucumber 特性进行了重试，这样只有失败的测试才会重新运行。如您所料，这些失败会出现在测试总结中。

除此之外，我们的配置相当繁忙，但就我所见，并不完全不寻常。这种繁忙只是 Circle 2.0 基础设施的一个产物，与 1.0 相比，它需要用户进行更多的配置和设置。

## 下一步

有一点对许多人来说可能是显而易见的，那就是当这些测试并行运行时所做的重复设置的数量。

Circle 现在提供工作流和一个允许您在这些工作流中的作业之间共享数据的功能。在未来，这将允许我们有一个任务来安装依赖项和编译资产，然后可以将这些资产分配给每个并行测试任务。

我试图让它运行起来，但当时文档与现实不符，Circle 的支持人员告诉我，文档用来尝试和实现我们想要的东西的方法不起作用。有一个新的特性在当时似乎没有被很好地记录下来。这是我将来要考虑的事情。

## 总结

在我看来，Circle 1.0 实际上相当不错，就好像你有一个普通的 Rails 应用程序一样；几乎不需要任何配置。在 Circle CI 2.0 中，似乎通过提供一个更加开放的平台，为相当普通的应用程序设置 CI 变得更加困难。在许多情况下，所提供的文档与要求相差甚远，我们的设置对于我工作过或咨询过的大多数 Rails 应用程序来说也没什么不寻常的。

此外，我们还在 Circle 上建立了我们的网站和一个旧的 PHP 应用程序，对他们来说也是如此。在 1.0 下很容易设置，但是在 2.0 下就有点困难了。

最后，我写这篇文章只是因为我希望它能帮助一些人！我不希望这篇文章读起来太像是在绕圈，因为在大多数情况下，我们能够将许多我们不想做的工作卸载给他们——维护 CI 服务器、与 slack 集成、与 Github 集成等等。我要说的是，这个过程比在他们以前的基础架构和我以前使用的其他 CI 系统下进行设置要困难得多。我意识到，如果你想在这个领域增加你的客户群，你的手会有些束缚——作为一个客户，这仍然不会让你的体验变得更好。

我们能够在一两天内运行我们的构建，花费这么长时间的原因主要是因为必须阅读文档和理解新系统。核心组件如 yarn 和 nvm 需要输出到`$BASH_ENV`,这是我在论坛上发现的。

在第一次胜利之后，我们花了几个星期的时间来修复间歇性的问题，以达到我们都认为相当稳定的东西。这些小修正是我希望这篇文章能够帮助解决的主要问题。

希望这能帮助到某个人！