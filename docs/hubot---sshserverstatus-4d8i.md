# Hubot - SSH 服务器状态

> 原文：<https://dev.to/chintukarthi/hubot---sshserverstatus-4d8i>

你好。我正在写一个 coffee 脚本来检查服务器状态。它是用咖啡脚本为 hubot 写的。从 child_process 函数中获取参数时，我遇到了一些错误。为了理解，我粘贴下面的代码。

代码:

child _ process = require ' child _ process '
module . exports =(机器人)- >
机器人.响应/检查状态/i，(res) - >

```
child_process.exec("sshpass -p 'password' ssh username@hostname 'sudo service httpd status'", (err, stdout, stderr) ->
  if err
    console.log(err)
  else
    console.log(stdout)
) 
```

Enter fullscreen mode Exit fullscreen mode

代码解释:

这(child_process)是一个 nodejs 模块，我正在尝试执行以下任务:

使用 ssh shell 命令，我登录到服务器(ssh username@hostname)，然后编写 shell 命令来检查服务器的状态(sudo 服务 httpd 状态)。

错误:响应不正常:no_text

我认为 child_process.exec()语法有问题。

我在虚拟机中使用 centos。我已经将 ssh 公钥保存在。ssh/authorize_keys

谢了。