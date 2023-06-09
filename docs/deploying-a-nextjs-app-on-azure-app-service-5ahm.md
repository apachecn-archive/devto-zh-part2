# 在 Azure 应用服务上部署 NextJS 应用

> 原文：<https://dev.to/codeprototype/deploying-a-nextjs-app-on-azure-app-service-5ahm>

从我的经验来看，在 Azure App Service 上部署带有 NodeJS 的 ReactJS 是没有问题的。在 DigitalOcean droplet 上部署 NextJS 也没有问题。但是 Azure 部署上的 NextJS 导致了这个错误:

`npm ERR! 404 'zeit/next-css' is not in the npm registry.`

还有许多奇怪的症状，很难描述，需要很长时间，读起来也很无聊。

例如，如果我单击“重新部署”,将会显示此消息，而不是之前的错误消息。这可能只是一件艺术品。

[![NextJS on Azure](img/8331e44106db47d33132be2adf20cf1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D-Pjd9Rr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbk64c7ziffa51kjjla4.png)

长话短说，如果你处理过这种情况，你的体验如何？