# gitlab runner 错误:没有这样的图像

> 原文：<https://dev.to/c33s/gitlab-runner-error-no-such-image-1gjj>

如果您运行自己的 gitlab 运行程序，并得到错误消息`ERROR: Preparation failed: Error: No such image: mariadb:10.1 (executor_docker.go:175:0s)`，这可能有多种原因。

有时候解决方法真的很简单。在我的例子中，错误信息并没有真正的帮助，但是解决方法很快就找到了。**服务器耗尽了硬盘空间**，如果你像我一样使用带有额外小分区的 lvm，就会发生这种情况。

```
ERROR: Preparation failed: Error: No such image: mariadb:10.1 (executor_docker.go:175:0s)
Will be retried in 3s ...
Using Docker executor with image c33s/php:7.1 ...
Starting service mariadb:10.1 ...
Pulling docker image mariadb:10.1 ...
ERROR: Preparation failed: Error: No such image: mariadb:10.1 (executor_docker.go:175:0s)
Will be retried in 3s ...
Using Docker executor with image c33s/php:7.1 ...
Starting service mariadb:10.1 ...
Pulling docker image mariadb:10.1 ...
ERROR: Preparation failed: Error: No such image: mariadb:10.1 (executor_docker.go:175:0s)
Will be retried in 3s ...
ERROR: Job failed (system failure): Error: No such image: mariadb:10.1 (executor_docker.go:175:0s) 
```

链接:

*   维多利亚·戈达的封面图片[https://www . pexels . com/photo/blue-red-and-yellow-chalk-1107495/](https://www.pexels.com/photo/blue-red-and-yellow-chalk-1107495/)