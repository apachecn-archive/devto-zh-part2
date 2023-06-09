# 使用 NodeJS 的 Google 云引擎的一些缺陷

> 原文：<https://dev.to/seraphicrav/some-pitfalls-on-google-cloud-engine-with-nodejs-5hao>

我一直在做一些基于 Typescript/NodeJS 的 API 作为一个附带项目，这个周末我决定将它部署在 Google Cloud Engine Standard for NodeJS 上，认为这将是快速而简单的。我错了。

使用 GCE 标准的主要好处是，它是完全管理的，因此不需要维护，而且它还可以从 0 开始扩展，因此不需要流量，也不需要付费。的不好的一面是你对运行应用程序的机器控制不好，像 ls、cd 甚至 npm 这样的命令甚至不可用。当部署应用程序时，Google 在生产模式下为您调用 npm install(因此不安装 devDependencies ),以便为您将要运行的架构获取正确的依赖项。这就是事情变得对我不利的地方。

我的应用程序非常标准，用 Typescript 编写，但是依赖于 Gitlab 上的私有存储库。在使用 git+ssh 进行本地开发之后，我尝试以这种方式进行部署。它不起作用。没有办法提供 npm 用 ssh 访问代码所需的凭证，甚至环境变量 GIT_SSH_COMMAND 也不起作用(好像被忽略了)。见鬼，连 npm install 都不存在，日志都在说 npm_install！3 天来，我一直试图寻找另一个解决方案，但没有成功，用 git+ssh。

然后，我尝试走 git+https 之路，在 URL 中嵌入令牌，这是应该走的路，我不喜欢在 package.json 中为只读访问添加凭证，但是找不到更好的方法。

好了，现在，部署成功！从我的 API 调用一个随机方法，有问题...经过多日的反复试验，我终于成功安装了一个依赖项，现在我正在想念它。太好了！问题是我的依赖也是用 Typescript 写的，我在 npm 脚本预安装中用 tsc 传输它，这似乎被忽略了。令人沮丧的是，我通过提交 dist 文件夹走了一条肮脏的路，现在它工作了。

为了让事情更干净，我正在考虑用 GitlabCI 的 transpiled 代码制作一个 tarball，并把它作为一个构建制品。还在想别的办法。

道路是漫长的，我找不到关于它的文档(只有关于在 npm 上有一个私有存储库的事情，所以为这个特性支付 7 美元/月/用户)。最后，我还是设法做了我想做的事情，在 Google Cloud Engine Standard for NodeJS 上部署了我的应用程序。希望这些笔记能帮助到一些人。