# 移除悬挂的 docker 图像和停止的容器。跨平台解决方案。

> 原文：<https://dev.to/melezhik/remove-dangling-docker-images-and-stopped-containerscross-platform-solution-21jh>

我知道有很多解决方案可以解决这个问题，但是如果你需要一个跨平台的解决方案，既适用于 Windows 又适用于 Linux 操作系统，就使用小巧精致的麻雀插件[docker-remove-dangling-images](https://sparrowhub.org/info/docker-remove-dangling-images):

```
$ sparrow plg install docker-remove-dangling-images
$ sparrow plg run docker-remove-dangling-images 
```

Enter fullscreen mode Exit fullscreen mode

* * *

最好的

阿列克谢