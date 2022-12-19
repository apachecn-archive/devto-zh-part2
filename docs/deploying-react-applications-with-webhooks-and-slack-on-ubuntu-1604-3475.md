# 在 Ubuntu 16.04 上使用 Webhooks 和 Slack 部署 React 应用程序

> 原文：<https://dev.to/digitalocean/deploying-react-applications-with-webhooks-and-slack-on-ubuntu-1604-3475>

Sanjin ari

作为[为捐赠而写](https://www.digitalocean.com/write-for-donations/?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)计划的一部分，作者选择了[科技教育基金](https://www.brightfunds.org/funds/tech-education)接受捐赠。

### 简介

当开发人员对应用程序进行持续的更改时，带有 webhooks 的部署系统可以简化开发，尤其是对团队而言。如果团队的一部分依赖于像 API 这样的后端软件，那么将代码变更的松弛通知集成到团队的工作流中也会很有帮助。

在本教程中，您将使用 [`create-react-app` `npm`包](https://github.com/facebook/create-react-app)构建一个 React 应用程序。这个包通过转换语法简化了 React 项目的引导工作，并通过依赖项和必备工具简化了工作。将您的应用程序代码添加到 GitHub 存储库之后，您将配置 Nginx 来服务您更新的项目文件。然后，您将下载并设置 webhook 服务器，并配置 GitHub 在您的代码被修改时与之通信。最后，您将配置 Slack 作为另一个 webhook 服务器，它将在触发成功部署时接收通知。

最终，您在本文中构建的部署系统将如下所示:

[![Sample deployment](../Images/2e5df154e9dec2fb2f7b2c45a0e2563e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--61agT5EC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assets.digitalocean.com/articles/react_deploy_webhooks/react-deploy.gif)

这个短视频展示了一个对 GitHub 存储库的空提交和推送，它触发了 Slack 中的应用程序构建和通知。

## 先决条件

要完成本教程，您需要:

*   一个 Ubuntu 16.04 服务器，你可以按照 Ubuntu 16.04 教程的[初始服务器设置来设置。遵循本指南后，您应该拥有一个拥有 sudo 权限的非 root 用户帐户。](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)
*   按照[如何在 Ubuntu 16.04 上安装 Nginx](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)中的前两步，在你的服务器上安装 Nginx。
*   在本地机器和服务器上都配置了 Git。您可以在本教程的[Git](https://www.digitalocean.com/community/tutorials/contributing-to-open-source-getting-started-with-git?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)入门中找到安装和配置 Git 的说明。
*   Node.js 和`npm`安装在您的本地机器和服务器上。对于您的服务器，请遵循[如何在 Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04#how-to-install-using-a-ppa?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand) 上安装 Node.js 中关于从 PPA 安装 Node.js 的说明。在您的本地机器上，您可以遵循[项目的安装说明](https://nodejs.org/en/download/)。
*   按照官方指南[安装 Yarn](https://yarnpkg.com/lang/en/docs/install/) 在您的服务器上安装 yarn。
*   为通知配置松弛时间和单个通道的权限。您可以在[松弛权限文档](https://get.slack.help/hc/en-us/articles/201314026-Roles-and-permissions-in-Slack)中找到关于角色和权限的更多信息。

## 步骤 1 —使用 create-react-app 创建 React 应用程序

让我们首先构建一个应用程序，我们将用它来测试我们的 webhooks 和`create-react-app`。然后，我们可以创建一个 GitHub 存储库，并将项目代码推送到其中。

在您的本地机器上，将`create-react-app`节点模块添加到您的全局存储库中，并使`create-react-app`命令在您的 shell 环境中可用:

```
sudo npm install -g create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

接下来，运行`create-react-app`来创建一个名为`do-react-example-app` :
的项目

```
create-react-app do-react-example-app 
```

Enter fullscreen mode Exit fullscreen mode

导航到目录`do-react-example-app` :

```
cd do-react-example-app 
```

Enter fullscreen mode Exit fullscreen mode

用`nano`或者你喜欢的文本编辑器，打开`package.json`文件:

```
nano package.json 
```

Enter fullscreen mode Exit fullscreen mode

该文件应该如下所示:

~/do-react-example-app/package . JSON

```
 {
  "name": "do-react-example-app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.2.0",
    "react-dom": "^16.2.0",
    "react-scripts": "1.0.17"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`package.json`文件包括以下脚本:

*   **`start`** :这个脚本负责启动应用程序的开发版本。它运行一个为应用程序服务的 HTTP 服务器。
*   **`build`** :这个脚本负责制作应用的量产版。您将在服务器上使用这个脚本。
*   **`test`** :这个脚本运行与项目相关的默认测试。
*   **`eject`** :这个脚本是`create-react-app`包的一个高级特性。如果开发人员对软件包提供的构建环境不满意，可以“弹出”应用程序，这将产生其他不可用的选项(包括自定义 CSS transpilers 和 JS 处理工具)。

检查完代码后，关闭文件。

接下来，让我们为项目创建一个 GitHub 存储库。你可以遵循这个关于[创建 GitHub 库](https://help.github.com/articles/create-a-repo/)的教程作为指导。记下存储库的来源(即它的 GitHub URL)。

回到您的`do-react-example-app`目录，用`git` :
初始化存储库

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加带有 GitHub URL 的远程源:

```
git remote add origin your-github-url 
```

Enter fullscreen mode Exit fullscreen mode

暂存项目目录中的所有文件:

```
git add . 
```

Enter fullscreen mode Exit fullscreen mode

提交它们:

```
git commit -m "initial commit" 
```

Enter fullscreen mode Exit fullscreen mode

并将它们推送到资源库:

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

关于创建 GitHub 库和用`git`初始化现有应用程序的更多信息，请参见 GitHub 的[文档](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)。

一旦我们完成了存储库设置，我们就可以继续指定服务器上的配置细节。

## 步骤 2 —目录设置和 Nginx 配置

有了存储库，现在就可以从 GitHub 获取应用程序代码，并配置 Nginx 来服务应用程序。

登录到您的服务器，转到您的主目录，并克隆您的存储库:

```
 cd ~

git clone your-github-url 
```

Enter fullscreen mode Exit fullscreen mode

转到克隆的项目:

```
cd do-react-example-app 
```

Enter fullscreen mode Exit fullscreen mode

要在项目和文件中创建一个构建目录供 Nginx 使用，您需要运行`yarn build`命令。这将运行项目的构建脚本，创建构建目录。这个文件夹包括一个`index.html`文件、一个 JavaScript 文件和一个 CSS 文件。`yarn`命令将下载项目所需的所有节点模块:

```
yarn && yarn build 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们在`/var/www/`目录中创建一个到`~/do-react-example-app`目录的符号链接。这将把应用程序保存在我们的主目录中，同时让 Nginx 可以从`/var/www`目录:

```
sudo ln -s ~/do-react-example-app /var/www/do-react-example-app 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这链接到项目目录，而不是构建目录，后者更改更频繁。在部署应用程序新版本的情况下，创建此链接尤其有用:通过创建指向稳定版本的链接，可以简化以后部署其他版本时将其换出的过程。如果出现问题，您也可以用同样的方式恢复到以前的版本。

应该在符号链接上设置一些权限，以便 Nginx 可以正确地为其服务:

```
sudo chmod -R 755 /var/www 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们配置一个 Nginx 服务器块来服务构建目录。通过键入:
进行新的服务器配置

```
sudo nano /etc/nginx/sites-available/test-server 
```

Enter fullscreen mode Exit fullscreen mode

复制以下配置，用您的 IP 或域(如果适用)替换`your_server_ip_or_domain`:

/etc/nginx/sites-available/测试服务器

```
server {
        listen 80;

        root /var/www/do-react-example-app/build;
        index index.html index.htm index.nginx-debian.html;

        server_name your_server_ip_or_domain;

        location / {
                try_files $uri /index.html;
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

该文件中的指令包括:

*   **`listen`** :配置服务器监听端口的属性。
*   **`root`**:Ngnix 提供文件的文件夹路径。
*   **`index`** :服务器先尝试服务的文件。它将尝试从`/var/www/do-react-example-app/build`目录中提供以下任意文件:`index.html`、`index.htm`、`index.nginx-debian.html`，优先级从第一个到最后一个。
*   **`server_name`** :服务器域名或 IP。

接下来，在`sites-enabled`目录中创建一个符号链接:

```
sudo ln -s /etc/nginx/sites-available/test-server /etc/nginx/sites-enabled/test-server 
```

Enter fullscreen mode Exit fullscreen mode

这将告诉 Nginx 从`sites-available`文件夹中启用服务器块配置。

检查配置是否有效:

```
sudo nginx -t 
```

Enter fullscreen mode Exit fullscreen mode

最后，重启 Nginx 来应用新的配置:

```
sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

有了这些配置细节，我们就可以继续配置 webhook 了。

## 步骤 3 —安装和配置 Webhooks

Webhooks 是简单的 HTTP 服务器，具有可配置的端点，称为 *hooks* 。一旦接收到 HTTP 请求，webhook 服务器就执行遵循一组可配置规则的可定制代码。互联网上已经有很多 webhook 服务器集成到应用程序中，包括 Slack。

webhook 服务器最广泛使用的实现是用 Go 编写的 [Webhook](https://github.com/adnanh/webhook) 。我们将使用这个工具来设置我们的 webhook 服务器。

确保您位于服务器上的主目录:

```
cd ~ 
```

Enter fullscreen mode Exit fullscreen mode

然后下载`webhook` :

```
wget https://github.com/adnanh/webhook/releases/download/2.6.6/webhook-linux-amd64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

提取出来:

```
tar -xvf webhook-linux-amd64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

通过将二进制文件移动到`/usr/local/bin` :
，使其在您的环境中可用

```
sudo mv webhook-linux-amd64/webhook /usr/local/bin 
```

Enter fullscreen mode Exit fullscreen mode

最后，清理下载的文件:

```
rm -rf webhook-linux-amd64* 
```

Enter fullscreen mode Exit fullscreen mode

通过键入:
来测试`webhook`在您的环境中的可用性

```
webhook -version 
```

Enter fullscreen mode Exit fullscreen mode

输出应该显示`webhook`版本:

```
Outputwebhook version 2.6.5 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们在`/opt`目录中设置`hooks`和`scripts`文件夹，第三方应用程序的文件通常放在这里。由于`/opt`目录通常归`root`所有，我们可以用 root 权限创建目录，然后将所有权转移到本地`$USER`。

首先，创建目录:

```
 sudo mkdir /opt/scripts

sudo mkdir /opt/hooks 
```

Enter fullscreen mode Exit fullscreen mode

然后将所有权转移到您的`$USER` :

```
 sudo chown -R $USER:$USER /opt/scripts

sudo chown -R $USER:$USER /opt/hooks 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们通过创建一个`hooks.json`文件来配置`webhook`服务器。用`nano`或者你喜欢的编辑器，在`/opt/hooks`目录下创建`hooks.json`文件:

```
nano /opt/hooks/hooks.json 
```

Enter fullscreen mode Exit fullscreen mode

对于 GitHub 发送 HTTP 请求时触发的`webhook`,我们的文件需要一个 JSON 规则数组。这些规则由以下属性组成:

```
{
    "id": "",
    "execute-command": "",
    "command-working-directory": "",
    "pass-arguments-to-command": [],
    "trigger-rule": {}
} 
```

Enter fullscreen mode Exit fullscreen mode

具体来说，这些规则定义了以下信息:

*   **`id`**:web hook 服务器将服务的端点名称。我们将称之为`redeploy-app`。
*   **`execute-command`** :钩子被触发时要执行的脚本的路径。在我们的例子中，这将是位于`/opt/scripts/redeploy.sh`的`redeploy.sh`脚本。
*   **`command-working-directory`** :执行命令时使用的工作目录。我们将使用`/opt/scripts`，因为那里是`redeploy.sh`的所在地。
*   **`pass-arguments-to-command`** :从 HTTP 请求传递给脚本的参数。我们将从 HTTP 请求的有效负载中传递提交消息、推送器名称和提交 id。这些信息也将包含在您的时差消息中。

`/opt/hooks/hooks.json`文件应包括以下信息:

/opt/hooks/hooks.json

```
[
  {
    "id": "redeploy-app",
    "execute-command": "/opt/scripts/redeploy.sh",
    "command-working-directory": "/opt/scripts",
    "pass-arguments-to-command":
    [
      {
        "source": "payload",
        "name": "head_commit.message"
      },
      {
        "source": "payload",
        "name": "pusher.name"
      },
      {
        "source": "payload",
        "name": "head_commit.id"
      }
    ],
    "trigger-rule": {}
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

GitHub HTTP POST 请求的有效载荷包括`head_commit.message`、`pusher.name`和`head_commit.id`属性。当您的 GitHub 存储库中发生一个已配置的事件(如 PUSH)时，GitHub 将发送一个 POST 请求，请求中包含关于该事件的信息。那些 POST 有效载荷的一些例子可以在 [GitHub 事件类型文档](https://developer.github.com/v3/activity/events/types/)中找到。

配置文件中的最后一个属性是`trigger-rule`属性，它告诉 webhook 服务器在什么条件下会触发钩子。如果留空，挂钩将总是被触发。在我们的例子中，我们将配置钩子在 GitHub 向我们的 webhook 服务器发送 POST 请求时被触发。具体来说，只有当 HTTP 请求中的 GitHub secret(这里表示为`your-github-secret`)与规则中的一致，并且提交发生在`master`分支时，才会被触发。

添加以下代码来定义`trigger-rule`，用您选择的密码替换`your-github-secret`:

```
... 
    "trigger-rule":
    {
      "and":
      [
        {
          "match":
          {
            "type": "payload-hash-sha1",
            "secret": "your-github-secret", 
            "parameter":
            {
              "source": "header",
              "name": "X-Hub-Signature"
            }
          }
        },
        {
          "match":
          {
            "type": "value",
            "value": "refs/heads/master",
            "parameter":
            {
              "source": "payload",
              "name": "ref"
            }
          }
        }
      ]
    }
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

完整地说，`/opt/hooks/hooks.json`会是这样的:

/opt/hooks/hooks.json

```
[
  {
    "id": "redeploy-app",
    "execute-command": "/opt/scripts/redeploy.sh",
    "command-working-directory": "/opt/scripts",
    "pass-arguments-to-command":
    [
      {
        "source": "payload",  
        "name": "head_commit.message"
      },
      {
        "source": "payload",
        "name": "pusher.name"
      },
      {
        "source": "payload",
        "name": "head_commit.id"
      }
    ],
    "trigger-rule":
    {
      "and":
      [
        {
          "match":
          {
            "type": "payload-hash-sha1",
            "secret": "your-github-secret", 
            "parameter":
            {
              "source": "header",
              "name": "X-Hub-Signature"
            }
          }
        },
        {
          "match":
          {
            "type": "value",
            "value": "refs/heads/master",
            "parameter":
            {
              "source": "payload",
              "name": "ref"
            }
          }
        }
      ]
    }
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

最后一个要检查的配置项目是服务器的防火墙设置。webhook 服务器将监听端口`9000`。这意味着如果服务器上运行防火墙，它将需要允许连接到此端口。要查看当前防火墙规则的列表，请键入:

```
sudo ufw status 
```

Enter fullscreen mode Exit fullscreen mode

如果端口`9000`不在列表中，启用它:

```
sudo ufw allow 9000 
```

Enter fullscreen mode Exit fullscreen mode

关于`ufw`的更多信息，请参见 [ufw 要领](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)的介绍。

接下来，让我们设置 GitHub 存储库，向这个端点发送 HTTP 请求。

## 步骤 4 —配置 GitHub 通知

让我们配置 GitHub 存储库，以便在向 master 提交时发送 HTTP 请求:

*   1.转到您的存储库并点击**设置**。
*   2.然后进入 **Webhooks** ，点击右上角的**添加 Webhook** 。
*   3.对于**有效负载 URL** ，按如下方式键入您的服务器地址:`http://your_server_ip:9000/hooks/redeploy-app`。如果你有域名，你可以用它来代替`your_server_ip`。注意，端点名与钩子定义中的`id`属性相匹配。这是 webhook 实现的一个细节:所有在`hooks.json`中定义的钩子都会以`http://your_server_ip:9000/hooks/id`的形式出现在 URL 中，其中`id`是`hooks.json`文件中的`id`。
*   4.对于**内容类型**，选择**应用/json** 。
*   5.对于**密码**，输入您在`hooks.json`定义中设置的密码(`your-github-secret`)。
*   6.对于**,您希望哪个事件触发此 webhook？**选择**刚推事件**。
*   7.点击**添加 webhook** 按钮。

现在，当有人向您的存储库提交提交时，GitHub 将发送一个 POST 请求，其中包含关于提交事件的信息。在其他有用的属性中，它将包含我们在触发规则中定义的属性，因此我们的 webhook 服务器可以检查 POST 请求是否有效。如果是，它将包含其他信息，如`pusher.name`。

与 payload 一起发送的属性的完整列表可以在 [GitHub Webhooks 页面](https://developer.github.com/webhooks/)上找到。

## 步骤 5 —编写部署/重新部署脚本

此时，我们已经将 webhook 指向了`redeploy.sh`脚本，但是我们还没有创建脚本本身。它将从我们的存储库中提取最新的主分支，安装节点模块，并执行构建命令。

创建脚本:

```
nano /opt/scripts/redeploy.sh 
```

Enter fullscreen mode Exit fullscreen mode

首先，让我们在脚本顶部添加一个函数，该函数将清理它创建的任何文件。如果重新部署没有成功，我们还可以使用它来通知 Slack 等第三方软件:

/opt/scripts/redeploy.sh

```
#!/bin/bash -e

function cleanup {
      echo "Error occoured"
      # !!Placeholder for Slack notification
}
trap cleanup ERR 
```

Enter fullscreen mode Exit fullscreen mode

这告诉`bash`解释器，如果脚本突然结束，它应该运行`cleanup`函数中的代码。

接下来，提取执行脚本时`webhook`传递给脚本的参数:

/opt/scripts/redeploy.sh

```
...

commit_message=$1 # head_commit.message
pusher_name=$2 # pusher.name
commit_id=$3 # head_commit.id

# !!Placeholder for Slack notification 
```

Enter fullscreen mode Exit fullscreen mode

注意，参数的顺序对应于来自`hooks.json`文件的`pass-arguments-to-command`属性。

最后，让我们调用重新部署应用程序所需的命令:

/opt/scripts/redeploy.sh

```
...

cd ~/do-react-example-app/
git pull origin master
yarn && yarn build

# !!Placeholder for Slack notification 
```

Enter fullscreen mode Exit fullscreen mode

完整的脚本如下所示:

/opt/scripts/redeploy.sh

```
#!/bin/bash -e

function cleanup {
      echo "Error occoured"
      # !!Placeholder for Slack notification
}
trap cleanup ERR

commit_message=$1 # head_commit.message
pusher_name=$2 # pusher.name
commit_id=$3 # head_commit.id

# !!Placeholder for Slack notification

cd ~/do-react-example-app/
git pull origin master
yarn && yarn build

# !!Placeholder for Slack notification 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将转到该文件夹，从最新的主分支中提取代码，安装新的包，并构建应用程序的生产版本。

注意`!!Placeholder for Slack notification`。这是本教程中最后一步的占位符。如果没有通知，就没有真正的方法知道脚本是否正确执行。

使脚本可执行，这样钩子就可以执行它:

```
chmod +x /opt/scripts/redeploy.sh 
```

Enter fullscreen mode Exit fullscreen mode

因为 Nginx 被配置为提供来自`/var/www/do-react-example-app/build`的文件，所以当这个脚本执行时，构建目录将被更新，Nginx 将自动提供新文件。

现在我们准备测试配置。让我们运行 webhook 服务器:

```
webhook -hooks /opt/hooks/hooks.json -verbose 
```

Enter fullscreen mode Exit fullscreen mode

`-hooks`参数告诉`webhook`配置文件的位置。

您将看到以下输出:

```
Output[webhook] 2017/12/10 13:32:03 version 2.6.5 starting
[webhook] 2017/12/10 13:32:03 setting up os signal watcher
[webhook] 2017/12/10 13:32:03 attempting to load hooks from /opt/hooks/hooks.json
[webhook] 2017/12/10 13:32:03 os signal watcher ready
[webhook] 2017/12/10 13:32:03 found 1 hook(s) in file
[webhook] 2017/12/10 13:32:03 loaded: redeploy-app
[webhook] 2017/12/10 13:32:03 serving hooks on http://0.0.0.0:9000/hooks/{id} 
```

Enter fullscreen mode Exit fullscreen mode

这告诉我们所有东西都被正确加载了，我们的服务器现在通过 URL `http://0.0.0.0:9000/hooks/redeploy-app`为钩子`redeploy-app`提供服务。这暴露了服务器上可执行的路径或挂钩。如果您现在用这个 URL 做一个简单的 REST 调用(比如 GET ),不会发生什么特别的事情，因为没有满足钩子规则。如果我们想要钩子被成功触发，我们必须满足我们在`hooks.json`中定义的`trigger-rule`。

让我们用本地项目目录中的空提交来测试一下。让你的 webhook 服务器保持运行，导航回你的本地机器并输入如下:

```
git commit --allow-empty -m "Trigger notification" 
```

Enter fullscreen mode Exit fullscreen mode

将提交推送到主分支:

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

您将在您的服务器上看到这样的输出:

```
Output[webhook] 2018/06/14 20:05:55 [af35f1] incoming HTTP request from 192.30.252.36:49554
[webhook] 2018/06/14 20:05:55 [af35f1] redeploy-app got matched
[webhook] 2018/06/14 20:05:55 [af35f1] redeploy-app hook triggered successfully
[webhook] 2018/06/14 20:05:55 200 | 726.412µs | 203.0.113.0:9000 | POST /hooks/redeploy-app
[webhook] 2018/06/14 20:05:55 [af35f1] executing /opt/scripts/redeploy.sh (/opt/scripts/redeploy.sh) with arguments ["/opt/scripts/redeploy.sh" "Trigger notification" "sammy" "82438acbf82f04d96c53cd684f8523231a1716d2"] and environment [] using /opt/scripts as cwd 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加 Slack 通知，看看当钩子触发一个带有通知的成功构建时会发生什么。

## 第 6 步—添加时差通知

为了在重新部署应用程序时接收 Slack 通知，您可以修改`redeploy.sh`脚本，向 Slack 发送 HTTP 请求。还需要通过启用 Slack 配置面板中的 **Webhook 集成**来配置 Slack 接收来自服务器的通知。一旦您从 Slack 获得了一个 **Webhook URL** ，您就可以将关于 Slack webhook 服务器的信息添加到您的脚本中。

要配置时差，请执行以下步骤:

*   1.在 Slack 应用程序的主屏幕上，单击位于左上方的下拉菜单并选择 **Customize Slack** 。
*   2.接下来，进入位于左侧栏**菜单**的**配置应用**部分。
*   3.在**管理**面板中，从左侧选项列表中选择**定制集成**。
*   4.搜索**传入的 WebHooks** 集成。
*   5.点击**添加配置**。
*   6.选择一个现有频道或创建一个新频道。
*   7.点击**添加传入的 WebHooks 集成**。

之后，您将看到一个显示 Slack webhook 设置的屏幕。记下 **Webhook URL** ，它是由 Slack webhook 服务器生成的端点。当您记下该 URL 并完成任何其他更改后，请务必按下页面底部的**保存设置**按钮。

返回到您的服务器并打开`redeploy.sh`脚本:

```
nano /opt/scripts/redeploy.sh 
```

Enter fullscreen mode Exit fullscreen mode

在上一步中，我们在脚本中为时差通知保留了占位符，表示为`!!Placeholder for Slack notification`。我们现在将用向 Slack webhook 服务器发出 POST HTTP 请求的`curl`调用来替换这些。Slack 钩子期待 JSON 主体，然后解析它，在通道中显示适当的通知。

用下面的`curl`调用替换`!!Placeholder for slack notification`。请注意，您需要将`your_slack_webhook_url`替换为您之前提到的 **Webhook URL** :

/opt/scripts/redeploy.sh

```
#!/bin/bash -e

function cleanup {
      echo "Error occoured"
      curl -X POST -H 'Content-type: application/json' --data "{
              \"text\": \"Error occoured while building app with changes from ${pusher_name} (${commit_id} -> ${commit_message})\",
              \"username\": \"buildbot\",
              \"icon_url\": \"https://i.imgur.com/JTq5At3.png\"
      }" your_slack_webhook_url
}
trap cleanup ERR

commit_message=$1 # head_commit.message
pusher_name=$2 # pusher.name
commit_id=$3 # head_commit.id

curl -X POST -H 'Content-type: application/json' --data "{
        \"text\": \"Started building app with changes from ${pusher_name} (${commit_id} -> ${commit_message})\",
        \"username\": \"buildbot\",
        \"icon_url\": \"https://i.imgur.com/JTq5At3.png\"
}" your_slack_webhook_url

cd ~/do-react-example-app/
git pull origin master
yarn && yarn build

curl -X POST -H 'Content-type: application/json' --data "{
        \"text\": \"Build and deploy finished with changes from ${pusher_name} (${commit_id} -> ${commit_message})\",
        \"username\": \"buildbot\",
        \"icon_url\": \"https://i.imgur.com/JTq5At3.png\"
}" your_slack_webhook_url 
```

Enter fullscreen mode Exit fullscreen mode

我们用略有不同的`curl`调用替换了每个占位符:

*   第一个确保我们收到执行脚本时发生的任何错误的通知。
*   第二个发送应用程序的构建已经开始的通知。
*   第三个发送构建已成功完成的通知。

关于 Slack 机器人和集成的更多信息可以在 Slack webhooks 文档中找到。

同样，我们可以在本地项目目录中用空提交来测试我们的钩子。让 webhook 服务器保持运行，导航回这个目录并创建空提交:

```
git commit --allow-empty -m "Trigger notification" 
```

Enter fullscreen mode Exit fullscreen mode

将提交推送到主分支以触发构建:

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

包括构建信息在内的输出如下所示:

```
Output[webhook] 2018/06/14 20:09:55 [1a67a4] incoming HTTP request from 192.30.252.34:62900
[webhook] 2018/06/14 20:09:55 [1a67a4] redeploy-app got matched
[webhook] 2018/06/14 20:09:55 [1a67a4] redeploy-app hook triggered successfully
[webhook] 2018/06/14 20:09:55 200 | 462.533µs | 203.0.113.0:9000 | POST /hooks/redeploy-app
[webhook] 2018/06/14 20:09:55 [1a67a4] executing /opt/scripts/redeploy.sh (/opt/scripts/redeploy.sh) with arguments ["/opt/scripts/redeploy.sh" "Trigger notification" "sammy" "5415869a4f126ccf4bfcf2951bcded69230f85c2"] and environment [] using /opt/scripts as cwd
[webhook] 2018/06/14 20:10:05 [1a67a4] command output: % Total % Received % Xferd Average Speed Time Time Time Current
                                 Dload Upload Total Spent Left Speed
100 228 0 2 100 226 11 1324 --:--:-- --:--:-- --:--:-- 1329
okFrom https://github.com/sammy/do-react-example-app
 * branch master -> FETCH_HEAD
   82438ac..5415869 master -> origin/master
Updating 82438ac..5415869
Fast-forward
yarn install v1.7.0
[1/4] Resolving packages...
success Already up-to-date.
Done in 1.16s.
yarn run v1.7.0
$ react-scripts build
Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  36.94 KB build/static/js/main.a0b7d8d3.js
  299 B build/static/css/main.c17080f1.css

The project was built assuming it is hosted at the server root.
You can control this with the homepage field in your package.json.
For example, add this to build it for GitHub Pages:

  "homepage" : "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  http://bit.ly/2vY88Kr

Done in 7.72s.
  % Total % Received % Xferd Average Speed Time Time Time Current
                                 Dload Upload Total Spent Left Speed
100 233 0 2 100 231 10 1165 --:--:-- --:--:-- --:--:-- 1166
ok
[webhook] 2018/06/14 20:10:05 [1a67a4] finished handling redeploy-app 
```

Enter fullscreen mode Exit fullscreen mode

在 Slack 中，您将收到发送到您选择的通道的消息，通知您应用程序构建已经开始以及何时完成。

## 结论

我们现在已经使用 webhooks、Nginx、shell 脚本和 Slack 完成了部署系统的设置。您现在应该能够:

*   配置 Nginx 来处理应用程序的动态构建。
*   设置 webhook 服务器并编写触发 GitHub POST 请求的钩子。
*   编写触发应用程序构建和通知的脚本。
*   配置 Slack 来接收这些通知。

本教程中的系统可以扩展，因为 webhook 服务器是模块化的，可以配置为与其他应用程序一起工作，如 [GitLab](https://about.gitlab.com/) 。如果通过 JSON 配置 webhook 服务器太麻烦，可以使用 [Hookdoo](https://www.hookdoo.com) 构建一个类似的设置。关于如何为`webhook`配置触发规则的更多信息可以在 webhook 项目[示例钩子页面](https://github.com/adnanh/webhook/wiki/Hook-Examples)中找到。

在 DigitalOcean 上构建、测试和部署一些新的东西——一体化云平台开发人员及其团队的最爱。为新用户提供 100 美元的免费帐户信用:[do.co/devto](http://do.co/devto)T3】

[![Creative Commons License](../Images/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/) 
本作品受[知识共享署名-非商业性使用-类似共享 4.0 国际许可](http://creativecommons.org/licenses/by-nc-sa/4.0/)的许可。