# Now.sh docker 图像大小限制

> 原文：<https://dev.to/hitochan777/nowsh-docker-image-size-limitation-59j6>

有人知道 now.sh 有没有限制 docker 图片的大小？我未能部署图像，可能是因为下面日志中看到的模仿。
[![now.sh log](img/053abde3115077a66c3eeee590ccd542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XgR1nQpK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j8cfvxz3dxymyhjuso19.png)

直到几天前，我已经能够部署几乎和我今天未能部署的图像一样大的图像。

更新:2018 . 8 . 7
我在 now.sh slack 频道问了这个问题，他们建议我试试 MSB(多阶段构建)。MSB 基本上允许您将构建的应用程序从一个阶段复制到另一个阶段，这样后一个阶段只包含应用程序，而不包含构建它所必需的库或包。