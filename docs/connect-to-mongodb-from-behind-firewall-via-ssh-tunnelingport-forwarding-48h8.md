# 通过 SSH 隧道/端口转发从防火墙后面连接到 MongoDB

> 原文：<https://dev.to/codeprototype/connect-to-mongodb-from-behind-firewall-via-ssh-tunnelingport-forwarding-48h8>

当 NodeJS 应用程序被部署到 Azure、Heroku 或其他一些云服务时，应用程序成功连接到同样位于云上的 MongoDB 实例应该没有问题。但是，如果我们位于公司防火墙之后，可能会出现以下问题:

1.  我们想用 Studio3T 这样的工具做 MongoDB，但是 MongoDB 端口被屏蔽了。
2.  无论出于什么原因，我们都不想在本地主机上安装 MongoDB 实例来进行开发。相反，我们希望连接到一个也在云上的测试 MongoDB 实例。

有些公司不会为美国开发者在防火墙上打开 27017 端口。我们可以通过使用 SSH 隧道(也称为 SHH 端口转发)来解决这个防火墙问题。

首先，注册最基本的(最便宜的)云副总裁，如 https://www.linode.com 的[或 https://www.digitalocean.com 的](https://www.linode.com)[的](https://www.digitalocean.com)

有两种方法可以认证 VPS 实例(DigitalOcean 称 VPS 实例为 droplet 我在本文后面将其称为 SSH 服务器):(I)用户名/密码，这是典型的(ii)公钥/私钥对

在本文中，我们主要关注后一种“公钥/私钥 RSA 密钥对”。

如果你使用 Mac/Linux，请遵循本[指南](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets);如果你使用 Windows，请遵循本指南[。](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-putty-on-digitalocean-droplets-windows-users)

如果您在多台计算机上进行开发工作(例如在工作时使用 Windows10，在家中使用 Mac ),或者如果您与其他开发人员一起进行一个项目，对于生成的每个公钥/私钥对，请将公钥上载到 VPS 实例。

为了在多台计算机和多个开发人员之间拥有相同的代码库，我们希望 NodeJS 代码引用同一目录中的私钥文件。我假设我们选择 NodeJS 的根目录。在 Windows 上，私钥由。ppk 扩展。我们只需复制到 NodeJS 项目的根目录，并将其命名为 *id_rsa.ppk* 。比如:

```
$ cd [root NodeJS directory]
$ cp ~/.ssh/id_rsa id_rsa.ppk 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记将该文件包含在*中。gitignore* 这样他们就不会提交 git 了。比如说*的内容。gitignore* 可能是这样的:

```
node_modules/
id_rsa.ppk 
```

Enter fullscreen mode Exit fullscreen mode

## 连接 Studio3T

Studio3T 是一个很好的工具，可以用于您的测试或生产 MongoDB。启动 Studio3T 后，使用类似的设置创建一个新连接，如下所示:

#### 服务器选项卡:

[![Studio3T server tab](img/fbbbd6824b625d61092e599d0c7d293e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P8fyB9QT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y55vqeuhfqi6slnwpbkh.png)

#### 认证标签页:

[![Studio3T authentication tab](img/e57a464755a3f100a8495428273b8f6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P5A5jVuv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35t7dazoxlvxlerjbl11.png)

#### SSH 隧道标签:

[![Studio3T SSH tunnel tab](img/fb0dacd3dd22b84be79c18e401c6b1d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DgWA22g9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n71flh1zt0tkiyohy6oo.png)

如果在创建公钥/私钥对时没有密码，请选中“我的私钥不受密码保护”复选框。

## NodeJS 程序化

在开发过程中，我们必须在公司防火墙后面的本地机器上运行 NodeJS 应用程序。因为运行在云上的 MongoDB 通常监听被防火墙阻塞的端口，所以我们必须像上面那样使用 SSH 隧道/端口转发技术来更改代码。

首先，引入必要的 npm 包:

```
npm i --save tunnel-ssh fs 
```

Enter fullscreen mode Exit fullscreen mode

然后对代码进行如下修改:

```
const mongoose = require('mongoose');
const tunnel = require('tunnel-ssh');
const dev = process.env.NODE_ENV !== 'production';

if (dev) {
  const sshTunnelConfig = {
    agent: process.env.SSH_AUTH_SOCK,
    username: 'kevin',
    privateKey: require('fs').readFileSync('./id_rsa.ppk'),
    host: 'xx.yyy.zz.xyz', //IP adress of VPS which is the SSH server
    port: 22,
    dstHost: 'your-test-instance.mlab.com',
    dstPort: 31568, //or 27017 or something like that
    localHost: '127.0.0.1',
    localPort: 50001 //or anything else unused you want
  };

  tunnel(sshTunnelConfig, (error, server) => {
    if(error) {
        console.log("SSH connection error: ", error);
    }

  mongoose.connect(`mongodb://${Constants.DEV_DB_USERNAME}:${Constants.DEV_DB_PASSWORD}@127.0.0.1:50001/${some_db_name}`);
 //important from above line is the part 127.0.0.1:50001 
  });
} else {
  mongoose.connect('your-production-instance-uri'); //normal from before
} 
```

Enter fullscreen mode Exit fullscreen mode