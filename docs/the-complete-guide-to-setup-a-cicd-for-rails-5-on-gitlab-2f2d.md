# 在 Gitlab 上为 Rails 6+设置 CI/CD 的完整指南

> 原文：<https://dev.to/zimski/the-complete-guide-to-setup-a-cicd-for-rails-5-on-gitlab-2f2d>

# 在 Gitlab 上持续集成/部署 Rails

[![Gitlab piplines](../Images/a3f6bb023d415605e2c4261c4b2edcb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wK2vft7t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.heyzimo.com/assets/images/pipline_green.png)

在这篇博文中，我们将通过必要的步骤来设置 Gitlab，以便
运行 Rails build，测试&部署，如果一切正常的话。
我会特别关注`rails system test`以及如何让它们发挥作用。

我们将使用 Heroku 部署我们的分期应用程序。

# 我们将实现什么？

## 建造阶段

该建筑将包含:

*   依赖项的安装
*   数据库设置
*   资产的预编译(资产和 webpacker)

## 测试阶段

### 集成测试

在这个阶段，我们将运行我们所有的集成测试，这些测试基本上会变成
运行:
`bundle exec rails test`

### 系统测试

这是我们 CI 中最激动人心和最重要的部分。
系统测试在测试复杂的 UI 方面非常有用，这需要大量使用 Javascript 的
(Vue app 的 React)以及与外部服务如`Google
Map Places`的交互。

系统测试将模仿普通用户，像普通用户一样在我们的应用程序上点击和填写输入。
该阶段执行的主命令是`bundle exec rails test:system`。

这种情况下的交互事实是使用容器嵌入`Selenium
Chrome browser`来运行真正的浏览器以获取和测试我们的前端。

## 部署阶段

这是一个简单的步骤，我们将把我们的应用程序部署到登台环境中。

* * *

# git lab-CI

Gitlab 向每个人(我们应该感谢他们所做的一切工作)提供了一个定义如何测试/部署代码的方法
以及这些任务所需的所有服务。
所有的指令都存储在`.gitlab-ci`中，存在于我们的 repo 的根目录中。

这为我们提供了一个*集中的*和一个*简单的方式*来管理我们的源代码和
我们对`FREE`的持续集成。

## 它是如何工作的

配置项遵循以下简单步骤:

1.  引导您在`.gitlab-ci`中指定的一个或几个容器，又名`services`
2.  在主容器中复制您的回购。
3.  运行其中需要的所有脚本

## 使用缓存加速 CI

Gitlab 允许我们缓存文件夹和文件，并在接下来的作业中使用它们。
不需要重新编译所有的依赖项，甚至下载它们。
在我们的例子中，缓存所有的`gems`和`node_modules`将会节省我们几分钟的时间。

## 使用工件来调试我们的测试

当系统测试失败时，测试会将`screenshots`保存在`temp`文件夹中。

`artifacts`使我们有可能保存那些文件并把它们与工作联系起来。

当我们想调试一个失败的系统测试时，这将会给我们很大的帮助。

* * *

# 开始吧

## 1。建筑

### 准备构建容器

构建将在一个容器中执行，所以我们应该有一个容器，里面捆绑了所有需要的依赖项。

对于现代 rails 应用程序，我们应该包括:

*   红宝石
*   节点+纱线
*   一些系统库

这里是`dockerfile`

```
FROM ruby:2.4.3

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update -qqy && apt-get install  -qqyy yarn nodejs postgresql postgresql-contrib libpq-dev cmake

RUN rm -rf /var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

简单耶！

构建容器

```
docker build .
Sending build context to Docker daemon  2.048kB
Step 1/6 : FROM ruby:2.6.5
2.6.5: Pulling from library/ruby
16ea0e8c8879: Pull complete 50024b0106d5: Pull complete ff95660c6937: Pull complete 9c7d0e5c0bc2: Pull complete 29c4fb388fdf: Pull complete 069ad1aadbe0: Pull complete e7188792d9dd: Pull complete bae7e74440d1: Pull complete Digest: sha256:2285f291f222e1b53d22449cc52bad2112f519bcce60248ea1c4d5e8f14c7c04
Status: Downloaded newer image for ruby:2.6.5
 ---> 2ff4e698f315
Step 2/6 : RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
 ---> Running in abb67e50af3e
Warning: apt-key output should not be parsed (stdout is not a terminal)
OK
Removing intermediate container abb67e50af3e
 ---> 461e2dd2134d
Step 3/6 : RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
 ---> Running in 414f508a391c

## Installing the NodeSource Node.js 8.x LTS Carbon repo...

.....
Processing triggers for libc-bin (2.28-10) ...
Removing intermediate container af1183021a8d
 ---> 603cab5f6952
Step 6/6 : RUN rm -rf /var/lib/apt/lists/*
 ---> Running in 53c5950a25c1
Removing intermediate container 53c5950a25c1
 ---> 42b50699301e
Successfully built 42b50699301e 
```

Enter fullscreen mode Exit fullscreen mode

标记它

```
docker tag 42b50699301e registry.gitlab.com/[ORG]/[REPO]/[CONTAINER]:v1 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们应该发布这个容器，使 GitlabCI 能够使用它。

为我们提供一个集装箱登记处！又免费了！

所以我们只需要将这个容器放入项目注册表中。

首先，你应该登录 gitlab 注册表

```
docker login registry.gitlab.com
# use your gitlab credential 
```

Enter fullscreen mode Exit fullscreen mode

并按下

```
docker push registry.gitlab.com/[ORG]/[REPO]/[CONTAINER]:v1 # v1 is my version tag 
```

Enter fullscreen mode Exit fullscreen mode

如果你有`ADSL`互联网连接，上传速度很差，你可以去
打个盹；)

一旦推送完成，我们就可以进入下一步了。

### 构建脚本

这是 gitlab-ci 文件
中的主要构建部分

```
image: "registry.gitlab.com/[ORG]/[REPO]/[CONTAINER]:v1"

variables:
  LC_ALL: C.UTF-8
  LANG: en_US.UTF-8
  LANGUAGE: en_US.UTF-8
  RAILS_ENV: "test"
  POSTGRES_DB: test_db
  POSTGRES_USER: runner
  POSTGRES_PASSWORD: ""

# cache gems and node_modules for next usage
.default-cache: &default-cache
  cache:
    untracked: true
    key: my-project-key-5.2
    paths:
      - node_modules/
      - vendor/
      - public/

build:
  <<: *default-cache
  services:
    - postgres:latest
  stage: build
  script:
  - ruby -v
  - node -v
  - yarn --version
  - which ruby
  - gem install bundler  --no-ri --no-rdoc
  - bundle install  --jobs $(nproc) "${FLAGS[@]}" --path=vendor
  - yarn install
  - cp config/database.gitlab config/database.yml
  - RAILS_ENV=test bundle exec rake db:create db:schema:load
  - RAILS_ENV=test bundle exec rails assets:precompile 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们使用之前创建的映像来托管构建。

我们应该在项目中添加一个`config/database.gitlab`来替换原来的
数据库配置，并使用自定义主机和凭证来连接到由 GitlabCI 引导的 postgres
容器。

```
 services:
    - postgres:latest 
```

Enter fullscreen mode Exit fullscreen mode

Gitlab 在读取这一行时将启动一个数据库容器(postgress)并且
将使用之前定义的变量来设置数据库

```
 POSTGRES_DB: test_db
  POSTGRES_USER: runner
  POSTGRES_PASSWORD: "" 
```

Enter fullscreen mode Exit fullscreen mode

`config/database.gitlab`将告诉我们的 rails 应用程序如何连接到
数据库，因此在应用程序启动之前，`database.yml`将被自定义的
所取代。

```
test:
  adapter: postgresql
  encoding: unicode
  pool: 5
  timeout: 5000
  host: postgres
  username: runner
  password: ""
  database: test_db 
```

Enter fullscreen mode Exit fullscreen mode

## 2。集成测试脚本

这个
不需要更多解释

```
integration_test:
  <<: *default-cache
  stage: test
  services:
    - postgres:latest
    - redis:alpine
  script:
    - gem install bundler  --no-ri --no-rdoc
    - bundle install  --jobs $(nproc) "${FLAGS[@]}" --path=vendor
    - cp config/database.gitlab config/database.yml
    - bundle install --jobs $(nproc) "${FLAGS[@]}" --path=vendor
    - RAILS_ENV=test bundle exec rake db:create db:schema:load
    - RAILS_ENV=test bundle exec rails assets:precompile
    - bundle exec rake test 
```

Enter fullscreen mode Exit fullscreen mode

## 3。系统测试脚本

使系统测试成为可能的基础设施非常有趣。

要运行测试，我们应该启动一个浏览器(在一个容器中)并从 rails 服务器(从另一个容器)获取页面
。

[![System tests & containers](../Images/81519166e68077cbe10ff2277f22fdda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WAIm9-LI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.heyzimo.com/assets/images/system_tests.png)T3】

```
system_test:
  <<: *default-cache
  stage: test
  services:
    - postgres:latest
    - redis:alpine
    - selenium/standalone-chrome:latest
  script:
    - gem install bundler  --no-ri --no-rdoc
    - bundle install  --jobs $(nproc) "${FLAGS[@]}" --path=vendor
    - cp config/database.gitlab config/database.yml
    - export selenium_remote_url="http://selenium__standalone-chrome:4444/wd/hub/"
    - bundle install  --jobs $(nproc) "${FLAGS[@]}" --path=vendor
    - RAILS_ENV=test bundle exec rake db:create db:schema:load
    - RAILS_ENV=test bundle exec rails assets:precompile
    - bundle exec rake test:system
  artifacts:
    when: on_failure
    paths:
      - tmp/screenshots/ 
```

Enter fullscreen mode Exit fullscreen mode

我们应该告诉水豚使用正确的`IP`而不是`localhost`，因为在这里我们将浏览器
和服务器放在两个不同的容器中。

在`environment/test.rb`中，添加这些行

```
 net = Socket.ip_address_list.detect{|addr| addr.ipv4_private? }
  ip = net.nil? ? 'localhost' : net.ip_address
  config.domain = ip
  config.action_mailer.default_url_options = { :host => config.domain }

  Capybara.server_port = 8200
  Capybara.server_host = ip 
```

Enter fullscreen mode Exit fullscreen mode

而且我们应该告诉系统测试在哪里找到`chrome driver`来控制浏览器，更新`application_system_test_case.rb`

```
require "test_helper"
require "socket"

def prepare_options
  driver_options = {
    desired_capabilities: {
      chromeOptions: {
        args: %w[headless disable-gpu disable-dev-shm-usage] # preserve memory & cpu consumption
      }
    }
  }

  driver_options[:url] = ENV['selenium_remote_url'] if ENV['selenium_remote_url']

  driver_options
end

class ApplicationSystemTestCase < ActionDispatch::SystemTestCase
  driven_by :selenium, using: :chrome, screen_size: [1400, 1400],
            options: prepare_options
end 
```

Enter fullscreen mode Exit fullscreen mode

`rails system test`将截图并保存到`tmp/screenshots`

[![System tests & scrennshots](../Images/b2af60abee3f88c2228d7db0e7beab08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EOMHKdcW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.heyzimo.com/assets/images/screenshots.png){:class = " img-responsive " }

正如你所看到的，截图被保存并附在 job 上，整洁！

## 4。分段部署

如果`build`和`tests`阶段成功，这将部署我们的代码。

```
deploy_staging:
  stage: deploy
  variables:
    HEROKU_APP_NAME: YOUR_HEROKU_APP_NAME
  dependencies:
    - integration_test
    - system_test
  only:
    - master
  script:
    - gem install dpl
    - dpl --provider=heroku --app=$HEROKU_APP_NAME --api-key=$HEROKU_API_KEY 
```

Enter fullscreen mode Exit fullscreen mode

`HEROKU_API_KEY`存储在项目设置中的安全位置

[![Gitlab CI variables](../Images/9f4b6316f4c6554bf878cfa5b5c885dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zla4N2Rb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.heyzimo.com/assets/images/gitlab_variables.png)

有关这方面的更多信息，请访问 [Gitlab 变量文档](https://docs.gitlab.com/ee/ci/variables/)

* * *

# 结论

`Gitlab`是一个令人惊叹的项目，提供了一个非常好的地方，在那里一切都被
很好地集成，编码体验得到了增强。

最后，让我们希望迁移到`Google compute engine`将为项目提供更好的健壮性和更少的问题。

> Gitlab 万岁！！

干杯！

这里是完整的 [`Gitlab CI`文件](https://gitlab.com/snippets/1745898)