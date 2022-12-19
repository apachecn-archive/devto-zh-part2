# 如何创建一个新的詹金斯奴隶

> 原文：<https://dev.to/gwllmnn/how-to-create-a-new-jenkins-slave-519b>

[![](../Images/9a80a9bba656a019cd6bb28374c0b41f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ec5NynT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/799/1%2AZmMvZvGsTb1NKCgDaDJFgg.png)

所以你已经设置好了 Jenkins 的主实例，现在你想让 Jenkins 的从实例为你工作？

在能够使用它们之前，需要做以下几件事情:

1.  使用以下内容创建用户 Jenkins:adduser Jenkins
2.  将您的主实例 ssh 公钥添加到文件/home/jenkins/中。ssh/authorized_keys 可以像这个命令一样简单:ssh-copy-id -i ~/。ssh/mykey user@host **记住，主设备应该能够连接到您的从设备，而不需要任何密码。**
3.  安装 java 时使用:apt install default-jre

### 如果你打算使用 Docker:

*   安装对接器([https://docs.docker.com/install/](https://docs.docker.com/install/))

确保 docker 在启动时运行:systemctl enable docker 并将 jenkins 用户添加到 docker 组:user mod -aG docker jenkins

*   如果你也打算使用 docker-compose，请安装它

[![](../Images/cd80f68a6ca6ce96ba8e8c733fe83ab3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xdBHs9eU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/453/1%2AQVFjsW8gyIXeCUJucmK4XA.png)

### 向 Jenkins 的主实例添加一个新节点

进入 Jenkins→管理 Jenkins→管理节点→新建节点，并填写如下不同字段。根据您的需求进行调整:

[![](../Images/69df5b874333638c1daa66104d683401.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S3xyOZYW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AejfW074hGirq9ZWSmeEbQQ.png)

就是这样！

现在，在你的 Jenkinsfile 中使用标签为 php 的代理，并赞美你的新奴隶为你工作:)