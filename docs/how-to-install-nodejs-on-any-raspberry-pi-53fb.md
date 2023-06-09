# 如何在任何树莓 Pi 上安装 Node.js

> 原文：<https://dev.to/wiaio/how-to-install-nodejs-on-any-raspberry-pi-53fb>

[![alt text](img/88f1cf7fc574b5efcda8567fe7e5e59b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NpohKnsq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-07-03/1530621440-312950-blog-nodejs-9touvegvpm.jpeg) 
大家好，
这里有一个快速教程，告诉你如何在任何树莓 Pi 上安装 Node.js。当登录到您的 Pi(通过 SSH 或桌面)时，依次运行以下 5 个命令:

```
cd ~
wget https://nodejs.org/dist/v4.3.2/node-v4.3.2-linux-$(uname -m).tar.gz 
tar -xvf node-v4.3.2-linux-$(uname -m).tar.gz
cd node-v4.3.2-linux-$(uname -m)
sudo cp -R * /usr/local/ 
```

Enter fullscreen mode Exit fullscreen mode

要检查 Node.js 是否安装正确，运行`node -v`。它应该返回`v4.3.2`。运行`npm -v`来检查 NPM(节点包管理器)是否也被安装了。