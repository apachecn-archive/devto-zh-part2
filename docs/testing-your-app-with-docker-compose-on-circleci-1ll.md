# 在 CircleCI 上用 docker-compose 测试你的应用

> 原文：<https://dev.to/einenlum/testing-your-app-with-docker-compose-on-circleci-1ll>

*本文原载于[我的博客](https://www.einenlum.com/articles/docker-compose-circle-ci)T3。*

作为一名开发人员，您使用 docker 作为开发环境。你很高兴你终于掌握了它(或者至少让它工作了)。因为你是一个优秀的开发人员，你知道自动化测试的重要性，并且你写了一堆的规格或者特性来测试。多亏了几个 docker-compose 命令，您让一切都在本地工作。也许你现在会想，通过将启动这些测试的过程委托给一个 CI，它会监听你的 Github 事件，并在你的 Pull 请求页面上给你发回一个绿色或红色的点，这样做甚至会很棒。

自从你几年前使用 CircleCI，自从他们声称 docker 很容易使用，自从他们提供了一个免费的计划，你认为你会成功。好吧，让我告诉你，如果你没有正确的说明，这可能是相当痛苦的，当涉及到使用 docker-compose 时，文档是相当糟糕的。幸运的是，当您在配置中使用正确的选项时，这非常容易。

## 让我们深入了解一下吧！

在创建了 CircleCI 帐户并将其连接到存储库之后，您将得到 CircleCI 提供的一个示例配置文件。可能看起来是这样的(哼...):

```
version: 2
jobs:
  build:
    working_directory: ~/mern-starter
    # The primary container is an instance of the first image listed. The job's commands run in this container.
    docker:
      - image: circleci/node:4.8.2-jessie
    # The secondary container is an instance of the second listed image which is run in a common network where ports exposed on the primary container are available on localhost.
      - image: mongo:3.4.4-jessie
    steps:
      - checkout
      - run:
          name: Update npm
          command: 'sudo  npm  install  -g  npm@latest'
      - restore_cache:
          key: dependency-cache-{{ checksum "package.json" }}
      - run:
          name: Install npm wee
          command: npm install
      - save_cache:
          key: dependency-cache-{{ checksum "package.json" }}
          paths:
            - node_modules
  test:
    docker:
      - image: circleci/node:4.8.2-jessie
      - image: mongo:3.4.4-jessie
    steps:
      - checkout
      - run:
          name: Test
          command: npm test
      - run:
          name: Generate code coverage
          command: './node_modules/.bin/nyc  report  --reporter=text-lcov'
      - store_artifacts:
          path: test-results.xml
          prefix: tests
      - store_artifacts:
          path: coverage
          prefix: coverage

workflows:
  version: 2
  build_and_test:
    jobs:
      - build
      - test:
          requires:
            - build
          filters:
            branches:
              only: master 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那很吓人。至少有`docker`这个关键词，所以你觉得你走的方向是对的。让我告诉我你错了。

实际上，这个`docker`关键字意味着您的 CircleCI 构建将在 docker 容器中启动，而不是在 Linux VM 中。在阅读了整个文档之后，您意识到您必须使用一个 Linux VM 才能使用 docker-compose with volumes(链接:[https://circleci.com/docs/2.0/executor-types/](https://circleci.com/docs/2.0/executor-types/))。

因此，让我们一步一步地创建一个新的配置文件。

```
version: 2 # CircleCI version
jobs:
  build:
    machine: true # Use a Linux VM instead of docker environment
    working_directory: ~/repo # Default working directory, where your project will be cloned 
```

Enter fullscreen mode Exit fullscreen mode

所以我们有一个非常基本的安装。很好。让我们添加一个库的签出。

```
 build:
    # ..
    steps:
        - checkout
        # Maybe you need to add some config file specific to circle…
        - run: cp .circleci/.some-parameter-file .some-parameter-file 
```

Enter fullscreen mode Exit fullscreen mode

Docker-compose 默认安装在 CircleCI 提供的 Linux VM 上。我们可以直接建造集装箱。

```
 build:
    # ..
    steps:
        # - …
        - run: docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以安装项目的依赖项了。这里我们有节点(yarn)和 PHP (composer)依赖关系。

```
 steps:
        # - …
        - run: docker-compose exec php composer install -n --prefer-dist
        - run: docker-compose run --rm front yarn install
        - run: docker-compose run --rm front yarn build 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行我们的测试了！

```
 # run tests!

    steps:
        # - …
        - run: docker-compose exec php vendor/bin/phpspec run -vvv
        - run: docker-compose exec php ./vendor/bin/behat -v 
```

Enter fullscreen mode Exit fullscreen mode

Tada！额外的奖金？假设您进行了一些端到端测试，并且在测试失败时拍摄了一些截图……也许您希望能够从 CircleCI 接口访问截图(或日志文件)？您可以添加一个目录作为`artifact`。这将在测试上方的`artifacts`链接中提供。

```
 steps:
        # …

        # This line is to be able to access failing screenshots in the
        # artifacts section in CircleCI
        - store_artifacts:
            path: ~/repo/features/fail-screenshots 
```

Enter fullscreen mode Exit fullscreen mode

太酷了！现在，您有了一个运行 docker-compose 的配置项，使用您的开发环境。漂亮的赃物。
现在，你们中的一些人可能会因为用户而遇到权限问题。这可能是因为 CircleCI 使用 id 为`1001`的用户和 id 为`1002`的组。有时 in 会与您的本地权限冲突(您的本地用户一般是`1000`)。如果是这样，您可以指定将启动 docker 命令的用户。

```
 steps:
        # run tests!

        # Circleci uses a user with id 1001 and a group with id 1002
        # We use this user instead of the default one in our dockerfile (1000)
        # to avoid permissions issues
        - run: docker-compose exec --user=1001:1002 php vendor/bin/phpspec run -vvv

        # We use the root user here to change logs and cache permissions
        - run: docker-compose exec --user=root php chmod -R 777 var/logs
        - run: docker-compose exec --user=root php chmod -R 777 var/cache 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇文章能帮助你让这个小绿点出现在你的 PRs 上:)。

*(使用 Gitlab？见[此处](https://dev.to/einenlum/a-free-ci-using-docker-compose-for-your-gitlab-repositories-6po) )*

*图片来源:[【亚历山德罗曼奇尼](https://www.flickr.com/photos/ingens/8882619935/)的《圈里的砖块》*