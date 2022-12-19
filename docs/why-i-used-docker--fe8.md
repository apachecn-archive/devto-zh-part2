# 我为什么用 Docker？

> 原文：<https://dev.to/k4ml/why-i-used-docker--fe8>

在我的私人电报群里出现了一个关于我们为什么使用 Docker 的话题，所以我将在这里贴回一些答案。

AC:为什么是 Docker？(因为 docker 只是一个额外的复杂性层，只有当你大规模地重复做很多事情时才有帮助。否则只是一个磁盘空间 sink)。你想对 docker 做什么？

AS:可能是“仅仅因为”男生...我把我的 vim 做成 dockerized，这样从我使用的任何 pc 上，我都可以拥有相同的插件和一切。比如 60 MB 的压缩图像和 130 MB 的未压缩图像。但那是因为我用巨大的 flag 编译了 vim，并加入了 python perl lua 和 ruby，否则它会小得多。我的带有大功能标志的基本 vim 只有 30MB 未压缩。

AC:之所以“为什么是 Docker？”是为了确保你能看到容器化你的用例的好处。否则只会更加痛苦。

https://alysivji.github.io/php-mysql-docker-containers.html 已经是一个好的开始。(他用 Docker 是因为他不想为了运行/测试 wordpress 而安装 PHP-7.2)。

k:我们是 python 商店，所以没有合适的 php 环境。但是有时客户会带着他们的 php 脚本来寻求帮助，所以 docker 是最容易快速测试脚本的。