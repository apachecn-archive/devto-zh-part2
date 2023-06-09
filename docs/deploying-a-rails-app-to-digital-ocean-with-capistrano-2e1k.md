# 使用 Capistrano 将 Rails 应用程序部署到数字海洋

> 原文：<https://dev.to/drbragg/deploying-a-rails-app-to-digital-ocean-with-capistrano-2e1k>

这是我如何设置 DO 服务器和部署 rails 应用程序的基本步骤。我最初是从 GoRails 的截屏中了解到这一点的，但随着时间的推移，我一直在对它进行迭代和调整/改进。也许这篇文章会帮助你让你的应用程序上线，或者你有更好的方法可以和我分享。

如果你还没有数字海洋账户，你可以通过这个链接注册，[https://m.do.co/c/33c1f4fb065a](https://m.do.co/c/33c1f4fb065a)。是的，如果你使用我的链接，我会得到推荐积分，但前提是你最终会和他们一起花钱，即使你没有，如果你在 Hacktoberfest 期间注册，你仍然可以得到 100 美元的积分，持续 60 天。

让我们启动服务器！

### 创建液滴

*   创建一个液滴

    *   选择 Ubuntu 作为您的发行版(16.04.4 x64)
    *   选择 droplet 的大小(最小 1GB 和 1CPU)
    *   选择纽约 1 或 3 作为您的数据中心(我在东海岸，所以这对我最有意义)
    *   可选:
    *   现在添加 SSH 密钥(以后也可以这样做)
    *   重命名主机名(通常是有意义的名称)
*   创建 droplet 后，会发送一封包含登录凭据的电子邮件

*   使用提供的凭据将 SSH 导入新的 drop

*   最佳实践是让新用户来管理部署，而不是使用`root`

    *   `$ sudo adduser deploy`
    *   `$ sudo adduser deploy sudo`
    *   `$ su deploy`

如果您在创建 droplet 时没有提供 SSH 密钥，现在应该提供了。如果你有，跳过这个:

#### 在你的电脑上做这件事，而不是在服务器上！

我用的是一个叫 ssh-copy-id 的 MacOS 工具。如果您没有它，您可以通过运行`$ brew install ssh-copy-id`
*运行`$ ssh-copy-id deploy@SERVERIP`来获得它，其中 SERVERIP 是您的 droplet 的 IP 地址

回到我们定期安排的节目

*   以部署用户的身份通过 SSH 返回到服务器

在部署时，在服务器上运行以下命令

*   首先得到节点和纱线。

    *   `$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -`
    *   `$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -`
    *   `$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list`
*   然后安装基本的依赖项。

    *   `$ sudo apt-get update`
    *   `$ sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev nodejs yarn`
*   然后安装 rbenv(为了管理我们的 ruby 版本，如果你愿意，可以使用 rvm，但是我更喜欢 rbenv)

    *   `$ cd`
    *   `$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv`
    *   `$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc`
    *   `$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc`
    *   `$ exec $SHELL`
    *   `$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build`
    *   `$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc`
    *   `$ exec $SHELL`
*   最后安装 ruby

    *   或者你的应用程序正在运行的任何版本的 ruby
    *   或者你刚刚安装的任何东西
    *   `$ ruby -v` -验证是否安装和设置了正确的版本
    *   `$ gem install bundler`
    *   `$ rbenv rehash`

#### 与乘客设置 Nginx

这将实际运行我们的“服务器”,并为我们的应用程序提供服务(没有双关语)

*   `$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7`
*   `$ sudo apt-get install -y apt-transport-https ca-certificates`

*   `$ sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list'`

*   `$ sudo apt-get update`

*   `$ sudo apt-get install -y nginx-extras passenger`

##### 检查以确保你刚刚做的一切都正常工作

在服务器上运行`$ sudo service nginx start`，然后在浏览器中访问 droplets 的 IP 地址

如果到目前为止一切正常，您应该会看到一条 Nginx 欢迎消息。

##### 配置 Nginx 和乘客

*   `$ sudo vim /etc/nginx/nginx.conf`
    *   找到以下代码行并取消注释`include /etc/nginx/passenger.conf` :

        ```
        ##
        # Phusion Passenger
        ##
        # Uncomment it if you installed ruby-passenger or ruby-passenger-enterprise
        ##
        ```

包括/etc/nginx/passenger . conf；

*   保存并关闭(:wq)

    *   `$ sudo vim /etc/nginx/passenger.conf`
*   将 passenger_ruby 行改为指向 rbenv(或者 rvm，如果您使用了它):

    *   `passenger_ruby /home/deploy/.rbenv/shims/ruby;`
*   保存并关闭(:wq)

##### 一旦这一切完成，重启 Nginx 服务器

*   `$ sudo service nginx restart`

### 环境变量

*   确保您位于“部署用户”部分的最顶层
    *   运行`$ cd ~`
*   `$ git clone [https://github.com/rbenv/rbenv-vars.git](https://github.com/rbenv/rbenv-vars.git) $(rbenv root)/plugins/rbenv-vars`
*   `$ vim .rbenv-vars`
*   将您需要的任何环境变量添加到文件中。

### PostgreSQL

*   运行`$ sudo apt-get install postgresql postgresql-contrib libpq-dev`
*   设置 postgres 用户(也称为“deploy”，但不同于我们的 linux 用户“deploy”)和 DB
    *   `$ sudo su - postgres`
    *   `$ createuser --pwprompt deploy` -记下您选择的密码
    *   `$ createdb -O deploy APP_NAME_production` -其中 APP_NAME 是应用程序的名称
    *   `$ exit`

### 正在部署

首先，我们需要配置我们的回购桶，以便能够与我们的服务器对话。因为我们直接从 repo 部署，而不是本地机器。

*   当登录我们的服务器时，在命令行输入`ssh-keygen`。
*   按照提示设置新密钥。在这种情况下，不需要口令。
*   一旦服务器生成了新的密钥，运行`cat ~/.ssh/id_rsa.pub`并复制返回的密钥
*   将密钥添加到应用程序的回购中
    *   如果你使用的是 bitbucket，可以在*设置* > *访问键*中找到

回到您的本地机器，让我们设置 capistrano

*   在您的 gem 文件中添加:

    > gem ' capistrano '
    > gem ' capistrano-rails '
    > gem ' capistrano-passenger '
    > gem ' capistrano-rbenv '(如果您使用了 rvm use: gem 'capistrano-rvm ')

*   运行`$ bundle install`然后运行`$ cap install`

如果您想定制此安装，请查阅 capistrano 文档。

*   在`config/deploy.rb`中，更改以下行，将其更新为当前应用程序的名称和报告:

    ```
    set :application, "YOUR_APPS_NAME"
    set :repo_url, "YOUR_APPS_REPO"

    set :deploy_to, "/home/deploy/YOUR_APPS_NAME"

    append :linked_files, "config/database.yml", "config/secrets.yml"

    append :linked_dirs, "log", "tmp/pids", "tmp/cache", "tmp/sockets", "public/system"

    ```

*   在`config/deploy/production.rb`中，将服务器的 IP 地址更改为新 droplet 的 IP 地址

    ```
    server '104.131.189.221', user: 'deploy', roles: %w{app db web}

    ```

*   然后，您可以使用命令`cap production deploy`来部署新版本的应用程序

    *   第一次尝试时，它会失败并抛出一个错误，但我们就快成功了。

### 首页延伸

SSH 返回到服务器，部署并创建 2 个文件

*   `/home/deploy/APP_NAME/shared/config/database.yml`

    *   在该文件中添加以下内容:

        ```
        production:
        adapter: postgresql
        host: 127.0.0.1
        database: APP_NAME_production
        username: deploy
        password: YOUR_POSTGRES_PASSWORD (NOT YOUR LINUX PASSWORD)
        encoding: unicode
        pool: 5

        ```

*   `/home/deploy/APP_NAME/shared/config/secrets.yml`

    *   在这个文件中添加以下内容:

        ```
        production:
        secret_key_base: YOUR_SECRET_KEY

        ```

        (注意:我相当确定在 Rails 5.2 中有一个更好的方法来做到这一点，但是我个人还没有在生产中使用过它)

运行`$ sudo visudo -f /etc/sudoers.d/nginx_overrides`并将以下内容添加到文件中:

```
  # Nginx Commands
  Cmnd_Alias NGINX_RESTART = /usr/sbin/service nginx restart
  Cmnd_Alias NGINX_RELOAD  = /usr/sbin/service nginx reload

  # No-Password Commands
  deploy ALL=NOPASSWD: NGINX_RESTART, NGINX_RELOAD

```

### 更新网址

在服务器上要做的最后一件事是告诉 Nginx 用应用程序来响应

*   运行`sudo ln -s /etc/nginx/sites-available/THE_URL_YOU_WANT_HERE.com /etc/nginx/sites-enabled/`
*   然后使用`sudo vim /etc/nginx/sites-enabled/THE_URL_YOU_WANT_HERE.com`将文件内容替换为:

    ```
    server {
      listen 80;
      listen [::]:80 ipv6only=on;

      server_name THE_URL_YOU_WANT_HERE.com;
      passenger_enabled on;
      rails_env    production;
      root         /home/deploy/APP_NAME/current/public;

      # redirect server error pages to the static page /50x.html
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   html;
      }
    }

    ```

*   回到您的本地机器上，再次运行`$ cap production deploy`,然后您应该可以开始工作了

*   如果你仍然看到默认的 Nginx 页面，运行`$ cap production server:restart`

如果我错过了什么或者我做错了什么(或者可以做得更好)，请让我知道。显然，运行一个“真正的”生产服务器，日志，安全，负载平衡等等还有很多，但这将让你的应用程序走向世界。另外，配置自己的服务器感觉很酷。