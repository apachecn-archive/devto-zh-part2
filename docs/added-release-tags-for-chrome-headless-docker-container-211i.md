# 添加了 chrome 无头 docker 容器的发布标签

> 原文：<https://dev.to/justinribeiro/added-release-tags-for-chrome-headless-docker-container-211i>

通过从 DockerHub 获取超过 314K 的数据，并在多次请求后，我终于开始为 [chrome-headless](https://hub.docker.com/r/justinribeiro/chrome-headless/) 创建单独的标签，这些标签针对 chrome 的每个频道(linux 的 stable、beta 和 dev)。

除了添加新的发布标签，我还对 docker 图像做了一些小的改进:

1.  增加了额外的字体支持，允许符号在 Chrome 中正确显示
2.  清理了一些 GPU 缓存问题
3.  更新到 debian:stretch-slim

图像大小目前大约在 280MB 左右，虽然我有一个更小的版本，但我还没准备好合并它，直到我有时间进一步测试它。与此同时，享受新的更新，继续使用 Chrome-headless！