# Docker 构建代理设置 docker 文件构建的代理

> 原文：<https://dev.to/zyfa/setup-the-proxy-for-dockerfile-building--4jc8>

如果我们只需要拉或推 docker 映像，通过系统服务配置文件设置代理就可以了。但是这个代理设置在构建 Dockerfile 时似乎不起作用。我们已经尝试修改/etc/default/docker 配置，但这似乎也不起作用。通过看 StackOverflow 的很多回答，我们终于找到了设置代理的正确设置。

`docker build --build-arg http_proxy=http://10.239.4.80:913 --build-arg https_proxy=http://10.239.4.80:913 .`

10.239.4.80:913 是我公司的代理地址，您可能需要根据您公司的代理地址和端口进行更改。

您也可以在 Dockerfile 文件中设置代理。

`ENV http_proxy 10.239.4.80:913`

但我认为不推荐这种方法，因为最终用户可能直接连接到互联网，而不是通过代理。