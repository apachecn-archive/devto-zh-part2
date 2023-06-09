# 最简单的 Node.js 和 Express 服务器设置和部署

> 原文：<https://dev.to/zrbecker/a-minimal-nodejs-and-express-server-setup-and-deployment-3noe>

作为一名软件工程师，我经常忘记如何部署代码的复杂性。当我运行我编写的代码时，通常是在我的本地开发环境中完成，然后我将我的代码推送到一个存储库，在那里通过持续的部署魔术，我的代码变得对我的最终用户可用。

虽然我从这种复杂性中获得了许多好处，但我经常忘记它为什么会在那里，当事情不工作时，我只是对复杂性感到恼火。

在本文中，我将一个非常简单的 Node.js 和 Express 应用程序放在一起，并将其手动部署到 AWS 实例上。这个练习的目标是评估这样做的优点和缺点。

注意:值得一提的是最少，我指的是最少的部分，不一定是最少的步骤。像 Heroku 这样的东西可能会让你更快地启动和运行。

# 申请

我心目中的应用程序是一个简单的“Hello World！”快递申请。首先，我在 bash 中运行一些命令来设置我的环境。

```
mkdir express_hello_world
cd express_hello_world
npm init -y
npm install express 
```

Enter fullscreen mode Exit fullscreen mode

然后我用下面的 JavaScript 代码创建了`server.js`。

```
const express = require("express")
const app = express()
app.get("/", function(req, res) {
    res.send("Hello World")
})
app.listen(3000) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我用`node server.js`运行我的代码，我应该能够转到 [http://localhost:3000/](http://localhost:3000/) 并在浏览器中看到消息“Hello World”。

太好了！现在，提交和推动，并转移到下一件事，对不对？哦，等等，我们正在手动部署，我们有一些工作要做。

# 服务器

我们需要某种计算机来在网上运行我们的代码。特别是因为我的笔记本电脑不是全天候在线的，我们不希望任何人无法访问我们的问候。我走向 AWS 控制台。

*   点击“EC2”
*   点击“启动服务器”
*   选择“亚马逊 Linux 2 AMI”
*   点击“查看并启动”
*   点击“启动”
*   在下拉列表中选择“创建新的密钥对”
*   输入“express_hello_world”作为我的“密钥对名称”
*   单击“下载密钥对”
*   单击“启动实例”

咻！这只是几个步骤，其中大部分我并没有真正花时间去评估最佳选项，但是现在我们有了某种“默认”服务器来运行我们的代码。但是，我们仍然需要在服务器上安装 Node.js，并将我们的“Hello World”应用程序上传到服务器，然后确保它正在运行。

# 连接服务器

我们下载的密钥对是一个“pem”文件，它是一个私钥。我们可以使用这个密钥通过 SSH 客户端连接到服务器。我们还需要我们创建的 AWS 服务器的公共 IP 地址。在“EC2 Dashboard”中，单击左侧栏中的“Instances ”,找到“IPv4 Public IP”列，其中包含我们刚刚创建的实例的 IP 地址。

现在我们可以连接到服务器了。

```
mkdir -p ~/Documents/AWS/keys/express_hello_world/
cd ~/Documents/AWS/keys/express_hello_world/
cp ~/Downloads/express_hello_world.pem ./
chmod 400 express_hello_world.pem
ssh -i "express_hello_world.pem" ec2-user@<PUBLIC_IP_ADDRESS_OF_INSTANCE> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该在 AWS EC2 实例上看到 bash 提示符。

*注意:如果你使用的是 Windows，你可能应该遵循使用 PuTTY 从 Windows 连接到你的 Linux 实例[的说明。但是，您可以使用“来自 Linux 的 Windows 子系统”(WSL)。如果您使用 WSL，请确保将`express_hello_world.pem`放在 WSL 文件夹中，否则`chmod 400 express_hello_world.pem`将无法工作。](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html?icmpid=docs_ec2_console)*

# 安装 Node.js

在 Node.js 主页上，我们的服务器实例使用的 yum 包管理器的安装说明如下。

```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
sudo yum -y install nodejs 
```

Enter fullscreen mode Exit fullscreen mode

您可以键入`node --version`来检查它是否已安装，并且您使用的是正确的版本。撰写本文时，当前的 LTS 版本是 8.11.4。任何大于这个值的都可以正常工作。

# 创建用户

我们将创建一个用户来运行我们的应用程序。这让我们不必担心我们的应用程序作为 root 可以做什么，或者甚至像`ec2-user`一样潜在地使用 sudo 能力。

```
sudo useradd express_hello_world 
```

Enter fullscreen mode Exit fullscreen mode

# 将应用复制到服务器

通过键入`exit`退出我们当前的 ssh 会话。找到我们最初放置`server.js`和`package.json`文件的目录。我们将把整个目录复制到 EC2 服务器实例上的`ec2-user`主目录中。

完成之后，我们希望设置权限，这样所有者就是我们刚刚创建的`express_hello_world`用户，并将应用程序移动到`express_helo_world`主目录。

```
rm -rf <EXPRESS_APP_DIRECTORY>/node_modules
scp -r -i "express_hello_world.pem" \
    <EXPRESS_APP_DIRECTORY> \
    ec2-user@<PUBLIC_IP_ADDRESS_OF_INSTANCE>:express_hello_world_app
ssh -i "express_hello_world.pem" ec2-user@<PUBLIC_IP_ADDRESS_OF_INSTANCE>
sudo chmod 600 ./express_hello_world_app/*
sudo chown -R express_hello_world:express_hello_world ./express_hello_world_app
sudo mv ./express_hello_world_app /home/express_hello_world/express_hello_world_app 
```

Enter fullscreen mode Exit fullscreen mode

*注意:对于 Windows 用户来说，使用像 [FileZilla](https://filezilla-project.org/) 这样的程序将文件复制到服务器可能是最有意义的。尽管使用 WSL，您可以使用这里描述的`scp`方法。再次确保您的私钥位于具有正确权限的 WSL 目录中(400)。*

# 检查点 1:本地测试应用程序

这是做理智检查的好时机。我们应该能够从`express_hello_world`用户运行应用程序，并使用`curl`获取我们的“Hello World”消息。

成为`express_hello_world`用户并安装 Express 依赖项。我们还在这里测试，以确保我们得到一个“连接被拒绝”，因为服务器不应该运行。

```
sudo su - express_hello_world
cd ./express_hello_world_app
npm install curl localhost:3000  # Expected output "Connection Refused" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该能够启动服务器并从它那里获得“Hello World”响应。

```
node server.js &
SERVER_PID=$!
curl localhost:3000  # Expected output "Hello World" 
```

Enter fullscreen mode Exit fullscreen mode

经过验证后，我们得到了“Hello World”响应。关闭服务器，检查我们是否不再接受连接。

```
kill $SERVER_PID
curl localhost:3000  # Expected output "Connection Refused" 
```

Enter fullscreen mode Exit fullscreen mode

最后键入`exit`返回到`ec2-user`用户。

# 向世界公开应用程序

因为我们是以非特权用户的身份运行我们的应用程序，所以我们不能在端口 80(或任何低于 1024 的端口)上托管它。但是，我们可以创建一个防火墙规则，将该端口转发到我们托管的端口。

```
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to 3000 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要设置 AWS 安全规则，以允许端口 80 上的流量。从 EC2 仪表板。点击左侧工具条中的 Instaces。右键单击我们创建的实例，转到“网络”，然后“更改安全组”。查看我们当前选择的“安全组 ID ”,并记住它。点击“取消”。

在左侧栏中，单击“安全组”。右键单击我们在上一步中找到的带有“安全组 ID”的行。然后单击“编辑入站规则”。单击“添加规则”，并从下拉列表中选择“HTTP”。然后点击“保存”。

# 检查点 2:从 Web 测试应用程序

在你的浏览器中尝试进入。您应该会收到一条“此站点无法访问”的消息。这是因为我们在上一步中停止了服务器的运行。

继续并再次启动服务器。运行以下命令。

```
sudo su - express_hello_world
cd ./express_hello_world_app
node server.js &
SERVER_PID=$! 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们转到前面的 URL，我们应该会在浏览器中看到“Hello World”消息。

厉害！我们现在直播！

要关闭服务器，我们可以像上次一样关闭应用程序。

```
kill $SERVER_PID
exit  # To exit the express_hello_world user prompt 
```

Enter fullscreen mode Exit fullscreen mode

# 将服务器作为守护进程运行

我们有一个工作的服务器，但是如果我们没有将服务器进程绑定到我们当前的 SSH 会话，情况会好得多。我们可以使用 systemd 进程来做这件事。它还将确保服务器在失败时重新启动，并在服务器启动时自动启动。

在`/etc/systemd/system/`中创建一个文件`express_hello_world.service`。

```
sudo vim /etc/systemd/system/express_hello_world.service 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到该文件中。这些是我们守护进程的设置。您可以使用“ExecStart”来控制我们用来启动进程的命令。用户/组、设置了哪些环境变量以及当前工作目录。

```
[Unit]
Description=Express Hello World Application
After=network.target

[Service]
ExecStart=/usr/bin/node server.js
Restart=always
User=express_hello_world
Group=express_hello_world
Environment=PATH=/usr/bin:/usr/local/bin
Environment=NODE_ENV=production
WorkingDirectory=/home/express_hello_world/express_hello_world_app

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

为了开始我们的工作，我们使用`systemctl`。我们也可以使用 status 子命令来检查守护进程的状态。

```
sudo systemctl start express_hello_world.service
sudo systemctl status express_hello_world.service 
```

Enter fullscreen mode Exit fullscreen mode

## 调试

在运行状态之后，我们应该会看到“Active: active(正在运行)”。如果你看不到这一点，显然有什么地方出错了。使用`sudo journalctl --unit express_hello_world.service`检查`node service.js`命令的错误日志。

作业失败的一个可能原因是端口 3000 正在使用中。当你在其中一个检查点忘记杀死服务器的时候就会出现这种情况(我也遇到过这种情况，哈哈)。执行`ps aux | grep node`，找到仍在运行的进程，注意它是 PID，然后执行`sudo kill <PID>`。在此之后，守护进程应该会顺利启动。

## 重启

最后，为了确保应用程序在服务器启动时启动，运行命令

```
sudo systemctl enable express_hello_world.service 
```

Enter fullscreen mode Exit fullscreen mode

### 在重新启动之间保留 iptables

如果您现在重新启动，我们之前定义的 iptable 规则将不会在启动时恢复。我们还可以创建一个 systemd 服务来完成这项工作。创建文件`/etc/systemd/service/express_hello_world_iptables.service`并放入以下 systemd 配置。

```
[Unit]
Description=Forward port 80 to port 3000

[Service]
Type=oneshot
ExecStart=/bin/sh -c "iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to 3000"
ExecStop=/bin/sh -c "iptables -t nat -D PREROUTING -p tcp --dport 80 -j REDIRECT --to 3000"
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

然后删除我们已经创建的规则，并启动我们刚刚定义的服务(这将重新创建规则)。

```
iptables -t nat -D PREROUTING -p tcp --dport 80 -j REDIRECT --to 3000
sudo systemctl start express_hello_world_iptables.service
sudo systemctl enable express_hello_world_iptables.service 
```

Enter fullscreen mode Exit fullscreen mode

注意:感觉有更好的方法来处理 iptables 的东西，但是我找不到任何感觉非常简单的东西。这看起来不像是应该很复杂的事情。

# 检查点 3:测试我们的守护进程

在浏览器中再次进入

运行以下命令来停止我们的应用程序。

```
sudo systemctl stop express_hello_world.service 
```

Enter fullscreen mode Exit fullscreen mode

并刷新浏览器以确保服务器不再响应。

现在重新启动服务器。

```
sudo reboot 
```

Enter fullscreen mode Exit fullscreen mode

等待服务器重新启动，并检查网站。我们应该会再次收到“你好，世界”的信息。

假设一切顺利，我们现在有了一个托管 nodejs 应用程序的功能服务器！

# 倒影

这是很多步骤来设置一切。然而，大部分可以很容易地自动化。甚至可以通过 [AWS 命令行界面](https://aws.amazon.com/cli/)自动提供 AWS 服务。

## 重新部署

要部署我们应用程序的新版本，我们只需要将文件复制到我们的服务器，并重启守护程序。另一个应该很容易自动化的步骤。

## 缺点

应用程序完全按照我们的设置运行。要改变什么，我们真的要非常了解 Linux。我可以通过谷歌搜索基本的命令，但做一些简单的事情似乎是一种痛苦，例如:

*   设置 HTTPS(在 Heroku 上，这几乎是一键设置)
*   转移到另一个服务。(有了 docker 之类的东西，这可能会更容易，我认为我做的很多事情都是针对 Amazon Linux 2 的)。
*   缩放。(类似 docker 的东西会让我的应用程序更容易运行)。

## iptables

我的理解是 iptables 已经被弃用，但是作为 Linux 系统管理的新手，我不清楚有什么可用的替代品。我能找到的许多信息都涉及到我的系统中不存在的命令。

## 下一步

我可能会尝试编写一些脚本来自动化这个过程，以了解在不同的服务器上部署不同的应用程序有多难。我的系统中肯定有一些硬编码的值，我必须处理它们。

总的来说，让它运行起来很有趣。