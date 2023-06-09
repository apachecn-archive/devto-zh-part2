# AWS Lambda，但是用 GPU？

> 原文：<https://dev.to/seveibar/aws-lambda-but-with-gpus-3ph3>

许多高级机器学习/深度学习任务需要使用 GPU。你可以在 AWS 等服务上出租这些 GPU，但即使是最便宜的 GPU 也要花费 600 美元/月。[弹性 GPU](https://aws.amazon.com/ec2/elastic-gpus/)有帮助，但只给有限的内存。我不知道你怎么想，但是大多数时间我都在做研究，我想要快速的结果，否则会有大量的空闲时间。

一些提供商似乎不再需要考虑具有云机器学习服务的 GPU([例如 GCP 的云 ML 引擎](https://cloud.google.com/ml-engine/docs/getting-started-training-prediction?hl=en_US))。这对于生产来说很好，但对于培训来说似乎有点麻烦，我不想让[打包我的“培训师应用”](https://cloud.google.com/ml-engine/docs/packaging-trainer)。

我真正想看到的是将本地机器上的上下文(变量和数据)转移到一台具有强大 GPU 的机器上，快速运行，然后将输出放回到我的环境中。

你觉得怎么样？我们是否会开始将 GPU 功能视为研究服务？