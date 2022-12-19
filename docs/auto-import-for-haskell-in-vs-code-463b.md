# VS 代码中 Haskell 的自动导入

> 原文：<https://dev.to/chrismwendt/auto-import-for-haskell-in-vs-code-463b>

今天我在 VS 代码里为 Haskell 黑了一下 auto imports:[https://github.com/chrismwendt/Auto-Import](https://github.com/chrismwendt/Auto-Import)基于[https://github.com/soates/Auto-Import](https://github.com/soates/Auto-Import)(目前只支持 TypeScript)。代码很粗糙，有很大的改进空间。目前，它只在 Hoogle 上搜索变量。

发现导出相同变量名的其他包和模块是很酷的，例如`concurrently`出现在`async`中(这是我所期望的)，但也出现在其他推广`async`类型的库中:

[![screenshot](../Images/74f09035f298cd90d285fd5eaed84bae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I6lHb1U3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/koiteptkrbzq1b44foai.png)