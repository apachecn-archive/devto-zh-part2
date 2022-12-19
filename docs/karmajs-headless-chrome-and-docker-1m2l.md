# Karma.js，无头 Chrome 和 Docker

> 原文：<https://dev.to/gimlet2/karmajs-headless-chrome-and-docker-1m2l>

今天我回到了我参与的前端项目。我不知道在我离开的时候发生了什么，但是我们的测试停止了。

这个项目是用 **Vue.js** 完成的，由于某种原因，不可能将插槽作为字符串传递给组件。好吧。并且有错误信息建议使用**木偶师**代替。

这里有一小段是关于如何做到这一点的。快速通知，我在 Docker 容器内运行我的测试…

1.  从你的**包中移除**幻象****
2.  给**木偶师**和 **karma-chrome-launcher** 添加依赖

3.  根据下面的片段更新 **karma.conf.js**